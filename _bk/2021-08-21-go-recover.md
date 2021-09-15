---
layout: post
---

```go
package main

import "fmt"

func f() {
	defer func() {
		if p := recover(); p != nil {
			fmt.Printf("internal error %v\n", p)
		}
	}()
	panic("shit")
}

func main() {
	f()

	fmt.Println("done")
}
```

如果没有recover，执行不到done。

在defer里调用recover()就足够从panic恢复出来了，不过获取它的返回值更便于打印错误信息。
