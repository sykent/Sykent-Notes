# Fragment

## Fragment 的生命周期

<img src="https://sykent-blog-image.oss-cn-beijing.aliyuncs.com/android/other/frg_life_1.jpg" width = "70%" />
<img src = "https://sykent-blog-image.oss-cn-beijing.aliyuncs.com/android/other/frg_life_2.jpg" width = "70%"/>

## 类关系
```plantuml
class FragmentTransaction
class FragmentManager

```

## 源码分析

```plantuml
class FragmentTransaction
class BackStackRecord

class FragmentManager
class FragmentManagerImpl
class Fragment
class Op
```
