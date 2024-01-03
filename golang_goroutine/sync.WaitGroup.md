> https://zhuanlan.zhihu.com/p/344973865

#### 1. automic
[[automic函数]]

#### 2.基本用法
通过add添加需要等待的个数，然后使用done来通知完成的个数。使用wait来等待所有的goroutine完成

#### 3. 具体流程

1. 当调用 `WaitGroup.Add(n)` 时，计数器 `counter` 会自增，表示要等待的任务数量增加了 `n` 个。
    
2. 当调用 `WaitGroup.Wait()` 时，`waiter` 计数会增加，表示有一个 goroutine 正在等待 `counter` 降为零。然后，调用 `runtime_Semacquire(semap)` 会尝试获取信号量，如果 `counter` 仍然不为零，当前 goroutine 就会被挂起。这意味着这个 goroutine 将被阻塞，直到 `counter` 降为零。
    
3. 当调用 `WaitGroup.Done()` 时，`counter` 会减少，表示有一个任务完成了。如果 `counter` 减少后为零，说明所有任务都已完成。此时，调用 `runtime_Semrelease` 会释放信号量，唤醒正在等待的 goroutine。被唤醒的 goroutine 将继续执行，完成整个等待过程。