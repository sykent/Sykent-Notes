# kotlin协程和Flow异步流的应用学习和研究

## 协程
### 协程特点
协程特点：轻量，内存泄漏更少，内置取消支持，Jetpack 集成。

### 协程要素

1. 作用域 CoroutineScope：协程都必须在一个作用域内运行。一个 CoroutineScope 管理一个或多个相关的协程。CoroutineScope：会跟踪使用 launch 或 async 创建的所有协程，可用scope.cancel() 取消。Android 里面有 viewModelScope、lifecycleScope
2. 调度程序 Dispatchers：指定协程在何处运行协程。
* Dispatchers.Main：主线程，用于与界面交互和执行快速工作。
* Dispatchers.IO: 适合在主线程之外执行磁盘或网络I/O。
* Dispatchers.Default:适合在主线程之外执行占用大量CPU资源工作。
例如：对列表进行排序或解析JSON。

```
viewModelScope.launch(Dispatchers.IO) {}
```
3. 挂起函数：suspend 前缀修饰的函数，suspend 其实只是一个标志，只能被协程代码块调用。
4. 作业: Job 是协程的句柄,使用 launch 或 async 创建的每个协程都会返回一个 Job 实例。
5. CoroutineContext 使用以下元素集定义协程的行为：
Job：控制协程的生命周期。
CoroutineDispatcher：将工作分派到适当的线程。
CoroutineName：协程的名称，可用于调试。
CoroutineExceptionHandler：处理未捕获的异常
6. withContext() 代码块，具备把工作切到别的线程，运行完毕后切回来功能。

### 总结
协程由 launch/async 在作用域 CoroutineScope 中启动，调用挂起函数，则会挂起协程，直到挂起函数执行完毕。其中可在协程把任务调度到别的线程执行，运行完毕后切回到原来协程的线程中。例如：withContext()代码块具备这功能。

## Android 上的 Kotlin 数据流
