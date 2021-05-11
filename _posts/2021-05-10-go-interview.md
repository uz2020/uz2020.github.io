---
layout: post
title:  "Go面试题"
---

1. new和make的区别

    **new**: 
    1. The new built-in function allocates memory. The first
    argument is a type, not a value.
    2. The value returned is a **pointer** to a newly allocated zero value
    of that type.

    **make**:
    1. The make built-in function allocates and initializes an object of type
        slice, map, or chan (only). 
        
    2. Like new, the first argument is a type, not a value. Unlike
        new, make's return type is the same as the type of its
        argument, **not a pointer** to it. 
        
    3. The specification of the result depends on the type: 
       1. **Slice**: The size specifies the length. The capacity of the
        slice is equal to its length. A second integer argument may be
        provided to specify a different capacity; it must be no
        smaller than the length. For example, make([]int, 0, 10)
        allocates an underlying array of size 10 and returns a slice
        of length 0 and capacity 10 that is backed by this underlying
        array.
        
       2. **Map**: An empty map is allocated with enough space to
        hold the specified number of elements. The size may be
        omitted, in which case a small starting size is allocated.
        
       3. **Channel**: The channel's buffer is initialized with the
        specified buffer capacity. If zero, or the size is omitted,
        the channel is unbuffered.

    
2. 进程、线程、协程的区别

    The differences between threads and goroutines are essentially quantitative, not qualitative.
    
    **Stack**: 
    1. Each OS thread has a fixed-size block of memory (often as large
        as 2MB) for its stack, the work area where it saves the local
        variables of function calls that are in progress or temporarily
        suspended while another function is called.
    
    2. A goroutine’s stack, like the stack of an OS thread, holds the local
        variables of active and suspended function calls, but unlike an OS
        thread, a goroutine’s stack is not fixed; it grows and shrinks as
        needed.
    
    **Scheduling**: 
    1. OS threads are scheduled by the OS kernel. Every few
        milliseconds, a hardware timer interrupts the processor, which
        causes a kernel function called the scheduler to be invoked. This
        function suspends the currently executing thread and saves its
        registers in memory, looks over the list of threads and decides
        which one should run next, restores that thread’s registers from
        memory, then resumes the execution of that thread. Because OS
        threads are scheduled by the kernel, passing control from one
        thread to another requires a full context switch.
        
    2. The Go runtime contains its own scheduler that uses a technique
        known as m:n scheduling, because it multiplexes (or schedules) m
        goroutines on n OS threads.
        
        Unlike the operating system’s thread scheduler, the Go scheduler is not invoked periodically
        by a hardware timer, but implicitly by certain Go language constructs. For example, when a
        goroutine calls time.Sleep or blocks in a channel or mutex operation, the scheduler puts it to
        sleep and runs another goroutine until it is time to wake the first one up. Because it doesn’t
        need a switch to kernel context, rescheduling a goroutine is much cheaper than rescheduling a
        thread.
        
    **identity**:
    1. In most operating systems and programming languages that
        support multithreading, the cur- rent thread has a distinct identity
        that can be easily obtained as an ordinary value, typically an integer
        or pointer. This makes it easy to build an abstraction called
        thread-local storage, which is essentially a global map keyed by
        thread identity, so that each thread can store and retrieve values
        independent of other threads.
        
    2. Goroutines have no notion of identity that is accessible to the
        programmer. This is by design, since thread-local storage tends to be
        abused.
        
        Just as with programs that rely excessively on global
        variables, this can lead to an unhealthy ‘‘action at a distance’’ in
        which the behavior of a function is not determined by its arguments
        alone, but by the identity of the thread in which it runs.
    
3. 有几种锁?

   Mutex和RWMutex。
   
   一个协程获得Mutex以后，其它协程需要等待这个协程释放Mutex。
   
   而RWMutex用于单写多读。在读锁（调用RLock()）占用的情况下，会阻止写，
   但不会阻止读。而写锁（Lock()）会阻止任何其他协程（无论读和写）进来。
   
4. 如何限制协程数量?
5. 数组和切片的不同?
6. 数组和切片传参方式? 传值还是指针?
7. RWMutex的实现?
8. buffered channel和unbuffered channel的区别?
9. 什么是并发? 什么是并发安全?

    When we cannot confidently say that one event happens before the
    other, then the events x and y are **concurrent**.
    
    Consider a function that works correctly in a sequential
    program. That function is **concurrency-safe** if it continues to
    work correctly even when called concurrently, that is, from two or
    more goroutines with no additional synchronization.

    We can generalize this notion to a set of collaborating functions,
    such as the methods and operations of a particular type. A
    **type** is concurrency-safe if all its accessible methods and
    operations are concurrency-safe.
