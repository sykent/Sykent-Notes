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
