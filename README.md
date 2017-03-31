# 1.Android系统的架构

## 1.Android系统架构之应用程序
Android会同一系列核心应用程序包一起发布，该应用程序包包括email客户端，SMS短消息程序，日历，地图，浏览器，联系人管理程序等。所有的应用程序都是使用JAVA语言编写的。
## 2.Android系统架构之应用程序框架
开发人员可以完全访问核心应用程序所使用的Api框架（android.jar）。该应用程序的架构设计简化了组件的重用；任何一个应用程序都可以发布它的功能块并且任何其他的应用程序都可以使用其发布的功能块。
## 3.Android系统架构之系统运行库
Android包含一些C/C++库，这些苦能被Android系统中的组件使用。它们通过Android应用程序框架为开发者提供服务。
## 4.Android系统架构之Linux内核
Android的核心系统服务依赖于Linux2.6内核，如安全性，内存管理，进程管理，网络协议栈和驱动模型。Linux内核也同时作为硬件和软件之间的抽象层。

# 2.activity的生命周期
![image](https://user-gold-cdn.xitu.io/2017/2/17/e3f9e991ccb1de7bb1cfa09488fb94d6.png)
Activity生命周期方法主要有onCreate(),onStart(),onResume(),onPause(),onStop(),onDestroy()和onRestart()等7个方法。
- 启动一个A Acticity，分别执行onCreate(),onStart,onRsume()方法。
- 从A Activity 打开B Activity分别执行A onPause(), B onCreate(),B onstart(),B onResume(),A onStop()方法。
- 关闭B Activity,分别执行B onPause(),A onRestart(),A onResume(),B onStop(),B onDestory()方法。
- 横竖屏切换A Activity,清单文件中不设置android：configChanges属性时，先销毁onPause(),onStop(),onDestory()在重新创建onCreate(),onStart(),onResume()方法，设置orientation|screenSize(一定要同时出现)属性值时，不走生命周期方法，只会执行onConfigurationChange()方法。
- Acitivity之间的切换可以看出onPause(),onStop()这两个方法比较特殊，切换的时候onPause()方法不要加入太多耗时操作否则会影响体验。

# 3.Fragment的生命周期
Fragment的生命周期

![image](https://user-gold-cdn.xitu.io/2017/2/17/f100848ea2eed17b1fa92c1f240f07c3.png)

Fragment与Activity生命周期对比

![image](https://user-gold-cdn.xitu.io/2017/2/17/f1ba4c43d09e6a9c24332cc764d93bc5.png)
Fragment的生命周期方法主要有onAttach(),onCreate(),onCreateView(),onActivityCreated(),onStart(),onResume(),onPause(),onStop(),onDestroyView,onDestory(),onDetach()等11个方法。
- 切换到该Fragment，分别执行onAttach(),onCreate(),onCreateView(),onAcitivityCreated(),onStart(),onResume()方法。
- 锁屏，分别执行onPause(),onStop()方法。
- 亮屏，分别执行onStart(),onResume()方法。
- 覆盖，切换到其他Fragment，分别执行onPause().onStop(),onDestoryView()方法。
- 从其他Fragment回到之前的Fragment，分别执行onCreateView(),onActivityCreated(),onStart(),onResume()方法。

# 4.service生命周期
在service的生命周期里常有：

4个手动调用的方法

- startService()   启动服务
- stopService()    关闭服务
- bindService()    绑定服务
- unbindService()  解绑服务

5个内部自动调用的方法

- onCreate()        创建服务
- onStartCommand() 开始服务
- onDestory()      销毁服务
- onBind()         绑定服务
- onUnbind()       解绑服务

1. 手动调用startService()启动服务，自动调用内部方法：onCreate(),onStartCommand(),如果一个Service被startService()多次启动，那么onCreate()也只会调用一次。
2. 手动调用stopService()关闭服务，自动调用内部方法：onDetroy(),如果一个service被启动且绑定，如果在没有解绑的前提下使用stopService()关闭服务是无法停止服务的。
3. 手动调用bindService()后，自动调用内部方法：onCreate(),onBind().
4. 手动调用unBindService()后，自动调用内部方法:onUnbind(),onDestory().
5. startService()和StopService()只能开启和关闭Service，无法操作service，调用者退出后Service仍然存在；bindService()和unBindService()可以操作Service,调用者退出后，Service随着调用者销毁。


# 5.Android中动画 
Android中动画分别帧动画，补间动画和属性动画（Android3.0以后的）

## 帧动画
帧动画是最容易实现的一种动画，这种动画更多的依赖于完美的UI资源，他的原理是间一张张单独的图片连贯的进行播放，从而在视觉上产生一种动画的效果；有点类似于某些软件制作gif动画的方式。在有些代码中，我们还会看到android:oneshot="false",这个oneshot的含义就是动画执行一次（true）还是循环执行多次。
```
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:drawable="@drawable/a_0"
        android:duration="100" />
    <item
        android:drawable="@drawable/a_1"
        android:duration="100" />
    <item
        android:drawable="@drawable/a_2"
        android:duration="100" />
</animation-list>
```
## 补间动画
补间动画有可以分为四种，分别是alpha（淡入淡出），translate（位移），scale（缩放大小），rotate（旋转）。
补间动画的实现，一般会采用xml文件的形式；代码会更容易书写和阅读，同时也更容易复用。interpolator主要作用是可以控制动画的变化速率，就是动画进行的快慢节奏。pivot决定了当前动画执行的参考位置
```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@[package:]anim/interpolator_resource"
    android:shareInterpolator=["true" | "false"] >
    <alpha
        android:fromAlpha="float"
        android:toAlpha="float" />
    <scale
        android:fromXScale="float"
        android:toXScale="float"
        android:fromYScale="float"
        android:toYScale="float"
        android:pivotX="float"
        android:pivotY="float" />
    <translate
        android:fromXDelta="float"
        android:toXDelta="float"
        android:fromYDelta="float"
        android:toYDelta="float" />
    <rotate
        android:fromDegrees="float"
        android:toDegrees="float"
        android:pivotX="float"
        android:pivotY="float" />
    <set>
        ...
    </set>
</set>
```
## 属性动画
属性动画，顾名思义它是对于对象属性的动画。因此，所有补间动画的内容，都可以通过属性动画实现。属性动画的运行机制是通过不断地对值进行操作来实现的，而初始值和结束值之间的动画过渡就是由ValueAnimator这个类来负责计算的。它的内部使用一种时间循环的机制来值与值之间的动画过渡，我们只需要将初始值和结束值提供给ValueAnimator就会自动帮我们完成从初始值平滑地过渡到结束值这样的效果。除此之外，ValueAnimator还负责管理动画的播放次数，播放模式，以及对动画设置监听器等。
# 6.Android中的4大组件
1. Activity：Activity是Android程序与用户交互的窗口，是Android构造中最基本的一种，他需要为保持各界面的状态，做很多持久化的事情，妥善管理生命周期以及一些跳转逻辑。
2. BroadCast Receiver:接受一种或者多种intent作触发事件，接受相关消息，做一些简单处理，转换成一条Notification，统一了Android的事件广播模型。
3. Content Provider：是Android提供的第三方应用数据的访问方案，可以派生Content Provider 类，对外提供数据，可以像数据一样进行选择排序，屏蔽内部数据的存储细节,向外提供统一的接口模型，大大简化上层应用，对数据的整合提供了更方便的途径。
4. Service：后台服务于Activity，封装有一个完整的功能逻辑实现，接受上层指令，完成相关的事务，定义好需要接受的intent提供同步和异步的接口。

# 7.Android中常用布局
常用的布局：
- FrameLayout(帧布局)：所有东西依次都放在左上角，会重叠。
- LinearLayout(线性布局)：按水平和垂直进行数据展示。
- RelativeLayout(相对布局)：以某一个元素为参照物，来定位的布局方式。

不常用的布局：
- TableLayout(表格布局)：每一个TableLayout里面有表格行TableRow，TableRow里面可以具体定义每一个元素（Android TV上使用）
- AbsoluteLayout(绝对布局)：用x,y坐标来指定元素的位置，元素多就不适用。（机顶盒上使用）
新增布局：
- PercentRelativeLayout(百分比相对布局)：可以通过百分比控制控件的大小。
- PercentFrameLayout(百分比帧布局)：可以通过百分比控制控件的大小。

# 8. Android数据存储
1. 使用SharedPreference存储数据；它是Android提供的用来存储一些简单配置信息的一种机制，采用了XML格式将数据存储到设备中，只能在同一个包内使用，不能在不同包之间使用。
2. 文件存储数据；文件存储方式是一种叫常用的方法，在Android中读取/写入文件的方法，与java中实现I/O的程序是完全一样的，提供了openFileInput()和openFileOutput()方法来读取设备上的文件。
3. SQlite数据库存储数据；SQlite是Android所带的一个标准的数据库，它支持SQL语句，它是一个轻量级的嵌入式数据库。
4. 使用ContentProvider存储数据；主要在于应用程序之间进行数据交换，从而能够让其他的应用保存或读取此ContentProvider的各种数据的类型。
5. 网络存储数据；同网络上提供给我们的存储空间来上传（存储）和下载（获取）我们存储在网络空间中的数据信息。


# 9.Activity启动模式
介绍Android启动模式之前，先介绍两个概念task和taskAffinity
- task：翻译过来就是“任务”，是一组相互关联的activity集合，可以理解为Activity是在task里面活动的。task存在于一个被称为back stack的数据结构中，也就是说，task是以栈的形式去管理activity的，所以也可以叫做“任务栈”。
- taskAffinity：官方文档翻译是：“The task that the activity has a affinity for.”,可以翻译为activity相关或者亲和任务，这个参数标识了一个Activity所需要的任务栈的名字。默认情况下，所有Activity所需要的任务栈的名字为应用的包名。taskAffinity属性主要和singleTask启动模式或者allowTaskReparenting属性配对使用。

4种启动模式
1. standard：标准模式，也是系统默认的启动模式。假如activity A启动了activity B，activity B则会运行在activity A所在的任务栈中。而且每次启动一个Activity，都会重新创建新的实例，不管这个实例在任务中是否已经存在。非Activity类型的context（如ApplicationContext）启动standard模式的Activity时会报错。非Activity类型的context并没有所谓的任务栈，由于上面第1点的原因所以系统会报错。此解决办法就是待启动acti指定FLAG_ACTIVITY_NEW_TASK标记位，这样启动的时候系统就会为它创建一个新的任务栈。这个时候待启动Activity其实是以singleTask模式启动的。
2. singleTop：栈顶复用模式。假如activity A启动了activity B，就会判断A所在的任务栈栈顶是否是B实例。如果是，则不创建新的activity B实例而是直接引用这个栈顶实例，同时onNewIntent方法会被调用，通过该方法的参数可以取得当前请求的信息；如果不是，则创建新的activity B实例。
3. singleTask：栈内复用模式。在第一次启动这个Activity时，系统便会创建一个新的任务，并且初始化Activity的实例，放在心任务的底部。不过需要满足一定条件的。那就是需要设置taskAffinity属性。前面也说过了，taskAffinity属性是和singleTask模式搭配使用的。
![image](https://raw.githubusercontent.com/jjdxmashl/online_image/master/androidface/icon04.png)
4. singleInstance:单实例模式。这个时singleTask模式的加强版，它除了具有singleTask模式的所有特性外，它还有一点独特的特性，那就是此模式的Activity只能单独地位于一个任务栈，不与其他Activity共存于同一个任务栈。
# 10.广播注册
首先写一个类要继承BroadCastReceiver
第一种：在清单文件中声明，添加
```
<receive android:name=".BroadCastReceiverDemo">
<intent-filter>
<action android:name="android.provider.Telephony.SMS_RECEIVED">
</intent-filter>
</receiver>
```
第二种：使用代码进行注册如：
```
IntentFilter filter = new IntentFilter("android.provider.Telephony.SMS_RECEIVED");
BroadCastReceiverDemo receiver = new BroadCastReceiver();
registerReceiver(receiver, filter);
```
两种注册类型的区别是：
1. 第一种是常驻型广播，也就是说当应用程序关闭后，如果有信息广播来，程序也会被系统调用自动运行。
2. 第二种不是常驻型广播，也就是说广播跟随程序的生命周期。

# 11.Android中的ANR

ANR的全称application not responding responding 应用程序未响应。
- 在android中Activity的最长执行事时间为5秒。
- broadcastreceiver的最长执行时间则是10秒。
- Service的最长执行时间是20秒。

超过执行时间就会产生ANR。注意：ANR是系统抛出的异常，程序是捕捉不了这个异常的。
解决方法：
1. 运行在主线程里的任何方法都尽可能少做事情。特别是，Activity应该在它的关键生命周期方法（如onCreate()和OnResue()）里尽可能少的去做创建操作。（可以采用重新开启子线程的方式，然后使用hander+message的方式去做一些操作，比如更新主线程中的UI等）。
2. 应用程序应该避免在broadcastreceiver里面做耗时的操作或者计算。但不再是在子线程里做这些任务（因为broadcastreceiver的生命周期短）替代的是，如果响应intent广播需要执行一个耗时的动作的话，应用程序应该启动一个Service。

# 12.ListView优化
1. convertView重用，利用好convertView来重用View，切勿每次getView()都新建。ListView的核心原理就是重用View，如果重用View不改变宽高，重用View就可以减少重新分配缓存造成的内存频繁分配/回收。
2. ViewHolder优化，使用ViewHolder的原因是findViewById方法耗时较大，如果控件个数过多，会严重影响性能，而使用ViewHolder主要是为了可以省去这个时间。通过setTag，getTag直接获取View。
3. 减少ItemView的布局层次，这是所有layout都必须遵循的，布局层级过深会直接导致View的测量和绘制浪费大量的时间。
4. adapter中的getView方法尽量少使用逻辑。
5. 图片加载采用三级缓存，避免每次都要重新加载。
6. 尝试开启硬件加速来使ListView的滑动更加流畅。
7. 使用RecycleView代替。

# 13.Thread和Runnable的区别
在程序开发中只要是多线程肯定永远以实现Runnable接口为主，因为实现Runnable接口相比。
继承Thread类有如下好处：
- 避免点继承的局限，一个类可以继承多个接口。
- 适合于资源的共享

在使用Runnable定义的子类中没有start()方法，只有Thread类中才有。此时观察Thread类，有一个构造方法：public Thread(Runnable targer)此构造方法接受Runnable的子类实例，也就是说可以通过Thread类来启动Runnable实现的多线程。
# 14.Android中activity，context，application有什么不同
Content与application都继承与contextWrapper，contextWrapper继承于Context类。

Context：表示当前上下文对象，保存的是上下文中的参数和变量，它可以让更加方便访问到一些资源。

Context通常与activity的生命周期是一样的，application表示整个应用程序的对象。

对于一些生命周期较长的，不要使用context，可以使用application。

在activity中，尽量使用静态内部类，不要使用内部类。内部里作为外部类的成员存在，不是独立于activity，如果内存中还有内存继续引用到context，activity如果被销毁，context还不会结束。


## Context区别

- Activity和Service以及Application的Context是不一样的,Activity继承自ContextThemeWraper.其他的继承自ContextWrapper
- 每一个Activity和Service以及Application的Context都是一个新的ContextImpl对象
- getApplication()用来获取Application实例的，但是这个方法只有在Activity和Service中才能调用的到。那么也许在绝大多数情况下我们都是在Activity或者Service中使用Application的，但是如果在一些其它的场景，比如BroadcastReceiver中也想获得Application的实例，这时就可以借助getApplicationContext()方法，getApplicationContext()比getApplication()方法的作用域会更广一些，任何一个Context的实例，只要调用getApplicationContext()方法都可以拿到我们的Application对象。
- Activity在创建的时候会new一个ContextImpl对象并在attach方法中关联它，Application和Service也差不多。ContextWrapper的方法内部都是转调ContextImpl的方法
- 创建对话框传入Application的Context是不可以的
- 尽管Application、Activity、Service都有自己的ContextImpl，并且每个ContextImpl都有自己的mResources成员，但是由于它们的mResources成员都来自于唯一的ResourcesManager实例，所以它们看似不同的mResources其实都指向的是同一块内存
- Context的数量等于Activity的个数 + Service的个数 + 1，这个1为Application

# 15.Serializable 和 Parcelable 的区别？
如果存储在内存中，推荐使用parcelable，使用serialiable在序列化的时候会产生大量的临时变量，会引起频繁的GC

如果存储在硬盘上，推荐使用Serializable，虽然serializable效率较低

Serializable的实现：只需要实现Serializable接口，就会自动生成一个序列化id

Parcelable的实现：需要实现Parcelable接口，还需要Parcelable.CREATER变量

# 16.ListView 如何提高其效率？
当 convertView 为空时，用 setTag()方法为每个 View 绑定一个存放控件的 ViewHolder 对象。当convertView 不为空， 重复利用已经创建的 view 的时候， 使用 getTag()方法获取绑定的 ViewHolder对象，这样就避免了 findViewById 对控件的层层查询，而是快速定位到控件。 复用 ConvertView，使用历史的 view，提升效率 200%

自定义静态类 ViewHolder，减少 findViewById 的次数。提升效率 50%

异步加载数据，分页加载数据。

使用 WeakRefrence 引用 ImageView 对象
# 17.uses-permission和permission的区别
就是<uses-permission>是官方定义的权限，是调用别人的东西的时候自己需要声明的权限，<permission>是自己定义的权限，就是别人调用这个程序时需要用<uses-permission>来声明。
在一般情况下实际上不需要为自己的应用程序声明某个权限，除非你提供了供其他应用程序调用的代码或者数据。这个时候你才需要使用<permission>这个标签，很显然这个标签可以让我们声明自己程序的权限。

# 18.Fragment 在你们项目中的使用

Fragment 是 android3.0 以后引入的的概念，做局部内容更新更方便，原来为了到达这一点要把多个布局放到一个 activity 里面，现在可以用多 Fragment 来代替，只有在需要的时候才加载Fragment，提高性能。

Fragment 的好处：
- Fragment 可以使你能够将 activity 分离成多个可重用的组件，每个都有它自己的生命周期和UI。
- Fragment 可以轻松得创建动态灵活的 UI 设计，可以适应于不同的屏幕尺寸。从手机到平板电脑。
- Fragment 是一个独立的模块,紧紧地与 activity 绑定在一起。可以运行中动态地移除、加入、交换等。
- Fragment 提供一个新的方式让你在不同的安卓设备上统一你的 UI。
- Fragment 解决 Activity 间的切换不流畅，轻量切换。
- Fragment 替代 TabActivity 做导航，性能更好。
- Fragment 在 4.2.版本中新增嵌套 fragment 使用方法，能够生成更好的界面效果。

# 19.Android 线程间通信有哪几种方式
- 共享内存（变量）；
- 文件，数据库；
- Handler；
- Java 里的 wait()，notify()，notifyAll()；

# 20.Framework 工作方式及原理，Activity 是如何生成一个 view 的，机制是什么？
所有的框架都是基于反射 和 配置文件（manifest）的。

普通的情况:

Activity 创建一个 view 是通过 ondraw 画出来的, 画这个 view 之前呢,还会调用 onmeasure方法来计算显示的大小.

特殊情况：

Surfaceview 是直接操作硬件的，因为 或者视频播放对帧数有要求，onDraw 效率太低，不够使，Surfaceview 直接把数据写到显存。

# 21.Android中touch事件的传递机制是怎样的?
1. Touch事件传递的相关API有dispatchTouchEvent、onTouchEvent、onInterceptTouchEvent 
2. Touch事件相关的类有View、ViewGroup、Activity 
3. Touch事件会被封装成MotionEvent对象，该对象封装了手势按下、移动、松开等动作 
4. Touch事件通常从Activity#dispatchTouchEvent发出，只要没有被消费，会一直往下传递，到最底层的View。 
5. 如果Touch事件传递到的每个View都不消费事件，那么Touch事件会反向向上传递,最终交由Activity#onTouchEvent处理. 
6. onInterceptTouchEvent为ViewGroup特有，可以拦截事件. 
7. Down事件到来时，如果一个View没有消费该事件，那么后续的MOVE/UP事件都不会再给它

# 22.描述下Handler 机制
1. Looper: 一个线程可以产生一个Looper对象，由它来管理此线程里的MessageQueue(消息队列)。 
2. Handler: 你可以构造Handler对象来与Looper沟通，以便push新消息到MessageQueue里;或者接收Looper从Message Queue取出)所送来的消息。
3. Message Queue(消息队列):用来存放线程放入的消息。 
4. 线程：UIthread 通常就是main thread，而Android启动程序时会替它建立一个MessageQueue。

Hander持有对UI主线程消息队列MessageQueue和消息循环Looper的引用，子线程可以通过Handler将消息发送到UI线程的消息队列MessageQueue中。

# 23.自定义view的基本流程
- 自定义View的属性 编写attr.xml文件 
- 在layout布局文件中引用，同时引用命名空间 
- 在View的构造方法中获得我们自定义的属性 ，在自定义控件中进行读取（构造方法拿到attr.xml文件值） 
- 重写onMesure 
- 重写onDraw

# 24.事件分发中的 onTouch 和 onTouchEvent 有什么区别，又该如何使用？
这两个方法都是在 View 的 dispatchTouchEvent 中调用的，onTouch 优先于 onTouchEvent执行。如果在 onTouch 方法中通过返回 true 将事件消费掉，onTouchEvent 将不会再执行。

另外需要注意的是，onTouch 能够得到执行需要两个前提条件，第一 OnTouchListener 的值不能为空，第二当前点击的控件必须是 enable 的。因此如果你有一个控件是非 enable 的，那么给它注册 onTouch 事件将永远得不到执行。对于这一类控件，如果我们想要监听它的 touch 事件，就必须通过在该控件中重写 onTouchEvent 方法来实现。
# 25.一般在开发项目中都使用什么设计模式？如何来重构，优化代码？
较为常用的就是单例设计模式，工厂设计模式以及观察者设计模式,

一般需要保证对象在内存中的唯一性时就是用单例模式,例如对数据库操作的 SqliteOpenHelper 的对象。

工厂模式主要是为创建对象提供过渡接口，以便将创建对象的具体过程屏蔽隔离起来，达到提高灵活性的目的。

观察者模式定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。
# 26. 定位项目中，如何选取定位方案，如何平衡耗电与实时位置的精度？
开始定位，Application 持有一个全局的公共位置对象，然后隔一定时间自动刷新位置，每次刷新成功都把新的位置信息赋值到全局的位置对象， 然后每个需要使用位置请求的地方都使用全局的位置信息进行请求。

- 该方案好处：请求的时候无需再反复定位，每次请求都使用全局的位置对象，节省时间。
- 该方案弊端：耗电，每隔一定时间自动刷新位置，对电量的消耗比较大。

按需定位，每次请求前都进行定位。这样做的好处是比较省电，而且节省资源，但是请求时间会变得相对较长。

# 27.andorid 应用第二次登录实现自动登录
前置条件是所有用户相关接口都走 https，非用户相关列表类数据走 http。
- 第一次登陆 getUserInfo 里带有一个长效 token，该长效 token 用来判断用户是否登陆和换取短 token 
- 把长效 token 保存到 SharedPreferences 
- 接口请求用长效 token 换取短token，短 token 服务端可以根据你的接口最后一次请求作为标示，超时时间为一天。
- 所有接口都用短效 token 
- 如果返回短效 token 失效，执行第3步，再直接当前接口 
- 如果长效 token 失效（用户换设备或超过一月），提示用户登录。

 # 28.怎样对 android 进行优化？
- 对 listview 的优化。
- 对图片的优化。
- 对内存的优化。
- 具体一些措施
- 尽量不要使用过多的静态类 static
- 数据库使用完成后要记得关闭 cursor
- 广播使用完之后要注销

# 29.wait()和sleep()的区别
sleep()方法是线程类（Thread）的静态方法，导致此线程暂停执行指定时间，将执行机会给其他线程，但是监控状态依然保持，到时后会自动恢复（线程回到就绪（ready）状态），因为调用sleep 不会释放对象锁。wait()是Object 类的方法，对此对象调用wait()方法导致本线程放弃对象锁(线程暂停执行)，进入等待此对象的等待锁定池，只有针对此对象发出notify 方法（或notifyAll）后本线程才进入对象锁定池准备获得对象锁进入就绪状态。

# 30.自定义View过程：
- onMeasure()
- onLayout()
- onDraw()。

## 如何自定义控件：

1. 自定义属性的声明和获取
- 分析需要的自定义属性
- 在res/values/attrs.xml定义声明
- 在layout文件中进行使用
- 在View的构造方法中进行获取
2. 测量onMeasure
3. 布局onLayout(ViewGroup)
4. 绘制onDraw
5. onTouchEvent
6. onInterceptTouchEvent(ViewGroup)
7. 状态的恢复与保存
