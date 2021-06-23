---
layout: post
---

```go
package main

type any interface{}

func (a any) A() {
}
```

定义func的这一行编译报错：invalid receiver any(pointer or interface type)

pointer和interface是不能作为函数的接收者的。

在c++里，调用一个对象的函数时，发生的事情是：向这个对象发送一个信号或消息，这个信号或消息代表了执行这个函数。

pointer和interface都不是对象，它们不能接收信号。因此把any定义成interface{}空接口是没有意义的，除非我们想往里面添加函数，那么any就不再是空接口。如果我们想要使用空接口，那么直接用interface{}就好了，不用再去定义any。

```go
type Int int

func (i Int) A() {
}
```

这个例子和上面的不同。这里的Int和int还是有区别的，Int的对象可以执行A函数。

空接口的目的是为了使我们在声明函数时处理未知类型的参数。在函数里如何把未知类型的参数还原回原来的值？如下：

```go
func f(a interface{}) {
	println(a.(int))
}
```

这个概念叫做type assertion。
