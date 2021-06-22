---
layout: post
title:  "sqlboiler"
---

把数据放进nsq之后，就要开始处理然后存到数据库。Gorm并不能满足我的要求，因为我的数据库表已经建好，我需要的工具是能够从数据库表导出到go struct。而sqlboiler做的正是这个事。

sqlboiler怎么玩呢？

首先要创建一个配置文件：sqlboiler.toml，内容如下：

```
output  = "models"
wipe    = true
no-test = true

[mysql]
  dbname    = "cross_depart"
  host      = "127.0.0.1"
  port      = 3306
  user      = "root"
  pass      = "1"
  sslmode   = "false"
  blacklist = ["schema_migrations"]
```

output表示把自动生成的go代码写入到models目录。wipe表示清空已存在的文件（如果重复操作的话）。这里配置的是mysql的情况，postgresql的配置参考官网。

然后要安装sqlboiler工具。

```bash
# Go 1.16 and above:
go install github.com/volatiletech/sqlboiler/v4@latest
go install github.com/volatiletech/sqlboiler/v4/drivers/sqlboiler-psql@latest
go install github.com/volatiletech/sqlboiler/v4/drivers/sqlboiler-mysql@latest
```

然后执行命令：

```bash
sqlboiler mysql
```

这样就会在models目录生成很多go文件。怎么使用这些go文件？

首先这些go代码都属于package models的。要导入这个package。

```go
import (
	"t/models"
)
```

我当前的项目模块名叫t，所以导入的路径就是“模块/包路径”即"t/models"。

完整的代码实例如下：

```bash
import (
	"context"
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
	"github.com/volatiletech/null/v8"
	"github.com/volatiletech/sqlboiler/v4/boil"
	"log"
	"t/models"
)

func main() {
	db, err := sql.Open("mysql", "root:1@/cross_depart")
	if err != nil {
		log.Fatalf("err: %s", err)
		return
	}
	boil.SetDB(db)
	a := models.TData主机{}
	orgId := null.NewInt64(777, true)
	a.OrgID = orgId

	log.Println(a)

	ctx := context.Background()
	err = a.Insert(ctx, db, boil.Infer())

	if err != nil {
		log.Fatalf("insert err: %s", err)
	}
}
```

1. 使用内置包database/sql来创建数据库连接。
2. 创建结构体对象。
3. null数据类型。
4. 创建context。
5. 插入记录。

## orm不应该这样发展

当sqlboiler生成的代码不能满足我们的需求时，我们不该直接去修改生成的代码，这终究只是会徒劳。因为它不能适应业务的变化。

我看到sqlboiler正在变得很复杂，gorm也是。我认为优秀的设计不应该是复杂的，复杂就一定会出错。
