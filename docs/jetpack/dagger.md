# Dagger

## 注解

* @Inject：声明依赖注入对象。
* @Moudle：依赖提供方，负责提供依赖中所需要的对象。
* @Component：依赖注入组件，负责将依赖注入到依赖需求方。
* @Provides：会根据返回值的类型，寻找有此注解应调用的方法。

**编写步骤**
* 编写一个需要被注入的类。
* 编写一个提供方的类，并提供一个方法获得被注入类的实例。
* 编写一个注入组件，并注解提供方，与一个注入方法。
* 声明依赖注入对象，使用注入组件，并调用注入方法。

```plantuml

class UseObj
class InjectObj

class Model
interface Component{
  void inject(UseObj obj)
}
class DaggerComponent

DaggerComponent .up.|> Component
DaggerComponent --> Model
Model .right.> InjectObj
UseObj --> InjectObj
UseObj <.left.> DaggerComponent

```

[例子](https://juejin.im/entry/5970a8175188254d1c7ab4b2)
[例子2](https://zhuanlan.zhihu.com/p/113124369)
