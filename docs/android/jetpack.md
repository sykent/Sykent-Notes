# 组件
## lifecycle

```plantuml
class ComponentActivity{
  LifecycleRegistry mLifecycleRegistry
}
abstract class Lifecycle{
  abstract void addObserver(LifecycleObserver observer)
}
class LifecycleRegistry{
  FastSafeIterableMap<LifecycleObserver,\n ObserverWithState> mObserverMap
}
class LCR.ObserverWithState{
  void dispatchEvent(\nLifecycleOwner owner, Event event)
}
interface LifecycleObserver{
   Lifecycle getLifecycle()
}
interface LifecycleEventObserver


LifecycleRegistry -up-|> Lifecycle
LifecycleEventObserver -up-|>LifecycleObserver
LifecycleRegistry --> LifecycleObserver
LifecycleRegistry ..> LCR.ObserverWithState

ComponentActivity --> LifecycleRegistry
ComponentActivity ..|> LifecycleObserver

```

## ViewModel 创建方式

1. 第一种，一般在Activity中使用，它只会被创建一次

```
val viewModel by lazy {
     ViewModelProvider(this,ViewModelProvider.NewInstanceFactory()).get(modelClass)
}
```

2. 一般被用于Fragment，它也只有一个实例，被用于Fragment的数据共享

```
val viewModel by lazy {
     ViewModelProvider(requireActivity(),ViewModelProvider.NewInstanceFactory()).get(modelClass)
```

3. 一般被用于Fragment，但是它会随着Fragment的重新创建,也会随着Fragment销毁而销毁，一般配合着ViewPager 里面的 Fragment使用。

```
val viewModel by lazy {
     ViewModelProvider.NewInstanceFactory().create(modelClass)
}
```
