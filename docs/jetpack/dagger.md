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

## dagger 两种传参方式

* Module 直接声明变量，然后构造函数注入，Component 传入 Module
* 通过 @Component.Builder + @BindsInstance set 方法注入，Module 的  @Provides 传入参数。
```
    @Component.Builder
    interface Builder {
        @BindsInstance
        Builder setMainView(MainView view);

        MainComponent build();
    }
```

* @Subcomponent 注解子 Component，然后在父 Component 中暴露子类返回对象

[例子](https://juejin.im/entry/5970a8175188254d1c7ab4b2)
[例子2](https://zhuanlan.zhihu.com/p/113124369)

[android 的使用](https://juejin.im/post/5cc7202fe51d456e31164a6c)
[教程1](https://juejin.im/post/5ba4b5a75188255c38535fa3)
[教程2](https://juejin.im/post/5a39f26df265da4324809685)
