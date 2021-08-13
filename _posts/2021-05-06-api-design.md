---
layout: post
title:  "api设计"
---

[Best practices for REST API design](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)

要点:
1. 路径由名词组成
2. 支持过滤、排序、分页
3. 可以在响应的json中返回路径，让有需要的客户端再发起http请求，避免路径名过长
4. api版本化

## Example

```
POST /api/users/
POST /api/users/login
GET  /api/user/                   retrieve
PUT  /api/user/                   update

GET    /api/profiles/:username               retrieve
POST   /api/profiles/:username/follow
DELETE /api/profiles/:username/follow

Articles:

POST /api/articles/                     create
PUT  /api/articles/:slug                update
DELETE  /api/articles/:slug
POST    /api/articles/:slug/favorite
DELETE  /api/articles/:slug/favorite
POST    /api/articles/:slug/comments
DELETE  /api/articles/:slug/comments

ArticlesAnonymous:

GET /api/articles/
GET /api/articles/:slug
GET /api/articles/:slug/comments

GET /api/tags/
GET /api/ping
```

groups:
1. users
2. user
3. articles
4. profiles
5. tags

思考:
1. 为何区分user和users group？
2. 登录和注册的逻辑。
3. 权限控制。

权限控制通过添加middleware控制，作用于group里的所有接口。

## CRUD: C

无论是注册用户还是创建其他记录，逻辑基本上是：
1. validate input data
2. save
3. return

在校验数据这一步中，如果校验通过，就要把校验结果用作创建临时的model object。在save步骤里，直接把model object存入数据库。然后return json response。

因此在第一步的校验数据过程，其目的是绑定数据。gin里有一个binding package，可以用它很方便地实现数据校验。

框架应该支持处理多种方式的request input，如GET请求的Form、POST请求的Form、POST请求的json/xml等。框架的做法是，通过http method和content type判断。

struct的命名，用UserModel优于User。虽然名字长了一点。但是更concise。form和data是两个不同的model。

### model

Form model, or generally speaking, input model is different from data model.

input可以是form、json或其它格式。input model对数据进行一定的校验和处理之后才可以入库。比如input包含password字段，但真正入库的字段是password hash。

所以input和data两个model分开。前者负责校验，后者负责数据库数据。

binding的逻辑在input model上面进行。数据bind到input model上，处理之后再赋值到data model上。

gin binding不管用户提供的是input model还是data model。它只负责校验和binding。得到binding之后，应用逻辑应该把input model赋值到data model。把这两部分逻辑合并成一个更abstract的binding，才是应用层想要调用的。

应用层得到的是：
1. 校验通过，data model数据填充好
2. 校验失败，错误详情（string或者map的方式）

在这之后应用层就把这个data model入库了。

### Save

database handle使用单例模式。无论在哪个package中操作数据库都用的同一个handle。

在common package定义一个DB全局变量就可以了，要使用时，用GetDB()函数获取。

使用数据库之前：
1. Init：负责连接数据库
2. Migrate：负责创建数据库表

### DB connections

数据库使用要考虑连接数和关闭问题。

### 识别码

防止恶意请求注册。

### response

注册或创建记录成功后，返回的不应该是整个model的数据，而应该有所调整，比如返回token而非password。

而这个响应的json，应该是另外一个结构体了。

三个struct：
1. 请求
2. 数据库
3. 响应

### gorm.Model

### Token

go jwt lib: github.com/golang-jwt/jwt

#### 生成token

```go
	jwt_token := jwt.New(jwt.GetSigningMethod("HS256"))
	jwt_token.Claims = jwt.MapClaims{
		"id":  id,
		"exp": time.Now().Add(time.Hour * 24).Unix(),
	}
	token, _ := jwt_token.SignedString([]byte(SecretPassword))
```

jwt包含JOSE Header、claims set和signature。

这里的id是private claim，只是用于传达id这个信息。而jwt lib本身应该去思想registered claims如exp的逻辑。claims部分是我们需要填充的。

签名需要指定算法和密钥。

#### 校验token

package: github.com/golang-jwt/jwt/request

```go
		token, err := request.ParseFromRequest(c.Request, MyAuth2Extractor, func(token *jwt.Token) (interface{}, error) {
			b := ([]byte(common.SecretPassword))
			return b, nil
		})
        
		if err != nil {
			c.AbortWithError(http.StatusUnauthorized, err)
			return
		}

		if claims, ok := token.Claims.(jwt.MapClaims); ok && token.Valid {
			myUserId := uint(claims["id"].(float64))
			UpdateContextUserModel(c, myUserId)
		}
```

面去我们手工从http header中抽取token的过程，jwt的request package帮我们完成。如果没有拿到token或者token有问题，那么都体现在返回的error中。

得到token之后，先将token claims转化为map。再从claims中获取user id，更新上下文（c.Set）。

token这种使用场景，就是典型的middleware使用场景。很多请求都需要在通过这个middleware的验证之后，才允许进行下一步操作。

除了提供自定义的MyAuth2Extractor之外，request.ParseFromRequest还需要另外一个回调函数来给它提供密钥。

#### 截取token

为了支持多种传递token的方式，request.ParseFromRequest需要我们写一个extractor。extractor提供了比回调函数更为通用的抽象。

request.ParseFromRequest要求extractor实现接口就可以。所以我们既可以采用HeaderExtractor也可以采用ArgumentExtractor，还可以采用MultiExtractor。

ArgumentExtractor比较简单，可以指定多个GET参数作为key或者POST Form参数。

HeaderExtractor类似于ArgumentExtractor。

还有一个PostExtractionFilter。它有两个步骤：
1. 先用ArgumentExtractor或HeaderExtractor获取整个token
2. 再对token做一定的处理（调用自定义的filter函数）

MultiExtractor则是，当我们需要支持各种花样的extractor时可以用到。


### 登录验证

password hash生成过程：

```go
	bytePassword := []byte(password)
	passwordHash, _ := bcrypt.GenerateFromPassword(bytePassword, bcrypt.DefaultCost)
	u.PasswordHash = string(passwordHash)
```

1. 真实密码转成[]byte
2. 利用bcrypt计算出password hash

bcrypt提供的GenerateFromPassword满足了这个场景。

比对密码过程：

```go
	bytePassword := []byte(password)
	byteHashedPassword := []byte(u.PasswordHash)
	return bcrypt.CompareHashAndPassword(byteHashedPassword, bytePassword)
```

相应地还是将密码明文和密码哈希转成[]byte，再用bcrypt的CompareHashAndPassword函数来比对。


## CURD: U

更新信息时提交的请求是部分信息。如果继续采用Create时用的那个validator就不合适了。使用那个validator就会报错比如：缺少字段。

有个技巧，复用validator。那就是在validate Bind之前先用context里的数据进行填充。

但还有一个问题。当我们用context中的model来填充validator里的input model时，我们无法去填充password这样的字段。因为数据库里存的就是hash。这时我们把它设置成一个随机值。直到我们要更新记录时，我们判断看到validator里的password是随机值，那就说明用户并没有修改密码。

更新完数据库之后，context也要记得更新。

token里的claims包含的是id信息，所以即使我们修改了user信息，token还是可以继续使用的。

### gorm的使用

update:

```go
	db := common.GetDB()
	err := db.Model(u).Updates(data).Error
```

u和data都是model，这里是用data去更新u。

create:

```go
	db := common.GetDB()
	err := db.Create(data).Error
	return err
```

我们拿着err，就知道执行成功与否了。
