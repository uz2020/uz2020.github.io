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

