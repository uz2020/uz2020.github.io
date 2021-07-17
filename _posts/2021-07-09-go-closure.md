---
layout: post
---

在读goapi时，读到这段代码：

```go
	walkers := make([]*Walker, len(contexts))
	var wg sync.WaitGroup
	for i, context := range contexts {
		i, context := i, context
		wg.Add(1)
		go func() {
			defer wg.Done()
			walkers[i] = NewWalker(context, filepath.Join(build.Default.GOROOT, "src"))
		}()
	}
	wg.Wait()
```

这一行特别醒目：

```go
		i, context := i, context
```

百思不得其解，去irc询问，得知原来这一行的作用是为了避免racy。假设不写这一行，那么在goroutine被调度时，它读取的i、context是什么呢？

This is called the variable capture problem. It doesn't necessarily have to be related to goroutines. The problem could also occur with anonymous functions.

The problem could be easily understood if we take the compiler author's stand. A variable is a piece of storage. There's no need to allocate a new piece of storage for i and context in each iteration. In the for loop, the value of the variable just gets changed, and the memory locations of them stay the same. With the assignment above, a pair of i and context variables get allocated (new memory locations). And they shadow the i and context variables created by the for initializer.

When the goroutines refer to these variables, it refers to different pieces of storage. If we don't do the assignment as above, they all refer to the same pieces of storage which are allocated by the for initializer, and that not only causes incorrect results but also raises race problems.
