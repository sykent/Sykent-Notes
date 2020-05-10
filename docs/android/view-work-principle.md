# View 的工作原理

## 理清关系

```plantuml
class Instrumentation
class ActivityThread{
  Activity performLaunchActivity(\nActivityClientRecord r, \nIntent customIntent)
}
class Activity{
  void attach(Context context, \nActivityThread aThread...)\n:产生PhoneWindow
}
class AttachInfo
class DecorView
class FrameLayout
abstract class Window{
  void setWindowManager(WindowManager wm,\n IBinder appToken, String appName,\nboolean hardwareAccelerated)
}
class PhoneWindow{
  View getDecorView()
}
class ViewRootImpl{
  void setView(View view,\nWindowManager.LayoutParams attrs,
     View panelParentView)
}
interface ViewParent
interface WindowManager
class WindowManagerImpl{
  void addView(View view, \nViewGroup.LayoutParams params)
}

class WindowManagerGlobal{
    void addView(View view,\n ViewGroup.LayoutParams params)
}

ActivityThread *.u.> Instrumentation
ActivityThread ..> Activity : attach
Activity -u-> PhoneWindow : new
Activity -u-> WindowManagerImpl : new
Activity --> DecorView
WindowManagerImpl .u.|> WindowManager
PhoneWindow .u.|> Window
Window --> WindowManagerImpl
WindowManagerGlobal --> ViewRootImpl : set
WindowManagerImpl ..> WindowManagerGlobal
PhoneWindow --> DecorView : 产生
ViewRootImpl o--> DecorView
ViewRootImpl --|> ViewParent
ViewRootImpl <--> AttachInfo
DecorView --|> FrameLayout
```

## 联通
* ViewRootImpl 联通 WMS 与 PhoneWindow 间的关系。addView、updateViewLayout、removeView，实际上是 window 的管理。
* PhoneWindow 联通事件分发等一些系统事件传递给 DecorView。
