# IPC 机制

IPC 是Inter-Process Communication 的缩写，含义为进程间通信或者跨进程通信，是指两个进程之间的数据交换过程。

### 开启多进程模式

四大组件都可以配置，Manifest中指定 android:process 属性。这个属性的值就是进程名。以`:`开头的进程属于该应用的私有进程，不允许其他应用组件和它跑在同一个进程中，否则为全局进程，其他应用可通过ShareUID的方式跑在同一进程中。

命令查看：使用 adb shell ps 或 adb shell ps|grep 包名 查看当前所存在的进程信息。

### IPC基础

#### 数据
* Serializable接口
  该接口为java 提供的序列化接口。之间实现即可，相当简单。其中serialVersionUID作用于序列化和反序列化。如果没写，会自动根据成员变量生成，类有改动会导致反序化失败。
* Parcalable接口
  该接口是android 提供的可序列化接口。实现这接口也并不复杂，先说下Parcel，这类内部封装了可序列化数据。所以Parcel是一个中间类，需要序列化的数据Bean 通过writeToParcal转为Parcel，再通过creatFromParcel，依赖注入Bean 转回Bean。

#### Binder

直观上说Binder是Android 中的一个类，他实现了IBinder接口。从IPC角度来说，Binder 是Android 中的一种跨进程通信方式，Binder还可以理解为一种虚拟的物理设备。

通过服务端返回来的Binder对象，客户端可以获取服务端提供的服务或者数据。

Binder 的工作机制
![](https://raw.githubusercontent.com/sykent/blogimage/master/android/%E5%BC%80%E5%8F%91%E8%80%85%E7%9A%84%E8%89%BA%E6%9C%AF/ipc_binder%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B61.jpg)

### AIDL文件自动生成java

1. Book.java
   表示图书信息的实体类，实现了Parcelable接口。
2. Book.aidl
   Book 类在AIDL中声明
   ```
   package com.sykent.ipc;
   parcelable Book;
   ```
3. IBookManager.aidl
    ```
    // IBookManager.aidl
    package com.sykent.ipc;

    // 一定要导包
    import com.sykent.ipc.Book;
    // Declare any non-default types here with import statements

    interface IBookManager {
        List<Book> getBookList();
        void addBook(in Book book);
    }
    ```
  定义的管理Book实体的一个接口，包含 getBookList 和 addBook 两个方法。
  系统为IBookManager.aidl生产的Binder类，在 gen 目录下的IBookManager.java类。结构如下：
   * 声明了 getBookList 和 addBook 方法，还声明了两个整型id分别标识这两个方法，用于标识transact 过程中客户端请求的到底是哪个方法。
   * 声明了一个内部类 Stub ，这个 Stub 就是一个Binder类，当客户端和服务端位于同一进程时，方法调用不会走跨进程的 transact 。当二者位于不同进程时，方法调用需要走 transact 过程，这个逻辑有 Stub 的内部代理类 Proxy 来完成。
   * 这个接口的核心实现就是它的内部类 Stub 和 Stub 的内部代理类 Proxy 。

```plantuml
    interface IBookManager{
        List<Book> getBookList()
        void addBook()
      }
      interface IInterface{
        public IBinder asBinder();
      }
      class Binder{
        public void linkToDeath(DeathRecipient recipient, int flags);
        public boolean unlinkToDeath(DeathRecipient recipient, int flags);
        public boolean isBinderAlive();
      }
      abstract IBookManager.Stub{
        public static IBookManager asInterface(IBinder obj)
        public IBinder asBinder();
      }
      abstract IBookManager.Proxy

      IBookManager .u.|>IInterface
      IBookManager.Stub .u.|> IBookManager
      IBookManager.Stub -u-|> Binder

      IBookManager.Proxy .u.|> IBookManager
      IBookManager.Proxy -u-|> Binder
```

#### Stub和Proxy类的内部方法和定义

1. DESCRIPTOR
Binder的唯一标识，一般用Binder的类名表示。
2. asInterface(android.os.IBinder obj)
将服务端的Binder对象转换为客户端所需的AIDL接口类型的对象，如果C/S位于同一进
程，此方法返回就是服务端的Stub对象本身，否则返回的就是系统封装后的Stub.proxy对
象。
3. asBinder
返回当前Binder对象。
4. onTransact
这个方法运行在服务端的Binder线程池中，由客户端发起跨进程请求时，远程请求会通过
系统底层封装后交由此方法来处理。该方法的原型是
java public Boolean onTransact(int code,Parcelable data,Parcelable reply,int flags)
i. 服务端通过code确定客户端请求的目标方法是什么，
ii. 接着从data取出目标方法所需的参数，然后执行目标方法。
iii. 执行完毕后向reply写入返回值（如果有返回值）。
iv. 如果这个方法返回值为false，那么服务端的请求会失败，利用这个特性我们可以来
做权限验证。
#### Binder死亡代理
可以给Binder设置一个死亡代理，当Binder死亡时，我
们就会收到通知。
1. 声明一个 DeathRecipient 对象。 DeathRecipient 只有一个方法 binderDied ，当Binder死
亡的时候，系统就会回调 DeathRecipient 方法。
```
private IBinder.DeathRecipient mDeathRecipient = new IBinder.DeathRecipient(){
        @Override
        public void binderDied(){
            if(mBookManager == null){
                return;
            }
            mBookManager.asBinder().unlinkToDeath(mDeathRecipient,0);
            mBookManager = null;
           // TODO：接下来重新绑定远程Service
        }
    }
```
2. Binder有两个很重要的方法 linkToDeath 和 unlinkToDeath 。通过 linkToDeath 为Binder
设置一个死亡代理。
```
mService = IBookManager.Stub.asInterface(binder);
binder.linkToDeath(mDeathRecipient,0);
```
3. 另外，可以通过Binder的 isBinderAlive 判断Binder是否死亡。

### Android 的中IPC 方式
* Bundle
* 文件共享
* Messenger
* AIDL
* ContentProvider
* Socket

优缺点:
![](https://raw.githubusercontent.com/sykent/blogimage/master/android/%E5%BC%80%E5%8F%91%E8%80%85%E7%9A%84%E8%89%BA%E6%9C%AF/ipc_%E9%80%9A%E4%BF%A1%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F%E7%9A%84%E4%BC%98%E7%BC%BA%E7%82%B9.jpg)

#### Bundle
四大组件中的三大组件（Activity、Service、Receiver）都支持在Intent中传递 Bundle 数据。
Bundle实现了Parcelable接口，当我们在一个进程中启动了另一个进程的Activity、
Service、Receiver，可以再Bundle中附加我们需要传输给远程进程的消息并通过Intent发送出去。被传输的数据必须能够被序列化。
#### Messenger
Messenger可以在不同的进程中传递Message对象，是一种轻量级的IPC方案，它底层是AIDL。
```plantuml

interface IMessenger{
  void send(in Message msg);
}
class Messenger{
  private IMessenger mTarget;

  public Messenger(Handler target);
   public Messenger(IBinder target);
}

Messenger *-- IMessenger
```
Messenger C/S 通信原理：

![](https://raw.githubusercontent.com/sykent/blogimage/master/android/%E5%BC%80%E5%8F%91%E8%80%85%E7%9A%84%E8%89%BA%E6%9C%AF/ipc_messeger_%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.jpg)

#### AIDL
如果有大量的并发请求，使用Messenger就不太适合，同时如果需要跨进程调用服务端的方
法，Messenger就无法做到了。这时我们可以使用AIDL。
流程如下：
1. 服务端需要创建Service来监听客户端请求，然后创建一个AIDL文件，将暴露给客户端的
接口在AIDL文件中声明，最后在Service中实现这个AIDL接口即可。
2. 客户端首先绑定服务端的Service，绑定成功后，将服务端返回的Binder对象转成AIDL接
口所属的类型，接着就可以调用AIDL中的方法了。
注意事项：
1. AIDL支持的数据类型：
i. 基本数据类型、String、CharSequence
ii. List：只支持ArrayList，里面的每个元素必须被AIDL支持
iii. Map：只支持HashMap，里面的每个元素必须被AIDL支持
iv. Parcelable
v. 所有的AIDL接口本身也可以在AIDL文件中使用
2. 自定义的Parcelable对象和AIDL对象，不管它们与当前的AIDL文件是否位于同一个包，
都必须显式import进来。
3. 如果AIDL文件中使用了自定义的Parcelable对象，就必须新建一个和它同名的AIDL文
件，并在其中声明它为Parcelable类型。
```
package com.ryg.chapter_2.aidl;
parcelable Book;
```
4. AIDL接口中的参数除了基本类型以外都必须表明方向in/out。AIDL接口文件中只支持方法，不支持声明静态常量。建议把所有和AIDL相关的类和文件放在同一个包中，方便管
理。
```
void addBook(in Book book);
```
5. AIDL方法是在服务端的Binder线程池中执行的，因此当多个客户端同时连接时，管理数据的集合直接采用 CopyOnWriteArrayList 来进行自动线程同步。类似的还IPC机制有ConcurrentHashMap 。
6. 因为客户端的listener和服务端的listener不是同一个对象，所以 RecmoteCallbackList 是系统专门提供用于删除跨进程listener的接口，支持管理任意的AIDL接口，因为所有AIDL接口都继承自 IInterface 接口。
`public class RemoteCallbackList<E extends IInterface>`它内部通过一个Map接口来保存所有的AIDL回调，这个Map的key是 IBinder 类型，value是 Callback 类型。当客户端解除注册时，遍历服务端所有listener，找到和客户端listener具有相同Binder对象的服务端listenr并把它删掉。
7. 客户端RPC的时候线程会被挂起，由于被调用的方法运行在服务端的Binder线程池中，可能很耗时，不能在主线程中去调用服务端的方法。

#### ContentProvider
1. ContentProvider是四大组件之一，其底层实现和Messenger一样是Binder。ContentProvider天生就是用来进程间通信，只需要实现一个自定义或者系统预设置的ContentProvider，通过ContentResolver的query、update、insert和delete方法即可。
2. 创建ContentProvider，只需继承ContentProvider实现 onCreate 、query、 update 、 insert 、 getType 六个抽象方法即可。除了 onCreate 由系统回调并运行在主线程，其他五个方法都由外界调用并运行在Binder线程池中。

#### Socket
通过Socket 来实现进程间通信。对应网络的传输控制层的TCP和UDP协议进行的。

### Binder 连接池
AIDL是一种最常用的IPC方式，是日常开发中涉及IPC时的首选。前面提到AIDL的流程是客户端在Service的onBind方法中拿到继承AIDL的Stub对象，然后客户端就可以通过这个Stub对象进行RPC。那么如果项目庞大，有多个业务模块都需要使用AIDL进行IPC，随着AIDL数量的增加，我们不能无限制地增加Service，我们需要把所有AIDL放在同一个Service中去管理。
流程是：

1. 服务端只有一个Service，我们应该把所有AIDL放在一个Service中去管理，不同业务模块之间是不能有耦合的
2. 服务端提供一个 queryBinder 接口，这个借口能够根据业务模块的特征来返回响应的Binder对象给客户端
3. 不同的业务模块拿到所需的Binder对象就可以进行RPC了

![](https://raw.githubusercontent.com/sykent/blogimage/master/android/开发者的艺术/ipc_binder连接池4.jpg)
