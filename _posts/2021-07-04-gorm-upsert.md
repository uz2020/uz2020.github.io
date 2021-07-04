---
layout: post
---

总算解决这个upsert问题了。原来gorm就提供了很简单的用法。

```go
import (
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"gorm.io/gorm/clause"
)

type product struct {
	Name   string `gorm:"primaryKey"`
	Price  float64
	Amount int
}


	db.Clauses(clause.OnConflict{
		UpdateAll: true,
	}).Create(&product{"p1", 18, 5})

```

当它发现key constraint violations时，就会变成一条update的语句。clause.OnConflict可以指定冲突的column，如果不指定，那就是primary key。UpdateAll则表示当发生冲突时更新的策略是全部columns更新（除了primary key），还是部分columns更新。

有了这个upsert，就可以把插入记录和更新记录统一起来了。之前我还以为很难实现。

那么这个upsert的底层原理是什么？数据库支持这种机制，还是gorm帮我们执行了多一条select语句来判断是否冲突？有待研究。
