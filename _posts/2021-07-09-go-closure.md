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
