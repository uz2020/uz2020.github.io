---
layout: post
---


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


