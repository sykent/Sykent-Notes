# Activity生命周期和启动方式

### Activity 生命周期切换
#### 生命周期分析
  ![](https://raw.githubusercontent.com/sykent/blogimage/master/android/开发者的艺术/Activity生命周期切换过程.jpg)

 MainActivity 启动SecondActivity
  ![](https://raw.githubusercontent.com/sykent/blogimage/master/android/开发者的艺术/A启动B.jpg)

#### onStart、onStop | onPause、onResume 区别
onStart、onStop Activity是从是否可见的回调；onPause、onResume Activity是从是否在前台的回调。

### 异常情况下重建生命周期分析。
#### 重建生命周期
![](https://raw.githubusercontent.com/sykent/blogimage/master/android/开发者的艺术/异常情况Activity重建过程.jpg)

生命周期日志信息
![](https://raw.githubusercontent.com/sykent/blogimage/master/android/开发者的艺术/activity重建时日志.jpg)
#### 避免系统配置改变是重建

Q：当系统配置发生改变时，Activity 会被重建，如何保护？
A：配置configChanges属性进行保护不重建，例如：`android:configChanges="orientation|keyboardHidden|navigation|screenSize"`

### Activity启动模式

#### 四种启动模式：
* standard：标准模式。每次启动都会重新创建实例，其所在的任务栈和启动它的Activity的任务栈中。
* singleTop：栈顶复用模式。如果新Activity已存在任务栈中且在栈顶，则不会被重新创建，同时回调onNewIntent函数；否则新的Activity重新创建。
* singleTask：栈内复用模式。这是一种单例模式，只要Activity在一个栈中，多次启动此Activity不会重新创建，同时回调onNewIntent函数。如果该Activity不在栈顶，则位于该Activity上层的Activity会出栈。该Activity的任务栈所有Activity会切换到启动任务栈中。
* singleInstance：单实例模式。该模式是singleTask的加强版，其不同之处在于这种模式的Activity只能独立于一个新的任务栈中。

#### singleTask启动模式的示例
A、B位于前台任务栈，C、D位于后台任务栈，B启动D后回退

![](https://raw.githubusercontent.com/sykent/blogimage/master/android/开发者的艺术/singleTask启动模式示例11.jpg)

A、B位于前台任务栈，C、D位于后台任务栈，B启动C后回退
![](https://raw.githubusercontent.com/sykent/blogimage/master/android/开发者的艺术/singleTask启动模式示例2.jpg)

#### 指定任务栈
没指定任务栈，默认为该应用的包名，若指定则配置`tashAffinity`的值。

#### 查看Activity 任务栈
命令`adb shell dumpsys activity`，其中信息中的TaskRecord中的Run#xx 记录了任务栈的Activity，所以我们只需要查看Running activities(most recent first)这块信息情况。
例如下图：
![](https://raw.githubusercontent.com/sykent/blogimage/master/android/开发者的艺术/命令查看activity任务栈.jpg)

### Activity Flags

* FLAG_ACTIVITY_NEW_TASK：作用指定Activity 为`singleTask`启动模式。
* FLAG_ACTIVITY_SINGLE_TOP：作用指定Activity为`sigleTop`启动模式。
* FLAG_ACTIVITY_CLEAR_TOP：当它启动时，在同一个任务栈中位于它上面Activity 都要出栈。
* FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS：具有该标志的Activity不会出现在历史Activity的列表中。

### IntentFilter 的匹配规则
启动Activity 主要有两种，显式调用和隐式调用，显示调用需要明确被启动对象的组件信息，隐式调用则需要匹配IntentFilter中所设置的过滤信息，匹配过滤表中的action类别、category类别、data类别。

* action 的匹配规则：
  必须设置，至少匹配一个action。
* category 的匹配规则：
  可不设置，默认为：`android.intent.category.DEFAULT`；
  如果设置了category，必须全部匹配所有category，这里是和action 的不同点。
* data 的匹配规则：
  data 可分开写也可合并写，其作用一样。
  格式：
  ```
  <data android:scheme="string"
        android:host="string"
        android:port="string"
        android:path="string"
        android:pathPattern="string"
        android:pathPrefix="string"
        android:mimeType="string"/>
  ```
  ```
  <data android:scheme="string"/>
  <data android:host="string"/>
  ...
  ```
  data 由两部分组成，mimeType和URI。mimeType 是媒体类型。
  URI 结构：`<scheme>://<host>:<port>/[<path>|<pathPrefix>|<pathPattern>]`
* 检查是是否匹配成功，采用 PackageManager 的 resloveActivity 方法。
  ```
   public abstract List<ResolveInfo> queryIntentActivityies(Intent intent,int flags);
   public abstract ResolveInfo resloveActivity(Intent intent,int flags);
  ```
