title: Android面试
categories: html
tags: 面试
date: 2022-01-13 16:35:20
---

# 四大组件

## 一、ContentProvider
    1. 使用什么类进行数据库的操作？
    使用SQLiteOpenHelper进行数据库操作。

    2. 在哪个方法中创建数据库？
    在onCreate中创建数据库，通过使用SQLiteDatabase.execSQL执行创建语句。

    3. 在哪个方法中升级数据库，如何进行升级，添加字段的语句是什么？
    在onUpgrade中进行数据库升级，通过对比Version进行升级。
    ALTER TABLE <表名> ADD <列名> <类型> <默认值>

    4. 数据库事务的作用？
    就是事务具有原子性，一个事务中的一系列的操作要么全部成功，要么一个都不做。
    保证操作的完整性，处理批量操作提高操作效率

    5. 数据库触发器
    SQLite 的触发器是数据库的回调函数，它会自动执行指定的数据库事件
    发生时调用。SQLite 的触发器可以指定在特定的数据库表发生 delete、
    insert或update时触发，或在一个或多个指定表的列发生更新时触发。

    6. 描述事务的隔离级别和传播属性

## 二、Service
    1. 启动Service的方法有哪些？它们的区别在哪？使用场景？
    startService：主要用于启动一个服务执行后台任务，不进行通信。停止服务使用stopService
    onCreate->onStartCommand->Service running->onDestroy->Service shutdown
    bindService：该方法启动的服务可以进行通信。停止服务使用unbindService，生命周期与其绑定的client息息相关。当client销毁时，client会自动与Service解除绑定。但是需要unbind否则会出现内存泄漏。
    onCreate->onBind->Clients are bound to service->onUnbind->onDestroy->Service shutdown

    2. IntentService：IntentService继承于Service，用于处理异步请求。通过工作队列把intent逐个发送给onHandleIntent()。执行完所一个Intent请求对象所对应的工作之后，如果没有新的Intent请求达到，则自动停止Service；否则执行下一个Intent请求所对应的任务。
       (1)IntentService是异步线程，Service是主线程
       例子：Messaging的action机制
       (2)在内部创建一个ServiceHandler

    3. 如果一个Service先被start，再被bind，那么如何完全停止
        此时如果解绑定，服务仍存在，若要停止服务，需要解绑定一次，stopService一次，没有先后顺序

## 三、Broadcast
    1. 广播的类型有哪些？分别有什么特性？
    普通广播，系统广播
    有序广播：按照Priority属性值从大-小排序；Priority属性相同者，动态注册的广播优先；按序接收，并且可以截断或修改广播。
    本地广播：安全性高 & 效率高
    粘性广播：广播发送之后，只要刚注册就能接收到广播，在API25之后废除

    2. 动态注册和静态注册有什么要注意的地方？
    有些广播只能通过动态注册，不能使用静态注册。对于动态注册的广播，要记得反注册，并且要在对应的生命周期。
    另外多次注册相同广播不会报异常，但是会影响代码逻辑和业务逻辑，多次反注册同一个广播会抛出广播不存在的异常。

    3. 在onReceive中，如果进行耗时操作，会有什么问题？
    ANR
    Service 前台：20 * 1000，后台：20 * 1000 * 10
    Broadcast：前台：10 * 1000，后台：60 * 1000
    InputDispatching/Activity： 5 * 1000
    ContentProvider：10 * 1000

    4. LocalBroadcast使用Handler实现的，所以不能进行跨进程的通信，只能用于线程间的通信
    Broadcast使用的Binder进行通信，可以跨进程。

## 四、Activity
    1. Activity的启动模式有哪些？<https://www.jianshu.com/p/a89e3e7b6f67>
    standard：默认启动模式，每启动一个新的Activity，就在返回栈入栈，并处于栈顶位置；（不管该活动是否已经在返回栈中存在，每次启动都会创建该活动的新实例）
    singleTop（onNewIntent)：如果发现有对应的Activity实例正位于栈顶，则重复利用，不再生成新的实例。如果不在栈顶，则生成新的实例。浏览器的书签；通讯消息聊天界面。
    singleTask：创建一个新活动时，检查返回栈中是否存在该活动实例；如果存在，则将该活动实例以上的所有活动统统出栈，否则创建该活动实例；某个Activity当做主界面的时候。
    singleInstance：创建新的返回栈管理该活动，且栈中只会存在该实例；比如浏览器BrowserActivity很耗内存，很多app都会要调用它，这样就可以把该Activity设置成单例模式。比如：闹钟闹铃。

    Flag:
    FLAG_ACTIVITY_NEW_TASK 创建一个新的堆栈，但是也不一定，可能跟亲和性有关系
    FLAG_ACTIVITY_CLEAR_TASK
    FLAG_ACTIVITY_SINGLE_TOP: 与singleTop一致
    FLAG_ACTIVITY_CLEAR_TOP: 当要启动的活动实例不存在时，创建新的活动实例入栈；当要启动的活动实例已经存在时，创建新的活动实例入栈的同时，会把已存在的活动实例以及栈中它上面所有的活动都销毁掉。

    taskAffinity:(与android:allowTaskReparenting使用)
    是设置活动和栈的依附关系。
    每个活动都可以设置自己的 taskAffinity 属性，这个属性指出了它希望进入的栈。如果一个活动没有指明该活动的 taskAffinity 属性，那么它的这个属性就等于 Application 指明的 taskAffinity，如果 Application 也没有指明，那么该 taskAffinity 的值就等于包名。而栈也有自己的 affinity 属性，它的值等于它的根活动的 taskAffinity的值。所以，设置该属性的时候，要按照包名的格式来设置，用 . 号分隔。

    2. Activity A启动另一个Activity B会回调哪些方法？如果Activity B是完全透明呢？如果启动的是一个对话框Activity呢
        Activity A启动另一个Activity B会回调的方法：Activity A的onPause() -->Activity B的onCreate()-->onStart()-->onResume()-->Activity A的onStop()；如果Activity B是完全透明的，则最后不会调用Activity A的onStop()；如果是对话框Activity，同后种情况。

    3. 如果在A里面打开Dialog周期是怎么走的
    如果dialog没有Activity创建的话，生命周期不会走。如果有的话才会走新的生命周期。

    4. onStart是可见，onResume是不可交互

    5. onSaveInstance, onRestoreInstance
    回调时机：如果targetsdk>=P而且运行在P手机上，就在onStop之后回调；否则，在onStop之前回调
    触发情景：如果确定Activity会被销毁，比如用户按下了返回键，或者调用了finish()方法销毁activity，则onSaveInstanceState不会被调用。
    1、当用户按下HOME键时
    2、屏幕被关闭时。
    3、从当前activity启动一个新的activity时。
    4、屏幕方向切换时。
    相关原理：
    最终得到一个Parcelable对象，保存到SparseArray中，这里的哈希表，是以View的ID为key的。因此如果View没有设置ID，onSaveInstanceState也就不会被回调了。

    回调时机：在onStart之后被回调，也就是 onStart --> onRestoreInstanceState --> onResume
    触发情景：只有在activity确实是被系统回收，重新创建activity的情况下才会被调用，此时参数savedInstanceState一定不为null，在onStart方法之后执行，如果在onRestoreInstanceState中读取savedInstanceState一定有值，如果在onCreate中读取Bundle存储的信息是有可能为null的。

    6. onSaveInstanceState()与onPause()的区别？
    onSaveInstanceState()适用于对临时性状态的保存，而onPause()适用于对数据的持久化保存。

    5. 为什么用Activity代替Fragment
        Fragment是一个片段，用来拼接或者组成Activity，比如ViewPager类似的显示，组件化
        生命周期比较难管理，一些Fragment动画，或者切换有一些原生的bug
        <https://github.com/hehonghui/android-tech-frontier/tree/master/androidweekly/Square%20%E5%BC%80%E6%BA%90%E5%BA%93Flow%E5%92%8CMortar%E7%9A%84%E4%BB%8B%E7%BB%8D>
        <https://www.zhihu.com/question/39662488>
        <https://www.zhihu.com/question/34917681>

    6. home键后，activity可能走的生命周期是什么
    onPause, onStop, onSaveInstance

    7. Activity的启动流程<https://blog.csdn.net/oheg2010/article/details/82826415> <https://www.jianshu.com/p/8a1a29073c44>
        1. Launcher响应点击事件(startActivity->Instrumentation通过binder通知AMS)
        2. AMS创建Activity Record，识别Flag和TaskAffinity，AMS暂停当前Activity
        3. AMS与zygote进行socket通信，然后zygote.fork一个新的进程（为了资源复用）
        4. 执行ActivityThread的static main，加载looper，创建ActivityThread对象，调用attach绑定到AMS中，通过ApplicationThread建立和AMS的通讯
        5. ApplicationThread再通过H和ActivityThread进行通讯，在ActivityThread中创建Instrumentation，Context，Application
        6. 通过Instrumentation创建Activity，通过attach方法中绑定ContextImp，以及创建和关联Windows，接着就是onCreate等生命周期

    7.1 Activity显示流程<https://www.jianshu.com/p/af6824588d9b>

    8. Activity启动的时候，有哪些线程会启动
        只有UI主线程会启动
        RenderThread 是在 Android Lollipop 中新加入的组件，负责承担一部分之前主线程的渲染工作，减轻主线程的负担

    9. 谈一谈Fragment的生命周期
        Fragment从创建到销毁整个生命周期中涉及到的方法依次为：onAttach()->onCreate()->onCreateView()->onActivityCreated()->onStart()->onResume()->onPause()->onStop()->onDestroyView()->onDestroy()->onDetach()，其中和Activity有不少名称相同作用相似的方法，而不同的方法有:
        onAttach()：当Fragment和Activity建立关联时调用
        onCreateView()：当Fragment创建视图时调用
        onActivityCreated()：当与Fragment相关联的Activity完成onCreate()之后调用
        onDestroyView()：在Fragment中的布局被移除时调用
        onDetach()：当Fragment和Activity解除关联时调用

    10. activity和application的context，startactivity有什么区别
        如果 Context is not an Activity，则通过 Context.startActivity() 的时候，必须加上一个 Intent.FLAG_ACTIVITY_NEW_TASK 标志。因为非 Activity环境启动Activity 的时候没有 Activity 栈，所以需要加上这个标志位新建一个栈。
        application的context生命周期是整个应用的生命周期，activity的生命周期只是activity

    11. activity和fragment startActivityForResult有什么区别
    __启动Activity之后，会在ActivityThread启动dispatchActivityResult，这个会优先回到当前的Activity，然后执行它的super，然后到fragment__
    fragment的startActivityForResult执行的是mHost的onStartActivityFromFragment，mHost是HostCallbacks，这个是FragmentActivity的内部类，所以执行的是Activity的startActivityFromFragment，然后再调用Activity类中的startActivityForResult()，且会修改requestCode
    FragmentActivity类中的startActivityForResult()，直接调用Activity类中的startActivityForResult()

    12. a启动b启动c，b死掉了，怎么把c的result传回a
    FLAG_ACTIVITY_FORWARD_RESULT，这个flag不能和startActivityForResult一起使用

    13. 同时启动两个Activity: TaskStackBuilder, startActivitys(Intent [] intents)

    14. 给Fragment传参数的时候，为什么要用arguments，而不用方法直接调用呢？
        在修改配置引起的重绘中，如果用arguments系统会帮忙保存，而直接方法调用就可能会丢失

## 五、SharePreference
    存储的是一个xml文件。使用一个SP Map（以文件名为key，SP为值）来缓冲SP的文件。然后SP里面维护一个map，存储的SP里面的键值。
    写数据：commit是会阻塞当前线程。apply会启动一个Runnable异步提交。

    SP 的读写操作是线程安全的，它对 mMap 的读写操作用的是同一把锁。SharedPreferences文件的写入是全量写入，即使只是修改了其中一条key-value，也会执行全部的写入操作，因为SharedPreferences只能用于存储体积较小的数据，太大了就容易引发OOM，同时如果需要修改多条数据，必须使用Editor来一次性完成修改再提交。

    MODE_MULTI_PROCESS:
    一个 prefs 实例通常有两种获得途径, 一个是第一次被 new 创建出来的, 这种方式会实际的读取磁盘文件。还一种是后续从缓存(sSharedPrefsCache) 中取出来了。
    1. 如果设置了flag，那么在读取文件的时候会去判断当前加载的内存的时间和大小是否相等，如果相等则不需要加载，如果不相等则需要加载。加载的时候，如果backup存在，则删除源文件，使用backup文件。那么当另外一个进程正在写入的时候，就会失去对象，不能正确写入，造成数据丢失。
    2. 如果没有设置flag，那么每个进程单独在写，因为每次commit/apply都会全写入，那么后面的写入就会把另外一个进程刚写进去的覆盖掉。

## 六、实现延时/定时任务的几种方式
    1. 普通线程sleep的方式实现定时任务
    2. Timer实现定时任务，可以控制TimerTask的启动和取消，第一次执行任务时可以指定delay的时间。
    3. ScheduledExecutorService实现定时任务
    4. Handler.postDelay实现定时任务
    5. AlarmManager实现精确定时操作
    6. JobScheduler定时循环，最小15分钟

# Android进阶
## 1. Exception
    1. try-catch-finally-return执行顺序
        1、不管是否有异常产生，finally块中代码都会执行；
        2、当try和catch中有return语句时，finally块仍然会执行；
        3、finally是在return后面的表达式运算后执行的，所以函数返回值是在finally执行前确定的。无论finally中的代码怎么样，返回的值都不会改变，仍然是之前return语句中保存的值；
        4、finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。
    2. 有哪些异常
        IOException
        SecurityException
        OutIndexException
        DeadObjectException
    3. Exception和Error
        都是继承于 Throwable
        Error类一般是指与虚拟机相关的问题，如系统崩溃，虚拟机错误，内存空间不足，方法调用栈溢出等。如java.lang.StackOverFlowError和Java.lang.OutOfMemoryError。对于这类错误，Java编译器不去检查他们。对于这类错误的导致的应用程序中断，仅靠程序本身无法恢复和预防，遇到这样的错误，建议让程序终止。
        Exception类表示程序可以处理的异常，可以捕获且可能恢复。遇到这类异常，应该尽可能处理异常，使程序恢复运行，而不应该随意终止异常。
    4. Java中Throwable是所有异常和错误的超类，两个直接子类是Error（错误）和Exception（异常）
        1）Error是程序无法处理的错误，由JVM产生和抛出
        如OOM、ThreadDeath等。这些异常发生时，JVM一般会选择终止程序。
        2）Exception是程序本身可以处理的异常
        又分为运行时异常(RuntimeException)(也叫Checked Eception)和非运行时异常(不检查异常Unchecked Exception)。
        运行时异常
        有NullPointerException\IndexOutOfBoundsException等，这些异常一般是由程序逻辑错误引起的，应尽可能避免。
        非运行时异常
        有IOException\SQLException\FileNotFoundException以及由用户自定义的Exception异常等。

## 2. touch事件的分发过程（触摸反馈）
    从Activity的dispatchTouchEvent切入<https://blog.csdn.net/carson_ho/article/details/54136311> (说的更清楚一些)<https://juejin.im/entry/58df5b33570c35005798493c>
    ```Java
    Activity.dispatchTouchEvent() {
        if (ViewGroup.dispatchTouchEvent) {
            return true;
        }
        return onTouctEvent();
    }
    ```
    <https://www.jianshu.com/p/fc0590afb1bf>
    dispatchTouchEvent->OnInterceptTouchEvent->onTouch->onTouchEvent->performClick->onClick(Action.UP)
    如果dispatchTouchEvent直接return，则执行父节点的onTouch事件（源码解释：因为父节点会去执行子节点的dispatchTouchEvent，如果return false，那么父节点的mFirstTouchTarget=null，则执行父节点的ouTouch，如果return true，则不执行onTouch，事件结束）
    如果OnInterceptTouchEvent返回ture，则拦截事件，执行自己的onTouch
    如果onTouch返回ture，则直接消费事件，且不再传递
    OnInterceptTouchEvent从父节点到子节点，onTouch从子节点到父节点，只要有一个地方返回true，即被消耗。

    怎么处理滑动冲突
    滑动冲突主要有两种解决方式：外部拦截法和内部拦截法
    <https://juejin.im/post/5c9181425188252d9b375d39>
    <https://www.jianshu.com/p/982a83271327>
    <https://www.jianshu.com/p/8c635cb59fdf>

    OnClick
    事件监听机制
    内部类形式：将事件监听器定义成当前类的内部类，因为监听器是内部类，所以可以自由访问外部类的所有界面组件，这也是内部类的优势。
    匿名内部类形式：使用匿名内部类创建事件监听器对象。代码简单的情况，只能用一次。

    MotionEvent.ACTION_DOWN 按下View（所有事件的开始）
    MotionEvent.ACTION_MOVE 滑动View
    MotionEvent.ACTION_UP   抬起View（与DOWN对应）
    MotionEvent.ACTION_CANCEL   结束事件（非人为原因）(父节点拦截了up或者move事件)
    谁的onTouchEvent消费了DOWN事件，则MOVE和UP都会通过dispatch下发到该View，让他继续处理

## 3. measure/layout/draw的过程
    1. 为什么不可以在draw里面不断的new paint
    因为不断的new对象会触发GC，频繁GC会造成内存抖动，影响系统性能

## 4. requestLayout、invalidate与postInvalidate(16.6ms会去刷新一次屏幕，也就是我们常说的60fpx)
    <https://blog.csdn.net/litefish/article/details/52859300>
    如果子View调用了requestLayout这个方法，其实会从View树重新进行一次测量、布局、绘制这三个流程，最终就会显示子View的最终情况。如果layout中发现ltrb发生了变化，就会重绘。
    当子View调用了invalidate方法后，会为该View添加一个标记位，同时不断向父容器请求刷新，父容器通过计算得出自身需要重绘的区域，直到传递到ViewRootImpl中，最终触发performTraversals方法，进行开始View树重绘流程(只绘制需要重绘的视图)。
    postInvalidate这个方法与invalidate方法的作用是一样的，都是使View树重绘，但两者的使用条件不同，postInvalidate是在非UI线程中调用，invalidate则是在UI线程中调用。通过handler进行调用。
    三者不是调用之后立刻触发绘制,都是通过scheduleTraversals, 注册屏幕刷新信号事件，从而开始绘制。
    交换的频率也是60次/秒，这就与屏幕的刷新频率保持了同步。

    绘制：
        在屏幕刷新中，Android系统引入了双缓冲机制。
        GPU只向Back Buffer中写入绘制数据，且GPU会定期交换Back Buffer和Frame Buffer，也就是让Back Buffer 变成Frame Buffer交给屏幕进行绘制，让原先的Frame Buffer变成Back Buffer进行数据写入。
    丢帧：
        当你的应用正在往Back Buffer中填充数据时，系统会将Back Buffer锁定。如果到了GPU交换两个Buffer的时间点，你的应用还在往Back Buffer中填充数据，GPU会发现Back Buffer被锁定了，它会放弃这次交换。
        这样做的后果就是手机屏幕仍然显示原先的图像，这就是我们常常说的丢帧，所以为了避免丢帧的发生，我们就要尽量减少布局层级，减少不必要的View的invalidate调用，减少大量对象的创建（GC也会占用CPU时间）等等。
    Choreographer
        Choreographer 的引入，主要是配合 Vsync ，给上层 App 的渲染提供一个稳定的 Message 处理的时机，也就是 Vsync 到来的时候 ，系统通过对 Vsync 信号周期的调整，来控制每一帧绘制操作的时机. 目前大部分手机都是 60Hz 的刷新率，也就是 16.6ms 刷新一次，系统为了配合屏幕的刷新频率，将 Vsync 的周期也设置为 16.6 ms，每个 16.6 ms ， Vsync 信号唤醒 Choreographer 来做 App 的绘制操作 ，这就是引入 Choreographer 的主要作用
        通过跳帧，尽可能的修正时间，保证doFrame和Vsync同步节奏

    同步消息屏障
        当 next() 方法在取 Message 时发现队头是一个同步屏障的消息时，就会去遍历整个队列，只寻找设置了异步标志的消息，如果有找到异步消息，那么就取出这个异步消息来执行，否则就让 next() 方法陷入阻塞状态。直到屏障被移除。
        为了提高消息处理的优先级，优先处理异步消息。
        Android应用框架中为了更快的响应UI刷新事件在ViewRootImpl.scheduleTraversals中使用了同步屏障

    垂直同步
        垂直同步的含义我们可以理解为，使得显卡生成帧的速度和屏幕刷新的速度的保持一致。
        不幸的是，刷新频率和帧率并不是总能够保持相同的节奏。如果发生帧率与刷新频率不一致的情况，就会容易出现Tearing的现象(画面上下两部分显示内容发生断裂，来自不同的两帧数据发生重叠)。

## 4. 动画相关<https://www.androidmst.com/993.html>
    1. 帧动画：AnimationDrawable，帧动画，由一组图片集合而成，是一种具有动画效果的图片资源，对应的xml标签是animation-list。利用xml布局加载
    通过配置图片，进行动态播放

    2. View动画（补间动画）（animation）：主要是向View对象设置动画效果，包括AlphaAnimation 、RotateAnimation 、ScaleAnimation 、TranslateAnimation 这4种效果，对应的xml标签分别是alpha、rotate、scale、translate。
    使用：
        1、设置相应动画效果的起点值、终点值、duration（时长）、Interpolator（加速度）。（注：RotateAnimation还需要设置旋转中心坐标值）
        2、View.startAnimation(animation)。
    Interpolator：插值器，随时间流逝动画路程所应走过的百分比。比如，若设置为LinearInterpolator（匀速），时间过一半则插值器的值是0.5；若设置的是DecelerateInterpolator（加速插值器），时间过一半则插值器的值大于0.5。
    是父容器不断的绘制view,看起来像移动了效果,其实view没有变化,还在原地
    Android 补间动画就是通过ParentView来不断调整ChildView的画布坐标系来实现的，在ParentView的dispatchDraw方法会被调用。
    __补间动画TranslateAnimation,View位置移动了，可是点击区域还在原来的位置，为什么？__<https://juejin.im/post/5c3814ea6fb9a049d519b5ba>
    View动画改变的只是View的显示，而没有改变View的响应区域；Animation产生的动画数据实际并不是应用在View本身的，而是应用在RenderNode或者Canvas上的，这就是为什么Animation不会改变View的属性的根本所在。

    3. 属性动画（animator）：属性动画可以对任何对象的属性做动画而不仅仅是View，甚至可以没有对象。除了作用对象进行扩展外，属性动画的效果也加强了，不仅能实现View动画的4中效果，还能实现其它多种效果，这些效果都是通过ValuAnimator或ObjectAnimator、AnimatorSet等来实现的。
    使用：
        1、设置作用对象、属性的起点值、属性的终点值、TypeEvaluator（路线，类型估值）、duration（时长）、TimeInterpolator（加速度，时间插值器）。
        2、animator.start()。
    是通过不断改变自己view的属性值,真正的改变view。
    https://www.jianshu.com/p/46f48f1b98a9
    实现机制：通过setValue()找到对应的属性值，然后去set

    源码分析: <https://www.jianshu.com/p/4af641bea24d>

## 5. RecyclerView和ListView的区别
    (简单对比)<https://blog.csdn.net/shu_lance/article/details/79566189>
    ListView采用的是RecyclerBin的回收机制在一些轻量级的List显示时效率更高
    RecycleBin 机制的实现原理：

    1. 固有的 ViewHolder 模式规范
    RecyclerView.Adapter 默认采用 ViewHolder 模式，减少 findViewById() 方法获取控件实例的调用次数。

    2. 使用 LayoutManager 支持多种布局方式
    RecyclerView 借助 LayoutManager 能够灵活地将列表控件放入不同的容器（LinearLayout, GridLayout）。
    ListView 布局只能实现纵向排列，而 RecyclerView 将排列工作 setLayoutManager() 交给 LayoutManager 布局排列接口，因此可以定制出不同排列方式（横向、瀑布流布局）。

    3. 通知 Adapter 的数据变化更加灵活
    不仅 notifyDataSetChange() 方法，RecyclerView 可以使用 notifyItemRangeChanged() 等方法实现局部更新数据并重绘视图。

    4. 子项视图的动画效果更容易实现
    RecyclerView.ItemAnimator
    RecyclerView.ItemDecoration

    5. 空数据处理：
    在ListView中有个setEmptyView() 用来处理Adapter中数据为空的情况；但是在RecyclerView中没有这个API，所以在RecyclerView中需要进行一些数据判断来实现数据为空的情况；

    6. HeaderView 与 FooterView：
    在ListView中可以通过addHeaderView() 与 addFooterView()来添加头部item与底部item，来当我们需要实现下拉刷新或者上拉加载的情况；而且这两个API不会影响Adapter的编写；
    但是RecyclerView中并没有这两个API，所以当我们需要在RecyclerView添加头部item或者底部item的时候，我们可以在Adapter中自己编写，根据ViewHolder的Type与View来实现自己的Header，Footter与普通的item，但是这样就会影响到Adapter的数据，比如position，添加了Header与Footter后，实际的position将大于数据的position；

    7. 缓存机制对比
    ListView只有2级缓存，即mActiveViews和mScrapViews，每次读mScrapViews，都会去getView
    RecyclerView4级缓存，mAttachedScrap，mCacheViews，mViewCacheExtension由用户自定义，mRecyclerPool，每次读取mAttachedScrap，都会直接使用View。从pool中拿出来的会重新bindView

    8. 效率对比
    ListView实现起来更加简洁方便，适用于简单的列表
    RecyclerView适合大数据，和布局比较复杂的列表(LayoutManager), 局部刷新, 动画效果，能快速重用，频繁更新

    https://mp.weixin.qq.com/s/auphzaQF6_wJx6dGFY6niA<简单讲了一下各个cache的作用>
    https://blog.csdn.net/tangedegushi/article/details/88899398<缓存实现原理>

    9. ListView优化
        ListView 的核心原理就是重用 View。
        让 ItemView 的 Layout 层次结构简单
        善用自定义 View，自定义 View 可以有效的减小 Layout 的层级
        getView() 中要做尽量少的事情，不要有耗时的操作，因为用户快速滑动列表时，会大量调用 getView ，而 getView 是在主线程中被调用的。
        findViewById 的实现是遍历，如果你定义的 View 越复杂代价越大。
        getView()不能频繁的去new 对象
        尽量能保证 Adapter 的 hasStableIds() 返回 true，这样在 notifyDataSetChanged() 的时候，如果 id 不变，ListView 将不会重新绘制这个 View，达到优化的目的
        利用好 View Type，例如你的 ListView 中有几个类型的 Item，需要给每个类型创建不同的 View，这样有利于 ListView 的回收，当然类型不能太多

## 6. LnearLayout和RelativeLayout
    1. RelativeLayout会对子View做两次measure。这是由于RelativeLayout是基于相对位置的，而且子View会在横向和纵向两个方向上分布，因此，需要在横向和纵向分别进行一次measure过程
    2. 而LinearLayout只进行纵向或横向的测量，所以measure的时间会比RelativeLayout少很多。但是如果设置了weight,在测量的过程中，LinearLayout会将设置过weight的和没设置的分别测量一次，这样就导致measure两次。

## 7. ConstraintLayout和RelativeLayout
    减少嵌套，通过约束来设置布局，不需要层层嵌套
    缺点：
    xml文件没有布局层次，可读性差
    代码布局复杂性高
    约束条件复杂时排查难度大

## 7. View的层级是怎样的
    DecorView->LinearLayout(通知栏，标题栏，内容显示)->content->activity_main

## 8. surface和window
    WindowManager.addView(contentView)最终会交给WindowManagerService处理，WindowManagerService会创建这个contentView相关的UI载体Surface。

    Surface是管理显示内容的数据（implements Parcelable），包括存储于数据的交换。而SurfaceView就是把这些数据显示出来到屏幕上面。
    概念：一个Surface就是一个对象，该对象持有一群像素（pixels），这些像素是要被组合到一起显示到屏幕上的。你在手机屏幕上看到的每一个window（如对话框、全屏的activity、状态栏）都有唯一一个自己的surface，window将自己的内容（content）绘制到该surface中。Surface Flinger根据各个surface在Z轴上的顺序（Z-order）将它们渲染到最终的显示屏上。

    Surface原理
    1. SurfaceFlinger服务,它的主要作用就是被Android应用程序调用，把绘制(测量，布局，绘制)后的窗口(Surface)渲染到手机屏幕上。
    2. Surface存储着当前窗口的像素数据

    SurfaceView
    1. SurfaceView提供了一个专门用于绘制的surface
    2. 不跟activity一起玩
    3. 可以自定义自己的绘制线程

    2. window和phoneWindow
        PhoneWindow是Android提供的Window唯一实现类。
        每一个Activity都包含一个Window对象(dialog，toast 等也是新添加的window对象)
        Window是在Activity attach的时候创建的
        调用Activity的setContentView，实际上用的是window.setContentView，在里面会创建DecorView(Activity的会，但是AppCompactActivity不是这个逻辑)
        在AppCompactActivity中，使用的是AppCompatDelegateImp代理，在这里面再调用Window的getDecorView来创建DecordView

    3.window机制探索源码阅读(https://blog.csdn.net/qian520ao/article/details/78555397)

## 8. Factory
    LayoutInflater.Factory的意义：通过 LayoutInflater 创建 View 时候的一个回调，可以通过 LayoutInflater.Factory 来改造或定制创建 View 的过程。
    LayoutInflater.setFactory 使用注意：不能在 super.onCreate 之后设置。
    AppCompatActivity 为什么 setFactory ？向下兼容新版本中的效果。

    Factory2增加了 parent的参数，可以为View配置新的parent

## 9. JetPark的使用
    Foundation(基础组件)：
    Android KTX：Android KTX 是一组 Kotlin 扩展程序，它优化了供Kotlin使用的Jetpack和Android平台的API。以更简洁、更愉悦、更惯用的方式使用Kotlin进行Android开发。
    AppCompat：提供了一系列以AppCompat开头的API，以便兼容低版本的Android开发。
    Cars(Auto)：有助于开发 Android Auto 应用的组件，无需担心特定于车辆的硬件差异（如屏幕分辨率、软件界面、旋钮和触摸式控件）。
    Benchmark(检测)：从 Android Studio 中快速对基于 Kotlin 或 Java 的代码进行基准化分析。衡量代码性能，并将基准化分析结果输出到 Android Studio 控制台。
    Multidex(多Dex处理)：为方法数超过 64K 的应用启用多 dex 文件。
    Security(安全)：按照安全最佳做法读写加密文件和共享偏好设置。
    Test(测试)：用于单元和运行时界面测试的 Android 测试框架。
    TV：构建可让用户在大屏幕上体验沉浸式内容的应用。
    Wear OS：有助于开发 Wear 应用的组件。

    Architecture(架构组件)：
    Data Binding(数据绑定)：数据绑定库是一种支持库，借助该库，可以使用声明式将布局中的界面组件绑定到应用中的数据源。
    Lifecycles：方便管理 Activity 和 Fragment 生命周期，帮助开发者书写更轻量、易于维护的代码。
    LiveData：是一个可观察的数据持有者类。与常规observable不同，LiveData是有生命周期感知的。
    Navigation：处理应用内导航所需的一切。
    Paging：帮助开发者一次加载和显示小块数据。按需加载部分数据可减少网络带宽和系统资源的使用。
    Room：Room持久性库在SQLite上提供了一个抽象层，帮助开发者更友好、流畅的访问SQLite数据库。
    ViewModel：以生命周期感知的方式存储和管理与UI相关的数据。
    WorkManager：即使应用程序退出或设备重新启动，也可以轻松地调度预期将要运行的可延迟异步任务。

    Behavior(行为)：
    CameraX：帮助开发者简化相机应用的开发工作。它提供一致且易于使用的 API 界面，适用于大多数 Android 设备，并可向后兼容至 Android 5.0（API 级别 21）。
    DownloadManager(下载管理器)：可处理长时间运行的HTTP下载，并在出现故障或在连接更改和系统重新启动后重试下载。
    Media & playback(媒体&播放)：用于媒体播放和路由（包括 Google Cast）的向后兼容 API。
    Notifications(通知)：提供向后兼容的通知 API，支持 Wear 和 Auto。
    Permissions(权限)：用于检查和请求应用权限的兼容性 API。
    Preferences(偏好设置)：提供了用户能够改变应用的功能和行为能力。
    Sharing(共享)：提供适合应用操作栏的共享操作。
    Slices(切片)：创建可在应用外部显示应用数据的灵活界面元素。

    UI(界面组件)：
    Animation & Transitions(动画&过度)：提供各类内置动画，也可以自定义动画效果。
    Emoji(表情符号)：使用户在未更新系统版本的情况下也可以使用表情符号。
    Fragment：组件化界面的基本单位。
    Layout(布局)：xml书写的界面布局或者使用Compose完成的界面。
    Palette(调色板)：从调色板中提取出有用的信息。

## 10. LifeCycler的原理是什么
    Registry, Owner, Observer
    fragment中创建了LifecycleRegistry对象，在fragment的生命周期中直接调用了LifecycleRegistry的handleLifecycleEvent方法，这样就将生命周期通知到了Lifecycle。
    通过FragmentManager切换Fragment生命周期的时候通过handleLifecycleEvent
    Activity是通过一个ReportFragment更新周期
    使用ObserverWithState 来保存更新状态
    https://blog.csdn.net/hyc1988107/article/details/81129744

    是怎么运用运行时注解和反射的
    基于注解生成了_LifecycleAdapter 的class，通过反射生成_LifecycleAdapter对象

## 11. 你知道哪些设计模式？<https://www.jianshu.com/p/1ffa92ae4728> <https://www.cnblogs.com/pony1223/p/7608955.html> <https://juejin.im/post/5c8756e6e51d456cda2e7ff1>
    创建型模式, 结构型模式, 行为型模式

    单例模式
        1.  构造函数一般为private
        2.  通过一个静态方法或者枚举返回单例类对象
        3.  饿汉模式：
        //声明静态对象时已经初始化。
        private static final Class sClass = new Class();
        public static Class getClass() {return sClass;}
        4.  懒汉模式：
        //使用时，才初始化变量 多线程中保证单例对象唯一性的手段。但是，每次都要进行同步，消耗不必要的资源。一般不建议使用。
        ```Java
        private static Class sClass;
        public static synchronized Class getClass() {
            if (sClass == null ) {
                sClass = new Class();
            }
            return sClass;
        }
        ```

        5. DCL模式：
        ```Java
        private static Class sClass = null;
        public static Class getClass() {
            if (sClass == null) {
                synchronized (Class.class) {
                    if (sClass == null) {
                        sClass = new Class();
                    }
                }
            }
            return sClass;
        } //资源利用率高，第一次加载时，反应稍慢，由于Java内存模型的原因偶尔会失败。
        ```

        6.  静态内部类模式：
        // 确保线程安全，保证单例唯一，延迟单例的实例化。推荐使用的单例模式。
        public static Class getClass() { return ClassHolder.sClass; }
        private static Class ClassHolder { private static final Class sClass = new Class(); }
        7.  枚举单例
        8.  容器实现单例，用例：安卓Context的实例化

        缺点：
        1. 一般没有接口，扩展只能通过修改代码的方式。（但是可以被继承）
        2. 如果单例对象持有Context，容易引发内存泄漏。
    WindowManager wm = (WindowManager)getSystemService(getApplication().WINDOW_SERVICE);

    Builder模式
    链式调用，build dialog

    原型模式
    深拷贝和浅拷贝

    工厂模式
    抽象类，具体类的实现，一个抽象工厂类，一个抽象产品类。一个具体工厂继承抽象工厂，用来生产具体产品类。
    比如，Activity的onCreate方法，就是一个工厂方法，生产View。
    其实，在getSystemService方法中就是用到了工厂模式，他就是根据传入的参数决定创建哪个对象，当然了，由于返回的都是以单例模式存在的对象，因此不用new了，直接把单例返回就好。

    策略模式
    策略模式定义了一系列的算法，并将每一个算法封装起来，而且使它们还可以相互替换。策略模式让算法独立于使用它的客户而独立变化，使算法自身更具灵活性。
    先将系列算法抽象出一个接口，每个具体算法实现这个接口，在策略使用类中，声明接口类，根据不同情况调用具体的算法类。
    在属性动画中使用时间插值器的时候就用到了。

    观察者模式
    Broadcast
    EventBus

    代理模式
    为其他类提供一种代理以控制这个对象的访问。
    AIDL

    组合模式
    将对象组成成树形结构，以表示“部分-整体”的层次结构，使得用户对单个对象和组合对象的使用具有一致性。
    Android中View的结构是树形结构，每个ViewGroup包含一系列的View，而ViewGroup本身又是View。这是Android中非常典型的组合模式。

    适配器模式
    Adapter
    像转换插，将两个不匹配的头通过中间层的实现，连接在一起
    ListView adapter data

    装饰模式
    动态的给一个对象添加额外的智者，就增加功能来说，装饰模式比子类继承的方式更灵活。
    实际上Java 的I/O API就是使用Decorator实现的，I/O变种很多，如果都采取继承方法，将会产生很多子类，显然相当繁琐。

    1 单一职责原则
    单一原则很简单，就是将一组相关性很高的函数、数据封装到一个类中。换句话说，一个类应该有职责单一。
    2 开闭原则
    开闭原则理解起来也不复杂，就是一个类应该对于扩展是开放的，但是对于修改是封闭的。我们知道，在开放的app或者是系统中，经常需要升级、维护等，这就要对原来的代码进行修改，可是修改时容易破坏原有的系统，甚至带来一些新的难以发现的BUG。因此，我们在一开始编写代码时，就应该注意尽量通过扩展的方式实现新的功能，而不是通过修改已有的代码实现。
    3 里氏替换原则
    里氏替换原则的定义为：所有引用基类的地方必须能透明地使用其子类对象。定义看起来很抽象，其实，很容易理解，本质上就是说，要好好利用继承和多态。简单地说，就是以父类的形式声明的变量（或形参），赋值为任何继承于这个父类的子类后不影响程序的执行。看一组代码你就明白这个原则了：
    ```
    //窗口类
    public class Window(){
        public void show(View child){
            child.draw();
        }
    }
    public abstract class View(){
        public abstract void draw();
        public void measure(int widht,int height){
            //测量视图大小
        }
    }
    public class Button extends View{
        public void draw(){
            //绘制按钮
        }
    }
    public class TextView extends View{
        public void draw(){
            //绘制文本
        }
    }
    ```
    Window 类中show函数需要传入View，并且调用View对象的draw函数。而每个继承于View的子对象都有draw的实现，不存在继承于View但是却没实现draw函数的子类（abstract方法必须实现）。我们在抽象类设计之时就运用到了里氏替换原则。
    4 依赖倒置原则
    依赖倒置主要是实现解耦，使得高层次的模块不依赖于低层次模块的具体实现细节。怎么去理解它呢，我们需要知道几个关键点：
    （1）高层模块不应该依赖底层模块（具体实现），二者都应该依赖其抽象（抽象类或接口）
    （2）抽象不应该依赖细节（废话，抽象类跟接口肯定不依赖具体的实现了）
    （3）细节应该依赖于抽象（同样废话，具体实现类肯定要依赖其继承的抽象类或接口）
    其实，在我们用的Java语言中，抽象就是指接口或者抽象类，二者都是不能直接被实例化；细节就是实现类，实现接口或者继承抽象类而产生的类，就是细节。使用Java语言描述就简单了：就是各个模块之间相互传递的参数声明为抽象类型，而不是声明为具体的实现类；
    5 接口隔离原则
    接口隔离原则定义：类之间的依赖关系应该建立在最小的接口上。其原则是将非常庞大的、臃肿的接口拆分成更小的更具体的接口。
    6 迪米特原则
    描述的原则：一个对象应该对其他的对象有最少的了解。什么意思呢？就是说一个类应该对自己调用的类知道的最少。还是不懂？其实简单来说：假设类A实现了某个功能，类B需要调用类A的去执行这个功能，那么类A应该只暴露一个函数给类B，这个函数表示是实现这个功能的函数，而不是让类A把实现这个功能的所有细分的函数暴露给B。

## 12. MVVM, MVP, MVC
    M就是Model层，负责提供数据模型
    V就是View层，就是我们的xml布局文件和Activity，负责界面相关的操作，比如显示弹窗，显示Toast，给控件赋值，跳转界面之类的
    C就是Controller层，也就是俗称的控制层，主要是用来处理业务逻辑的
    C->M->V<-C

    Model：用来处理业务逻辑
    View：跟mvc一样，用来处理view相关的逻辑
    Presenter：协调者，主要是用来协调model和view之间的联系
    V<->P<->M

    C比P要重，且M和V解耦了，不再相互依赖
    M和P可以用来写单元测试

    MVVM（数据驱动的架构）
    model：数据业务
    view：视图UI
    view_model:提供数据源, 处理业务逻辑
    通过LiveData/DataBinding实现ViewModel和View之间的通信（观察者ViewModel更新之后，直接更新View）
    V->VM<->M

    https://yuzhiqiang.blog.csdn.net/article/details/79082234
    https://yuzhiqiang.blog.csdn.net/article/details/98848936

## 13. Serializable 和 Parcelable
    序列化就是把实体对象状态按照一定的格式写入到有序字节流，反序列化就是从有序字节流重建对象，恢复对象状态。

    Serializable（Java自带）：
    Serializable是序列化的意思，表示将一个对象转换成可存储或可传输的状态。序列化后的对象可以在网络上进行传输，也可以存储到本地。
    数据持久化。

    Parcelable（android 专用）：
    除了Serializable之外，使用Parcelable也可以实现相同的效果，
    不过不同于将对象进行序列化，Parcelable方式的实现原理是将一个完整的对象进行分解，
    而分解后的每一部分都是Intent所支持的数据类型，这样也就实现传递对象的功能了。
    只写在内存中的字节流。
    因为序列化和反序列化依赖代码实现，如果出现版本不一致，就会导致读取的数据有问题。

    选择序列化方法的原则
    1）在使用内存的时候，Parcelable比Serializable性能高，所以推荐使用Parcelable。
    2）Serializable在序列化的时候会产生大量的临时变量，从而引起频繁的GC。
    3）Parcelable不能使用在要将数据存储在磁盘上的情况，因为Parcelable不能很好的保证数据的持续性在外界有变化的情况下。尽管Serializable效率低点，但此时还是建议使用Serializable 。

    应用场景
    需要在多个部件(Activity或Service)之间通过Intent传递一些数据，简单类型（如：数字、字符串）的可以直接放入Intent。复杂类型必须实现Parcelable接口。

    Serializable 和Parcelable的对比
    android上应该尽量采用Parcelable，效率至上
    编码上：
    Serializable代码量少，写起来方便
    Parcelable代码多一些
    效率上：
    Parcelable的速度比高十倍以上

    serializable的迷人之处在于你只需要对某个类以及它的属性实现Serializable 接口即可。Serializable 接口是一种标识接口（marker interface），这意味着无需实现方法，Java便会对这个对象进行高效的序列化操作。
    这种方法的缺点是使用了反射，序列化的过程较慢。这种机制会在序列化的时候创建许多的临时对象，容易触发垃圾回收。
    Parcelable方式的实现原理是将一个完整的对象进行分解，而分解后的每一部分都是Intent所支持的数据类型，这样也就实现传递对象的功能了

## 13. 性能分析工具，SystemTrace（分析启动速度，滑动速度），TraceView，Leakcanary（内存泄漏），Profile（对MMS不起作用）

## 14. android插件化
    模块化设计（抽出Interface）
    组件化设计（无任何依赖）SPI，路由，EventBus
    插件化

## 15. APK打包流程
    1. 通过aapt打包res资源文件，生成R.java、resources.arsc和res文件
    2. 处理.aidl文件，生成对应的Java接口文件
    3. 通过Java Compiler编译R.java、Java接口文件、Java源文件，生成.class文件
    4. 通过dex命令，将.class文件和第三方库中的.class文件处理生成classes.dex
    5. 通过apkbuilder工具，将aapt生成的resources.arsc和res文件、assets文件和classes.dex一起打包生成apk
    6. 通过Jarsigner工具，对上面的apk进行debug或release签名
    7. 过zipalign工具，对签名后的APK文件进行对齐处理(如果你发布的apk是正式版的话，就必须对APK进行对齐处理，用到的工具是zipalign，对齐的主要过程是将APK包中所有的资源文件距离文件起始偏移为4字节整数倍，这样通过内存映射访问apk文件时的速度会更快。对齐的作用就是减少运行时内存的使用。)

## 16. AndroidQ的新特性
    1. 沙箱模式
    每个应用进程都相当于一个 Sandbox 沙箱，Android 通过对每一个应用分配一个 UID，注意这里的 UID 不同于 Linux 系统的 User ID，可以将每个应用理解为一个 User，只能对其目录下的内容具有访问和读写权限，这样就从根源上保护了其他应用程序，下图说明了其隔离效果。
    2. 后台启动Activity
    3. Location权限
    4. 权限限制访问：MAC地址，DeviceID
    5. non-SDK限制，反射限制
    6. preferece库废弃

    Android9.0（P）新特性
    室内WIFI定位
    “刘海”屏幕支持
    安全增强

    Android8.0（O）新特性
    优化通知：通知渠道 (Notification Channel) 通知标志 休眠 通知超时 通知设置 通知清除
    画中画模式：清单中Activity设置android:supportsPictureInPicture
    后台限制
    自动填充框架
    系统优化

    Android7.0新特性
    多窗口支持
    V2签名
    增强的Java8语言模式
    夜间模式

    Android6.0新特性
    动态权限管理
    支持快速充电的切换
    支持文件夹拖拽应用
    相机新增专业模式

    Android5.0新特性
    MaterialDesign设计风格
    支持64位ART虚拟机（5.0推出的ART虚拟机，在5.0之前都是Dalvik。他们的区别是：Dalvik,每次运行,字节码都需要通过即时编译器转换成机器码(JIT)。ART,第一次安装应用的时候,字节码就会预先编译成机器码(AOT)）
    通知详情可以用户自己设计

## 17. 为何在 Android 中 fork Zygote 进程如此高效了？
    Linux Kernel 采用了 Copy-On-Write 的技术，Copy-On-Write 的意思是只有在写的时候才单独复制一份，而读的时候不进行操作。 换而言之 fork zygote 实际上并未实际复制什么东西，只有在发生写操作时，才单独复制一份。而另一方面，class 和 Resource 资源文件并不需要重新写，这些文件在绝大多数时候都是只读的。最后，实际的效果就是尽管运行着多个 APP，但实际只有一份 class 和 resource 文件在Zygote 进程中。

# Android相关实践
## 1. 优化的方向有哪些
    1. 内存优化
        1. 减少OOM（内存泄漏，Bitmap）
        2. 减少卡顿（绘制，刷新，内存抖动）原理是1.doFrame没有在vsync信号内完成绘制，2.doFrame耗时太长，绘制不完
            1. UI生命周期的控制 系统时间的处理 消息处理 界面布局 界面绘制 界面刷新
            2. 内存抖动，避免在短时间内创建很多对象，然后又销毁掉，频繁触发GC
        3. 减少内存占用（自动装箱 内存复用 最优的数据模型 枚举类型 图片内存优化 字符串拼接 不要在绘制周期去刷新UI）
        4. 减少程序异常（）
    2. 启动速度
        1. 启动会有白屏，是因为在主题中设置了windowBackground，如果应用没有绘制，就会出现白屏
        2. 有一个Splash启动页（广告页）通过监听windowChangeFocus或者ViewTreeObserver切换主页面
        3. 描述APP启动流程，fork->ActivityThread->创建application实例，执行attachBaseContext、onCreate，不能在application中做耗时操作
        4. 通过systrace和traceView来确定函数执行的时间和耗时的地方
        5. 异步，懒加载，IntentService
        6. Application启动完成之后，就开始启动Activity，走onCreate, onStart, onResume
        7. 因为setContentView读取XML，初始化View耗时，减少布局使用ViewStub
        8. 在onResume中请求JNI刷新，然后绘制DecorView
        9. 将DecorView添加到Window，交于SurfaceFlinger绘制
        9. 绘制过程中会对View进行onMeasure, onLayout, onDraw
        10. 减少耗时，和GC操作
        11. 减少了400ms的启动时间，从1500ms到1100ms
    3. 功耗（使用JobService，布局优化，内存优化，避免后台操作，心跳机制）
    4. 保证APP稳定运行：内存，代码质量，Crash，ANR，后台保活，
    5. 网络优化（能用IP尽量用IP（DNS），服务器部署）
    6. 存储方式：SP（第一次读取会耗费时间，尽量使用apply，不要频繁的提交），数据库（事务，触发器，索引，异步线程）
    7. 日志优化
    8. 体积优化（图片转成SVG，去除多语言，去除SO，去除无用资源，开混淆，使用shinkResource，离线包下载，分包编译）

## 2. pss内存
    应用实际运行占用内存
    https://blog.csdn.net/berber78/article/details/47819139


## 3. 应用保活的方式有哪些
    1. Service互相拉起，JobScheduler拉起
    2. 利用1像素Activity提升进程优先级，监听解锁屏广播，将进程拉到前台
    3. 利用Notification提升进程优先级
    4. 利用系统广播拉活
    5. 利用第三方应用广播拉活
    6. 利用账号同步机制拉活

## 4. 应用如何建立自己的长链接
    1. 长连接方式：
        1. 一种是定时去server查询数据，通常是使用HTTP协议来访问web服务器，称Polling（轮询）；
        2. 还有一种是移动端和服务器建立长连接，使用XMPP长连接，称Push（推送）。（XMPP是基于XML，因此具有良好的扩展性的协议，冗余，成本高）
        3. 基于TCP的长链接（使用三方库google的protobuf），
    2. 长链接注意事项：（https://blog.csdn.net/carson_ho/article/details/79522975）
        1. 长连接独立进程
        2. 进程保活
        3. 心跳机制
        4. 断开重连
        创建Receiver，监控网络状态，如果网络发生切换则立即重连；
        监控服务端心跳包回包，如果连续5次没有收到回包，则认为长连接已经失效；
        设置心跳包超时限制，如果超过时间还没有收到心跳回包，则重连，这种方式比较耗电；
        等socket IO异常抛出，不过耗时太长，需要15s左右才能发现。

    3. 推送协议
        使用XMPP协议（Openfire + Spark + Smack）
        简介：基于XML协议的通讯协议，前身是Jabber，目前已由IETF国际标准化组织完成了标准化工作。
        优点：协议成熟、强大、可扩展性强、目前主要应用于许多聊天系统中，且已有开源的Java版的开发实例androidpn。
        缺点：协议较复杂、冗余（基于XML）、费流量、费电，部署硬件成本高。

        使用MQTT协议
        简介：轻量级的、基于代理的“发布/订阅”模式的消息传输协议。
        优点：协议简洁、小巧、可扩展性强、省流量、省电，目前已经应用到企业领域，且已有C++版的服务端组件rsmb。
        缺点：不够成熟、实现较复杂、服务端组件rsmb不开源，部署硬件成本较高。

## 5. 注解<https://blog.csdn.net/csdn_aiyang/article/details/81564408>
    运行时注解就是就是运行时运用反射，动态获取对象、属性、方法等，一般的IOC框架就是这样，可能会牺牲一点效率。
    编译时注解就是在程序编译时根据注解进行一些额外的操作，大名鼎鼎的ButterKnife运用的就是编译时注解，ButterKnife在我们编译时，就根据注解，自动生成了一些辅助类。要玩编译时注解呢，你得先依赖apt，r然后自己写一个类继承AbstractProcessor,重写process方法，在里面实现如何把配置或注解的信息变成所需要的类。

## 6. OAPM原理
    1. Activity/Fragment等生命周期对象泄漏检测
        知道对象的生命周期后对对象的GC的结果进行检测，Java中只有弱引用 引用的的对象会在GC时被回收，同时垃圾收集器会把引用加入ReferenceQueue，制定一定策略判断是否可能有内存泄漏，通过dump heap进行分析，可以确定是否真的泄漏和泄漏的对象的被引用的路径
    2. 低内存检测
        物理内存不足
        根据LowMemoryKiller在系统低内存时开始清理后台进程时，系统会调用后台进程的onLowMemory 的回调或者 onTrimMemory 回调且不同的标记位，加上 ActivityManager.MemoryInfo 的 lowMemory 标记来获得低内存的发生时机。
        虚拟内存不足
        超过虚拟内存的大小限制则直接触发 OOM 异常,当虚拟内存占用超过最大限制的 90% 时，认为进程低内存。
        实现：
        已用逻辑空间地址可以查看/proc/pid/status中的VmSize记录
    3. 内存使用曲线
        目标：
        每隔一段时间获取堆内存和进程内存的使用情况,从而获得随使用时间而变化的内存曲线.
        通过Runtime获取堆内存数据
        runtime.freeMemory runtime.maxMemory runtime.totalMemory
        系统内存数据
        ActivityManager.MemoryInfo
        进程内存数据信息
        ActivityManager.getProcessMemoryInfo
    4. Crash
        通过Thread.setDefaultUncaughtExceptionHandler(this)获取
    5. Cpu
        通过读取/proc/stat和/proc/pid/stat文件获取cpu数据进行计算
    6. 卡顿检测
        使用Looper的print机制，计算当前主线操作执行的时间来确定是否卡顿，实现时可通过watchdog实现，但是正确的堆栈数据比较难获取，通过多次获取MainThread的堆栈来增加准确性

## 7. 换肤的机制是如何实现的？（系统方面和应用方面）
    替换res的资源包
    OS层通过加载资源包达到换肤效果
    https://www.jianshu.com/p/ef64106ca1d3
    https://www.jianshu.com/p/ccd925994103
    https://www.jianshu.com/p/c2b1ae56381e

    资源id均是16进制数，前两位均是7f，代表包名，后边两位是类型，代表drawable的类型为02，代表layout的类型是03，最后四位是资源的真正编号。请注意，类型的编号是会变的，并不是02就一定代表drawable。
    资源匹配规则比较简单。资源匹配一共有18个维度，在匹配时，根据当前的配置，去掉与配置相冲突的选项（比如语言、方向），然后根据维度的优先级，选择最合适的资源
    ResouresManager->AssetManager->Resources
        1. 查看第6步，调用addAssetPath方法，参数即是apk的路径，因此可以根据路径查找到apk里的资源。特别强调此处，是因为addAssetPath这个方法太重要了，部分的动态加载框架，也要使用这个方法，添加动态库的路径，以实现不仅使用动态库的代码，还能使用动态库的资源。
        2. Resources最终在ResourcesManager类中完成初始化，而ResourcesManager使用了工厂模式，ResourcesManager将所有的Resources保存在一个键值对中，key与apk路径相关。因为能实现同一个apk引用同一个Resources的目的。
        3. AssetManager获取ResTable，ResTable可以理解为一张资源表，它是通过解析resources.arsc文件、添加系统资源、添加overlay资源一起得到的一个资源表结构。

    通过反射获取Resources，addAssetPath，然后再通过动态加载的方式获取资源id

    通过Factory2去加载和自定义View

## 8. AOP(面向切面编程，是针对业务处理过程中的切面进行提取，它所面对的是处理过程中的某个步骤，以获得逻辑过程中各部分之间低耦合性的隔离效果。AOP解决的特定问题，不是解决所有问题。)
    1. AOP的运用场景
        1. 参数校验和判空
            系统之间在进行接口调用时，往往是有入参传递的，入参是接口业务逻辑实现的先决条件，有时入参的缺失或错误会导致业务逻辑的异常，大量的异常捕获无疑增加了接口实现的复杂度，也让代码显得雍肿冗长，因此提前对入参进行验证是有必要的，可以提前处理入参数据的异常，并封装好异常转化成结果对象返回给调用方，也让业务逻辑解耦变得独立
        2. Android API23+的权限控制
            避免到处都是申请权限和处理权限的代码
        3. 无埋点
            分离业务代码和统计代码
        4. 安全控制
            比如全局的登录状态流程控制
        5. 日志记录
            侵入性更低更利于管控的日志系统
        6. 事件防抖
            防止View被连续点击触发多次事件
        7. 性能统计
            检测方法耗时其实已经有一些现成的工具，比如 trace view。痛点是这些工具使用起来都比较麻烦，效率低下，而且无法针对某一个块代码或者某个指定的sdk进行查看方法耗时。可以采用 AOP 思想对每个方法做一个切点，在执行之后打印方法耗时
        8. 事务处理
            声明方法，为特定方法加上事务，指定情况下（比如抛出异常）回滚事务
        9. 异常处理
            替代防御性的 try-Catch
        10. 缓存
            缓存某方法的返回值，下次执行该方法时，直接从缓存里获取
        11. 软件破解
            使用 Hook 修改软件的验证类的判断逻辑
    2. AspectJ的使用<https://www.jianshu.com/p/8582f896d136>

## 9. 动态代理


# 进程和线程

## Handler的机制是怎样的，Looper如何创建？
    Android系统在启动的时候会为Activity创建一个消息队列和消息循环（Looper），Looper.prepare()来创建消息队列，然后调用Looper.loop()进入消息循环。
        1.为什么不能在子线程中初始化
        因为Handler的使用依赖于Looper，而在子线程中，不会自动创建looper，所以没办法在子线程中使用Handler。调用Looper.prepare()之后可以使用Handler，但是使用完之后需要quit()

        2.在哪里进行Looper的初始化
        在ActivityThread中的main()方法中初始化Looper

        3. 重复Looper.prepare()会有什么后果
        一个线程只能有一个Looper，重复prepare会抛出RuntimeException

        4. Handler的机制是怎样的<https://www.kancloud.cn/alex_wsc/android-deep2/413393>
        Handler.sendMessage()发送消息时，会通过MessageQueue.enqueueMessage()向MessageQueue中添加一条消息，并在队列中根据时间进行排序；
        通过Looper.loop()开启循环后，不断轮询调用MessageQueue.next()；
        调用目标Handler.dispatchMessage()去传递消息，目标Handler收到消息后调用Handler.handlerMessage()处理消息。
        1. Message （消息）
        在线程之间传递，可在内部携带少量信息，用于不同线程之间交换数据
        可以使用what、arg1、arg2字段携带整型数据
        obj字段携带Object对象
        2. Handler （处理者）
        主要用于发送和处理消息，sendMessage（）用来发送消息，最终会回到handleMessage（）进行处理
        3. MessageQueue （消息队列）
        主要存放所有通过Handler发送的消息，它们会一直存在于队列中等待被处理
        每个线程只有一个MessageQueue
        4. Looper （循环器）
        调用loop（）方法后，会不断从MessageQueue 取出待处理的消息，然后传递到handleMessage进行处理

        5. sendMessageDelay的原理是啥？
        计算一个delaytime，如果没有满足，则执行下一条消息，如果满足则执行当前消息。
        就调用nativePollOnce(ptr, nextPollTimeoutMillis)进行阻塞。nativePollOnce()的作用类似与object.wait()，只不过是使用了Native的方法对这个线程精确时间的唤醒。
        pollOnce: timeOutMillis参数为超时等待时间。如果为-1，则表示无限等待，直到有事件发生为止。如果值为0，则无需等待立即返回。

        6. Message可以如何创建？哪种效果更好，为什么？
        创建Message对象的三种方式：
        Message msg = new Message();
        Message msg = Message.obtain();
        Message msg = handler.obtainMessage();(内部实现同样是Message.obtain())

        后两种方法都是从整个Messge池中这样是从一个可回收对象池中获取Message对象，能有效避免重复Message创建对象，因此更鼓励这种方式创建Message

        7. ThreadLocal有什么作用？
        本地线程变量，里面填充的的是当前线程的变量，该变量对其他线程而言是封闭且隔离的，ThreadLocal为变量在每个线程中创建了一个副本，这样每个线程都可以访问自己内部的副本变量。
        1、在进行对象跨层传递的时候，使用ThreadLocal可以避免多次传递，打破层次间的约束。
        2、线程间数据隔离
        3、进行事务操作，用于存储线程事务信息。
        4、数据库连接，Session会话管理。

        ThreadLocal类可实现线程本地存储的功能，把共享数据的可见范围限制在同一个线程之内，无须同步就能保证线程之间不出现数据争用的问题，这里可理解为ThreadLocal帮助Handler找到本线程的Looper。
        一个线程内可以存在多个 ThreadLocal 对象,每个线程的Thread对象中都有一个ThreadLocalMap对象，它存储了一组以ThreadLocal.threadLocalHashCode为key、以本地线程变量为value的键值对，而ThreadLocal对象就是当前线程的ThreadLocalMap的访问入口，也就包含了一个独一无二的threadLocalHashCode值，通过这个值就可以在线程键值值对中找回对应的本地线程变量。
        线程之间是独立的。不存在共享同步的问题。

        使用ThreadLocal要注意内存泄漏，可能会key被回收了，但是value还在，所以用完要remove

        使用的使用通过static ThreadLocal valuable = new ThreadLocal(), 创建一个ThreadLocal对象，用来保存对应的值，key是这个对象本身，value是要存储使用的值

        8. 为什么不会ANR
        ANR是指没有相应对应的生命周期或者事件，而死循环其实并没有需要响应的事件，所以不会ANR

        9. Handler的内存泄漏
        当一个对象一句不需要再使用了，本来该被回收时，而有另外一个正在使用的对象持有它的引用从而导致它不能被回收，这导致本该被回收的对象不能被回收而停留在堆内存中，这就产生了内存泄漏
        静态内部类，弱引用，destroy的时候removeCallbacksAndMessages

        10. sendMessage和post有什么区别
        本质上没有区别，最后都是调用到了sendMessageDelayed，post只需要使用Runnable，然后源码封装成Message，将runnable封装成callback，而sendMessage则是直接发送message

## 1. 进程和线程的区别，线程是内存共享的吗，进程是内存共享的吗
    进程之间不共享内存，只能通过其他手段进行通信
    线程之间可以共享内存

## 2. 多线程，线程池 https://www.cnblogs.com/absfree/p/5327678.html
    Thread，线程最好有一个命名，方便查找
    Runnable
    通过Callable和Future创建线程
    既然有两种方式可以创建线程，那么我们该使用哪一种呢？
    首先，直接继承Thread类的方法看起来更加方便，但它存在一个局限性：由于Java中不允许多继承，我们自定义的类继承了Thread后便不能再继承其他类，这在有些场景下会很不方便；实现Runnable接口的那个方法虽然稍微繁琐些，但是它的优点在于自定义的类可以继承其他的类。

    AsyncTask，注意内存泄漏
    Handler，比AsyncTask安全，AsyncTask一旦线程开启即dobackground方法执行后无法给线程发送消息，仅能通过预先设置好的标记来控制逻辑。
    ThreadExecuter，网络消息异步阻塞队列
    一个AsyncTask创建之后只会执行一次，通过一个成员变量去记录Task的状态，初始化是Pending，结束是Finished，不存在重置的情况。

    1. 如何新建线程池
    newFixedThreadPool(固定数目线程的线程池)
    newCachedThreadPool(可缓存线程的线程池)
    newSingleThreadExecutor(单线程的线程池)
    newScheduledThreadPool(定时及周期执行的线程池)

    2. 线程池的构造函数和作用
    ThreadPoolExecutor
    corePoolSize： 线程池核心线程数最大值
    maximumPoolSize： 线程池最大线程数大小
    keepAliveTime： 线程池中非核心线程空闲的存活时间大小
    unit： 线程空闲存活时间单位
    workQueue： 存放任务的阻塞队列
    threadFactory： 用于设置创建线程的工厂，可以给创建的线程设置有意义的名字，可方便排查问题。
    handler：  线城池的饱和策略事件，主要有四种类型。
    核心线程
    最大线程数
    当提交一个新任务到线程池时首先线程池判断基本线程池(corePoolSize)是否已满？没满，创建一个工作线程来执行任务。满了，则进入下个流程；其次线程池判断工作队列(workQueue)是否已满？没满，则将新提交的任务存储在工作队列里。满了，则进入下个流程；最后线程池判断整个线程池(maximumPoolSize)是否已满？没满，则创建一个新的工作线程来执行任务，满了，则交给饱和策略来处理这个任务；如果线程池中的线程数量大于 corePoolSize 时，如果某线程空闲时间超过keepAliveTime，线程将被终止，直至线程池中的线程数目不大于corePoolSize；如果允许为核心池中的线程设置存活时间，那么核心池中的线程空闲时间超过 keepAliveTime，线程也会被终止。
    饱和策略：
    Abort策略：默认策略，新任务提交时直接抛出未检查的异常RejectedExecutionException，该异常可由调用者捕获。
    CallerRuns策略：为调节机制，既不抛弃任务也不抛出异常，而是将某些任务回退到调用者。不会在线程池的线程中执行新的任务，而是在调用exector的线程中运行新的任务。
    Discard策略：新提交的任务被抛弃。

    3. 使用线程池的好处
    它帮我们管理线程，避免增加创建线程和销毁线程的资源损耗。因为线程其实也是一个对象，创建一个对象，需要经过类加载过程，销毁一个对象，需要走GC垃圾回收流程，都是需要资源开销的。
    提高响应速度。 如果任务到达了，相对于从线程池拿线程，重新去创建一条线程执行，速度肯定慢很多。
    重复利用。 线程用完，再放回池子，可以达到重复利用的效果，节省资源。

    4. 线程池是如何保留不执行的核心线程池的<https://blog.csdn.net/vernonzheng/article/details/8247564>
    因为workQueue是BlockingQueue
    一种是通过 take() 方法一直阻塞直到取出任务，通过Condition.await阻塞线程
    另一种是通过 poll(keepAliveTime，timeUnit) 方法在一定时间内取出任务或者超时，如果超时就会return nll，这个线程就会被回收，请注意核心线程一般不会被回收。

    5. 多线程同步<http://www.androidchina.net/6443.html>
        1. synchronized, volatile
        2. wait与notify
        sleep():是一个静态方法，使一个正在运行的线程处于睡眠状态，调用此方法要捕捉InterruptedException异常。sleep(0)直接丢出来
        Thread.sleep(0)的作用是什么
        答： - 由于Java采用抢占式的线程调度算法，因此可能会出现某条线程常常获取到CPU控制权的情况，为了让某些优先级比较低的线程也能获取到CPU控制权，可以使用Thread.sleep(0)手动触发一次操作系统分配时间片的操作，这也是平衡CPU控制权的一种操作
        wait():是对象的一个基本方法，使一个线程处于等待状态，并且释放所持有的对象的lock。
        notify():唤醒一个处于等待状态的线程，注意的是在调用此方法的时候，并不能确切的唤醒某一个等待状态的线程，而是由JVM确定唤醒哪个线程，而且不是按优先级。
        notifyAll():唤醒所有处入等待状态的线程，注意并不是给所有唤醒线程一个对象的锁，而是让它们竞争。
        3. 使用重入锁实现线程同步
        ReentrantLock() : 创建一个ReentrantLock实例
        lock() : 获得锁
        unlock() : 释放锁
        4. 使用局部变量实现线程同步
        ThreadLocal() : 创建一个线程本地变量
        get() : 返回此线程局部变量的当前线程副本中的值
        initialValue() : 返回此线程局部变量的当前线程的"初始值"
        set(T value) : 将此线程局部变量的当前线程副本中的值设置为value
        5. 使用阻塞队列实现线程同步
        BlockingQueue<E>定义了阻塞队列的常用方法，尤其是四种操作元素的方法，我们要多加注意，当队列满或空时：
        add()方法会抛出异常
        offer()方法返回false
        take()方法会阻塞
        put()方法会阻塞
        6. 使用原子变量实现线程同步(原理是CAS)
        AtomicInteger类常用方法：
        AtomicInteger(int initialValue) : 创建具有给定初始值的新的
        AtomicIntegeraddAddGet(int dalta) : 以原子方式将给定值与当前值相加
        get() : 获取当前值

        __几种方法的优劣__

    6. 如何加锁
    在字节码中通过添加monitor加锁，monitor存在于对象头的Mark word中
    同步方法ACC_SYNCHRONIZED，当方法调用时，调用指令将会检查方法的 ACC_SYNCHRONIZED 访问标志是否被设置，如果设置了，执行线程将先获取monitor，获取成功之后才能执行方法体，方法执行完后再释放monitor。在方法执行期间，其他任何线程都无法再获得同一个monitor对象。

    锁升级：
    在前面偏向锁和轻量级锁的小节中已经大概了解的锁的膨胀流程：
    偏向锁->轻量级锁->重量级锁
    偏向所锁，轻量级锁都是乐观锁，重量级锁是悲观锁。
    一个对象刚开始实例化的时候，没有任何线程来访问它的时候。它是可偏向的，意味着，它现在认为只可能有一个线程来访问它，所以当第一个线程来访问它的时候，它会偏向这个线程，此时，对象持有偏向锁。
    偏向第一个线程，这个线程在修改对象头成为偏向锁的时候使用CAS操作，并将对象头中的ThreadID改成自己的ID，之后再次访问这个对象时，只需要对比ID，不需要再使用CAS在进行操作。
    一旦有第二个线程访问这个对象，因为偏向锁不会主动释放，所以第二个线程可以看到对象是偏向状态，这时表明在这个对象上已经存在竞争了，检查原来持有该对象锁的线程是否依然存活，如果挂了，则可以将对象变为无锁状态，然后重新偏向新的线程，如果原来的线程依然存活，则马上执行那个线程的操作栈，检查该对象的使用情况，如果仍然需要持有偏向锁，则偏向锁升级为轻量级锁，（偏向锁就是这个时候升级为轻量级锁的）。如果不存在使用了，则可以将对象回复成无锁状态，然后重新偏向。
    轻量级锁认为竞争存在，但是竞争的程度很轻，一般两个线程对于同一个锁的操作都会错开，或者说稍微等待一下（自旋），另一个线程就会释放锁。 但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁膨胀为重量级锁，重量级锁使除了拥有锁的线程以外的线程都阻塞，防止CPU空转。

    P.S.:如果要阻塞或唤醒一条线程，都需要操作系统来帮忙完成，这就需要从用户态转换到核心态中，因此状态转换需要耗费很多的处理器时间。(重量级)

    7. 如何终止线程任务
    前提：其实调用Thread对象的interrupt函数并不是立即中断线程，只是将线程中断状态标志设置为true。
    通过interrupt中断被阻塞的线程
    使用了实例方法isInterrupted判断线程是否已被中断，如果被中断将跳出循环以此结束线程，注意非阻塞状态调用interrupt()并不会导致中断状态重置。

    8. 开run()和start()方法区别
    start（）方法来启动线程，真正实现了多线程运行，这时无需等待run方法体代码执行完毕而直接继续执行下面的代码
    run（）方法当作普通方法的方式调用，程序还是要顺序执行，还是要等待run方法体执行完毕后才可继续执行下面的代码

    9. 有了进程为什么还要线程？
        进程只能在一个时间干一件事，如果想同时干两件事或多件事，进程就无能为力了。
        进程在执行的过程中如果阻塞，例如等待输入，整个进程就会挂起，即使进程中有些工作不依赖于输入的数据，也将无法执行。

## 3. volatile和synchronized的作用和区别是什么
    1. 如何修饰
    volatile只能修饰变量，使得变量在内存中可见。但是不能保证变量的线程安全，对volatile字段的写入操作先于读操作，即使两个线程同时修改和获取volatile变量，get操作也能拿到最新的值，这是用volatile替换锁的经典应用场景。
    synchronized(obj)锁对象当前实例对象
    synchronized void method()锁对象当前实例对象
    synchronized(this)锁对象当前实例对象
    synchronized(obj.Class)锁对象是当前的类Class对象
    static synchronized void method()锁对象是当前的类Class对象

    2. 锁是存储在堆的什么位置
    对象锁是存在堆的对象头里面

## 4. ReentrantLock重入锁<https://crossoverjie.top/2018/01/25/ReentrantLock/>
    ReentrantLock 就是一个普通的类，它是基于 AQS(AbstractQueuedSynchronizer)来实现的。
    是一个重入锁：一个线程获得了锁之后仍然可以反复的加锁，不会出现自己阻塞自己的情况。
    ReentrantLock 分为公平锁和非公平锁，默认是非公平锁(线程自己去抢占)

## 5. AQS(AbstractQueuedSynchronizer)
    AQS 核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是用虚拟双向队列锁实现的，即将暂时获取不到锁的线程加入到队列中。
    未看<https://juejin.im/entry/5ae02a7c6fb9a07ac76e7b70>

## 6. IPC(Inter-Process Communication)进程间通信<https://www.jianshu.com/p/c1015f5ffa74>
    通常的做法是消息发送方将要发送的数据存放在内存缓存区中，通过系统调用进入内核态。然后内核程序在内核空间分配内存，开辟一块内核缓存区，调用 copyfromuser() 函数将数据从用户空间的内存缓存区拷贝到内核空间的内核缓存区中。同样的，接收方进程在接收数据时在自己的用户空间开辟一块内存缓存区，然后内核程序调用 copytouser() 函数将数据从内核缓存区拷贝到接收进程的内存缓存区。这样数据发送方进程和数据接收方进程就完成了一次数据传输，我们称完成了一次进程间通信。
    通信机制：
    1.匿名管道
    只支持单向数据流；
    只能用于具有亲缘关系的进程之间；
    没有名字；
    管道的缓冲区是有限的（管道制存在于内存中，在管道创建时，为缓冲区分配一个页面大小）；
    管道所传送的是无格式字节流，这就要求管道的读出方和写入方必须事先约定好数据的格式，比如多少字节算作一个消息（或命令、或记录）等等；
    2.有名管道
    有名管道的名字存在于文件系统中，内容存放在内存中。
    3.信号
    信号是软件层次上对中断机制的一种模拟，是一种异步通信方式
    4.消息队列
    消息队列是存放在内核中的消息链表，每个消息队列由消息队列标识符表示。
    缓冲区大，信息量大，可以随机定位消息，存储在内核
    5.共享内存
    使得多个进程可以可以直接读写同一块内存空间，是最快的可用IPC形式。是针对其他通信机制运行效率较低而设计的。
    为了在多个进程间交换信息，内核专门留出了一块内存区，可以由需要访问的进程将其映射到自己的私有地址空间。进程就可以直接读写这一块内存而不需要进行数据的拷贝，从而大大提高效率。
    由于多个进程共享一段内存，因此需要依靠某种同步机制（如信号量）来达到进程间的同步及互斥。
    6.信号量
    信号量是一个计数器，用于多进程对共享数据的访问，信号量的意图在于进程间同步。
    7.套接字(Socket)
    套接字是一种通信机制，凭借这种机制，客户/服务器（即要进行通信的进程）系统的开发工作既可以在本地单机上进行，也可以跨网络进行。

## 7. Android跨进程通信的方式有哪几种
    Bundle, ContentProvider, Broadcast, AIDL, Binder, Messenger，文件共享, Socket
    ## Binder
        Client、Server 借助 Binder 驱动完成跨进程通信的实现机制
        Binder IPC 正是基于内存映射（mmap）来实现的，使用mmap在内核空间创建数据接收的缓存空间。<https://juejin.im/post/5e85aa5e6fb9a03c341d9737>
        对于接受进程而言，他的用户空间的虚拟地址指向的物理地址和他的内核空间的虚拟地址指向的物理地址是同一块区域的，所以就避免了数据从内核空间到用户空间的一次拷贝
        Binder 通信过程
        至此，我们大致能总结出 Binder 通信过程：
        首先，一个进程使用 BINDERSETCONTEXT_MGR 命令通过 Binder 驱动将自己注册成为 ServiceManager；
        Server 通过驱动向 ServiceManager 中注册 Binder（Server 中的 Binder 实体），表明可以对外提供服务。驱动为这个 Binder 创建位于内核中的实体节点以及 ServiceManager 对实体的引用，将名字以及新建的引用打包传给 ServiceManager，ServiceManger 将其填入查找表。
        Client 通过名字，在 Binder 驱动的帮助下从 ServiceManager 中获取到对 Binder 实体的引用，通过这个引用就能实现和 Server 进程的通信。在通信过程中，发送方通过binder驱动将数据写在缓存区中，然后接收方到缓存中读取数据。

        Binder通信的代理模式：
        当 A 进程想要获取 B 进程中的 object 时，驱动并不会真的把 object 返回给 A，而是返回了一个跟 object 看起来一模一样的代理对象 objectProxy，这个 objectProxy 具有和 object 一摸一样的方法，但是这些方法并没有 B 进程中 object 对象那些方法的能力，这些方法只需要把把请求参数交给驱动即可。对于 A 进程来说和直接调用 object 中的方法是一样的。

        binder服务端是如何限制客户端的访问：
        每个 Server 进程在启动时创建一个 binder 线程池，并向其中注册一个 Binder 线程；之后 Server 进程也可以向 binder 线程池注册新的线程，或者 Binder 驱动在探测到没有空闲 binder 线程时主动向 Server 进程注册新的的 binder 线程。对于一个 Server 进程有一个最大 Binder 线程数限制，默认为16个binder线程

        1. 如何知道客户端需要调用哪个进程以及该进程中的函数
        给每个需要远程通信的类一个唯一标识，可以通过包名+类名的字符串以标识，然后在类里面给每个函数编号即可对函数唯一编码。
        2. 客户端如何将函数形参发送给远程进程中的函数，以及如何将远程进程函数计算结果返回客户端
        定义一个可打包的接口Parcelable，这个接口提供2个重要函数，分别是将对象中的属性写入到数组和从数组中的数据还原对象，每个可以发送到远程函数作为形参的对象只需实现Parcelable接口即可。
        3. 如何去屏蔽底层通信细节，让实现客户端调用远程函数就像调用本地函数一样
        服务端想要实现被跨进程访问，就必须继承Binder类。

        优势：(为什么 Android 要采用 Binder 作为 IPC 机制？)<https://www.zhihu.com/question/39440766/answer/89210950>
        1.只需要copy一次
        2.UID/PID身份，安全性高
        3.开销小（相比于socket）

    ## AIDL
        简言之，AIDL能够实现进程间通信，其内部是通过Binder机制来实现的，后面会具体介绍，现在先介绍AIDL的使用。
        1. aidl使用到的组件有哪些，怎么使用的
        Service需要定义：onBind()、onCreat、和公共接口，通过new ILibraryManager.Stub()创建mBinder对象。
        Client: AIDL.Stub.asInterface(service);调用远程Service。onServiceConnected, onServiceDisconnected

    ## Messager
        Messenger是一种轻量级的多进程通信方式，它是在 AIDL 的基础上封装而成的，可以看做是 AIDL 的简化版，支持一对多的串行实时通信，一次只处理一个请求，不存在并发的问题。是基于Message对象进行跨进程通信的，类似于Handler发送消息实现线程间通信一样的用法。
        首先创建一个Handler对象
        接着创建一个Messenger对象，并把Handler对象以形参传入Messenger中
        最后通过mMessenger.getBinder得到Binder对象，在onBind方法中返回。

## 8. Activity的stack和task
    Process，系统为App提供的进程（Process）资源，默认一个App一个Process，我们也可以要求为一个App提供多个Process。

    AMS，系统开机启动时，会启动ActivityManagerService（简称AMS）来管理四大组件以及Intent、pendingintent、apk进程、task和释放组件内存等。AMS是一个独立的进程（SystemServie、AMS、PMS、WMS等系统服务，实质都是一个个App，各自拥有自己的进程），系统只有一个AMS，而且因为AMS在独立的进程上，所以AMS和App之间，需要通过IPC机制进行跨进程通信。

    ActivityStack ，AMS为了管理Activity实例，会建立并管理一个ActivityStack，用ActivityStackSupervisor来更新这个栈，因为系统只有一个AMS，所以ActivityStack也只有一个。ActivityStack是一个管理者的角色，会管理协调ActivityRecord和TaskRecord，以及所有的任务栈。

    TaskRecord，系统中的所有Activity，在面向用户时可能分成多个组，这是按照任务栈（Task）来组织的，我们常说的Activity启动模式，其实是操作任务栈（Task），而不是操作Activity栈（Stack）。

    Activity管理的核心在于AMS，AMS中分两个维度来管理Activity，一个是ActivityStack，只有一个，用ActivityRecord来记录对应的Activity信息，主要用来管理系统中有哪些Activity；另一个是ActivityTask，可能有多个，用TaskRecord来记录对应的Activity信息，主要用来把Activity组合成为多个Task栈，以便按照特定的业务逻辑展示给用户。
    所以，要管理多个Activity之间的关系，主要依靠任务栈Task。

## 9. 多线程系列之Semaphore、CyclicBarrier、CountDownLatch<https://blog.csdn.net/qq_30379689/article/details/53769474>
    Semaphore属于有上限的线程容器，当容器满的时候，不会再往容器中增加新的线程。比如：打印机
    CyclicBarrier属于等待线程容器，只有当容器的线程数满了后，才会执行所有线程，主线程不用等待（子线程需要等待）
    CountDownLatch属于等待线程容器，只有当容器的线程数满了后，才会执行所有线程，主线程需要等待（子线程不需要等待）

# Java基础
## 1. HashMap底层实现原理
    HashMap实际是一种“数组+链表”数据结构。在put操作中，通过内部定义算法寻止找到数组下标，将数据直接放入此数组元素中，若通过算法得到的该数组元素已经有了元素（俗称hash冲突，链表结构出现的实际意义也就是为了解决hash冲突的问题）。将会把这个数组元素上的链表进行遍历，将新的数据放到链表末尾。
    线程不安全:
    1. 重复写入，导致数据丢失
    2. 重复扩展，导致数据丢失
    使用一个数组记录hash下标，使用一个entry记录hash和value<https://www.jianshu.com/p/aff3b8990ab3>

    多线程异常如何处理
    1. 使用ConcurrentHashMap(不允许value为null)
    2. Collections.synchronizedMap将HashMap包装起来
    3. hashtable但是代价太高

    hashmap与SparseArray对比：内存使用、查找效率、使用场景
    hashMap默认创建16的数组，且使用enrty记录节点，所以内存占用更多
    SparseArray避免了装箱节省内存，不需要存储hash值，因为是通过二分查找，但是put和删除不快，因为都要二分查找，它也是线程不安全的，允许value为null。
    ArrayMap与SparesArray类似，只不过key可以为其他数据类型

    hashmap 1.7VS1.8<https://www.cnblogs.com/liang1101/p/12728936.html> <https://www.jianshu.com/p/8cbd941a89a7>
    扩容: 在jdk1.8之前，多线程并发调用put方法，引发扩容，触发resize的过程，因为是链表的关系，甚至有几率形成死循环<https://juejin.im/post/5d8c261de51d4577ea077e94>。扩容位置同put
    jdk1.8中，采用了复制的方法，而不是操作原链，不会再出现死循环的情况。使用原位置 OR 原位置 + 旧容量

    ConcurrentHashMap<https://blog.csdn.net/rickiyeat/article/details/77367017>
    SDK1.7以前使用的是分段锁(ReentrantLock)
    一个ConcurrentHashMap里面有一个Segment数组，每个Segment数组里面有个HashEntry数组
    容器里有多把锁，每一把锁用于锁容器其中一部分数据，那么当多线程访问容器里不同数据段的数据时，线程间就不会存在锁竞争，从而可以有效的提高并发访问效率，这就是ConcurrentHashMap所使用的锁分段技术，首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。
    get操作：get没有锁是因为，value和count用volatile进行修饰，保证读取的值是最新的。(除非读到的值是空的才会加锁重读)
    put操作：put方法里需要对共享变量进行写入操作，所以为了线程安全，在操作共享变量时必须得加锁。Put方法首先定位到Segment，然后在Segment里进行插入操作。插入操作需要经历两个步骤，第一步判断是否需要对Segment里的HashEntry数组进行扩容，第二步定位添加元素的位置然后放在HashEntry数组里。
    统计：ConcurrentHashMap的做法是先尝试2次通过不锁住Segment的方式来统计各个Segment大小，如果统计的过程中，容器的count发生了变化，则再采用加锁的方式来统计所有Segment的大小。
    SDK1.8使用的是synchronized+CAS(Compare and Swap)，数组+链表+红黑树的形式，通过CAS + synchronized保证线程安全。<https://my.oschina.net/pingpangkuangmo/blog/817973#h2_12>
    get操作：
    put操作：通过CAS插入第一个元素，通过对index进行加锁进行和CAS，存放元素<https://russxia.com/2019/05/09/ConcurrentHashMap%E7%9A%84%E9%AB%98%E5%B9%B6%E5%8F%91%E5%92%8C%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8/>
    扩容：元素个数超过了8个，当数组长度还未达到64个时，优先数组的扩容，否则选择链表转为红黑树。ConcurrentHashMap的扩容操作可以允许多个线程并发执行

    CAS(Compare and Swap): 一个变量v，旧值A，新值B。当且仅当变量v当前的值和旧值A相同时，才会将v的值更新为B。这整个操作是原子化的，不同平台的JVM也有不同的实现，一般以Native方法执行。
    AQS(Abstract Queued Synchronizer):同步队列，阻塞线程排队，等待通知
    红黑树:自平衡的二叉查找树。通过旋转和变色达到自平衡。<https://www.jianshu.com/p/e136ec79235c>
    1.节点是红色或黑色。
    2.根节点是黑色。
    3.每个叶子节点都是黑色的空节点（NIL节点）。
    4 每个红色节点的两个子节点都是黑色。(从每个叶子到根的所有路径上不能有两个连续的红色节点)
    5.从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。
    最坏时间复杂度为O(2lgN)。

## 2. ==, equal, hashCode
    1.基本数据类型，也称原始数据类型。byte,short,char,int,long,float,double,boolean他们之间的比较，应用双等号（==）,比较的是他们的值。
    2.复合数据类型(类)
    当他们用（==）进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。
    3.如果两个对象根据equals()方法比较是相等的，那么调用这两个对象中任意一个对象的hashCode方法都必须产生同样的整数结果。
    4.如果两个对象根据equals()方法比较是不相等的，那么调用这两个对象中任意一个对象的hashCode方法，则不一定要产生相同的整数结果。但是程序员应该知道，给不相等的对象产生截然不同的整数结果，有可能提高散列表的性能。

## 3. object有哪些常用的方法
    1. getClass
    2. clone
    3. hashCode
    4. equal
    5. toString
    6. notify
    7. notifyAll
    8. wait
    9. finalize(用于GC回收，但是不建议重写)

## 4. 内存泄漏的具体表现是什么？会造成什么后果？
    会导致可用内存越来越少，引起OOM，频繁GC，造成程序卡顿
    强引用new一个对象
    软引用SoftReference：实现缓存机制，软引用是用来描述一些还有用但并非必须的对象。对于软引用关联着的对象，在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围进行第二次回收
    弱引用WeakReference：防止内存泄漏，要保证内存被虚拟机回收。
    虚引用一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来获取一个对象的实例。它的作用在于跟踪垃圾回收过程。

        0.内存泄漏场景
            静态集合类/静态变量持有了Activity
            匿名内部类或非静态内部类引起的内存泄漏（编译的时候内部类会自动生成带有外部类入参的构造方法）
            Handler handler.removeCallbacksAndMessages(null)
            Static View
            广播，监听器Listener，没有remove掉
            Cursor, file没有关闭销毁

        1.最好解释清楚GC垃圾回收机制以及概念GC Root。
            因为这些东西被认为是在被使用。JVM设计者命令JVM将他们作为GC Roots, GC Roots是一个统称，是所有可以用作“根集可达性算法”中的根。
            GC ROOT：
            1.JAVA虚拟机栈中的本地变量引用对象；
            2.方法区中静态变量引用的对象；
            3.方法区中常量引用的对象；
            4.本地方法栈中JNI引用的对象；https://www.jianshu.com/p/f5582d9a0f73
            5.激活状态的线程
            垃圾回收器不会回收GC Roots以及那些被它们间接引用的对象

        2.如何避免内存泄露：
            减小对象的内存占用：
            使用更加轻量级的数据结构：
            考虑适当的情况下替代HashMap等传统数据结构而使用安卓专门为手机研发的数据结构类ArrayMap/SparseArray。SparseLongMap/SparseIntMap/SparseBoolMap内存会优化。
            HashMap.put(string,Object);Object o = map.get(string);会导致一些没必要的自动装箱和拆箱。
            适当的避免在android中使用Enum枚举，替代使用普通的static常量。（一般还是提倡多用枚举---软件的架构设计方面；如果碰到这个枚举需要大量使用的时候就应该更加倾向于解决性能问题。）。
            较少Bitmap对象的内存占用。
            使用inSampleSize: 计算图片压缩比例进行图片压缩，可以避免大图加载造成OOM;
            decodeformat：图片的解码格式选择，ARGB_8888/RGB_565/ARGB_4444/ALPHA_8,还可以使用WebP。
            使用更小的图片
            资源图片里面，是否存在还可以继续压缩的空间。
            2）内存对象的重复利用：
            使用对象池技术，
            两种：
            1.自己写；
            2.利用系统既有的对象池机制。比如LRU(Last Recently Use)算法。
                a.ListView/GridView源码可以看到重用的情况ConvertView的复用。RecyclerView中Recycler源码。
                b.Bitmap的复用 Listview等要显示大量图片。需要使用LRU缓存机制来复用图片。
                c. 避免在onDraw方法里面执行对象的创建，要复用。避免内存抖动。
                d.常见的java基础问题---StringBuilder等
            3）避免对象的内存泄露
            4）使用一些内存的优化策略：看文档

        3.虚拟机为什么gc的时候会暂停进程
            垃圾回收并不会阻塞我们程序的线程，他是与当前程序并发执行的。所以问题就出在这里，当GC线程标记好了一个对象的时候，此时我们程序的线程又将该对象重新加入了“关系网”中，当执行二次标记的时候，该对象也没有重写finalize()方法，因此回收的时候就会回收这个不该回收的对象。

        4.GC回收算法
            1.标记回收
            2.标记整理
            3.复制算法
            4.分代回收

        5.可达性算法和引用计数的区别和用处。用于判断是否可以被GC回收
            1）引用计数法。引用被标记为零的时候，意味着可以被GC回收
            2）在Java中采取了 可达性分析法
            通过一系列的“GC Roots”对象作为起点进行搜索，如果在“GC Roots”和一个对象之间没有可达路径，则称该对象是不可达的，不过要注意的是被判定为不可达的对象不一定就会成为可回收对象。被判定为不可达的对象要成为可回收对象必须至少经历两次标记过程，如果在这两次标记过程中仍然没有逃脱成为可回收对象的可能性，则基本上就真的成为可回收对象了。

        6.新生代和老生代的算法
            堆大小 = 新生代 + 老年代。默认下，新生代 ( Young ) = 1/3 的堆空间大小，老年代 ( Old ) = 2/3 的堆空间大小；
            新生代 ( Young ) 被细分为 Eden 和 两个 Survivor 区域，这两个 Survivor 区域分别被命名为 from 和 to，以示区分。默认的，Edem : from : to = 8 : 1 : 1；
            JVM 每次只会使用 Eden 和其中的一块 Survivor 区域来为对象服务，所以无论什么时候，总是有一块 Survivor 区域是空闲着的。 因此，新生代实际可用的内存空间为 9/10 ( 即90% )的新生代空间；
            GC 分为两种：老生代中采用标记-清除算法的Full GC ( 或称为 Major GC )和新生代中采用复制算法的Minor GC。新生代是 GC 收集垃圾的频繁区域；

        7.PermGen 即永久代 ( 方法区 )，它还有一个名字，叫非堆，主要用来存储由 jvm 加载的类文件信息、常量、静态变量等；

    AsyncTask
    非静态内部类：尽量使用静态内部类来替代内部类，同时避免让长期运行的任务（ 线程 ）持有 Activity的引用。
    匿名内部类：同上
    深入理解AsyncTask的工作原理(https://www.cnblogs.com/absfree/p/5357678.html)

    Handler：使用 WeakReference 来引用 Activity
    单例／静态引用
    Activity Context：换成使用Application Context
    资源没有关闭造成的内存泄漏：BraodcastReceiver，ContentObserver，File，Cursor，Stream，Bitmap

    在非Activity中，正常是不能直接getContext来拿到Context的，获取资源有需要靠Context，这时可以考虑在自己的Application中维护一个全局的Context，供无法直接拿到Context的类使用，省的参数传来传去（视图相关的不建议使用ApplicationContext）

## 5. 系统杀进程的顺序是怎样的
    重要性层次结构一共有 5 级。以下列表按照重要程度列出了各类进程（第一个进程最重要，将是最后一个被终止的进程）：
    1.前台进程：（foreground process）
    用户当前操作所必需的进程。如果一个进程满足以下任一条件，即视为前台进程：
    托管用户正在交互的 Activity（已调用 Activity 的 onResume() 方法）
    托管某个 Service，后者绑定到用户正在交互的 Activity
    托管正在“前台”运行的 Service（服务已调用 startForeground()）
    托管正执行一个生命周期回调的 Service（onCreate()、onStart() 或 onDestroy()）
    托管正执行其 onReceive() 方法的 BroadcastReceiver
    通常，在任意给定时间前台进程都为数不多。只有在内存不足以支持它们同时继续运行这一万不得已的情况下，系统才会终止它们。 此时，设备往往已达到内存分页状态，因此需要终止一些前台进程来确保用户界面正常响应。

    2.可见进程
    没有任何前台组件、但仍会影响用户在屏幕上所见内容的进程。 如果一个进程满足以下任一条件，即视为可见进程：
    托管不在前台、但仍对用户可见的 Activity（已调用其 onPause() 方法）。例如，如果前台 Activity 启动了一个对话框，允许在其后显示上一 Activity，则有可能会发生这种情况。
    托管绑定到可见（或前台）Activity 的 Service。
    可见进程被视为是极其重要的进程，除非为了维持所有前台进程同时运行而必须终止，否则系统不会终止这些进程。

    3.服务进程
    正在运行已使用 startService() 方法启动的服务且不属于上述两个更高类别进程的进程。尽管服务进程与用户所见内容没有直接关联，但是它们通常在执行一些用户关心的操作（例如，在后台播放音乐或从网络下载数据）。因此，除非内存不足以维持所有前台进程和可见进程同时运行，否则系统会让服务进程保持运行状态。

    4.后台进程
    包含目前对用户不可见的 Activity 的进程（已调用 Activity 的 onStop() 方法）。这些进程对用户体验没有直接影响，系统可能随时终止它们，以回收内存供前台进程、可见进程或服务进程使用。 通常会有很多后台进程在运行，因此它们会保存在 LRU （最近最少使用）列表中，以确保包含用户最近查看的 Activity 的进程最后一个被终止。如果某个 Activity 正确实现了生命周期方法，并保存了其当前状态，则终止其进程不会对用户体验产生明显影响，因为当用户导航回该 Activity 时，Activity 会恢复其所有可见状态。 有关保存和恢复状态的信息，请参阅 Activity文档。

    5.空进程
    不含任何活动应用组件的进程。保留这种进程的的唯一目的是用作缓存，以缩短下次在其中运行组件所需的启动时间。 为使总体系统资源在进程缓存和底层内核缓存之间保持平衡，系统往往会终止这些进程。
    根据进程中当前活动组件的重要程度，Android 会将进程评定为它可能达到的最高级别。例如，如果某进程托管着服务和可见 Activity，则会将此进程评定为可见进程，而不是服务进程。

    此外，一个进程的级别可能会因其他进程对它的依赖而有所提高，即服务于另一进程的进程其级别永远不会低于其所服务的进程。 例如，如果进程 A 中的内容提供程序为进程 B 中的客户端提供服务，或者如果进程 A 中的服务绑定到进程 B 中的组件，则进程 A 始终被视为至少与进程 B 同样重要。

    由于运行服务的进程其级别高于托管后台 Activity 的进程，因此启动长时间运行操作的 Activity 最好为该操作启动服务，而不是简单地创建工作线程，当操作有可能比 Activity 更加持久时尤要如此。例如，正在将图片上传到网站的 Activity 应该启动服务来执行上传，这样一来，即使用户退出 Activity，仍可在后台继续执行上传操作。使用服务可以保证，无论 Activity 发生什么情况，该操作至少具备“服务进程”优先级。 同理，广播接收器也应使用服务，而不是简单地将耗时冗长的操作放入线程中。

## 6. 堆和栈的区别是什么？
    堆用来存放new出来的变量。虚拟机栈用来存储临时变量。

## 7. Java中多态是怎么表现？
    多态是指程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编译时不确定，在运行期间才确定，一个引用变量到底会指向哪个类的实例。这样就可以不用修改源程序，就可以让引用变量绑定到各种不同的类实现上。
    Java实现多态有三个必要条件
    继承、重定、向上转型，在多态中需要将子类的引用赋值给父类对象，只有这样该引用才能够具备调用父类方法和子类的方法。

    概念：同一操作作用于不同子类，可以有不同的解释，产生不同的执行结果。
    最简单的说法就是：父类引用指向子类对象。

    有父类的情况下，父类和子类的加载顺序。
    首先加载父类的静态变量，静态代码块（从上到下顺序执行）-->子类的静态变量，静态代码块-->父类非静态变量，非静态代码块-->父类构造函数-->子类非静态变量，非静态代码块-->子类构造函数

    函数重载，同一个函数名，具有不同的参数和返回值。

## 8. 内部类
    非静态内部类
    1、成员内部类中不能存在任何static的变量和方法
    2、成员内部类是依附于外围类的对象，所以只有先创建了外围类对象才能够创建内部类对象

    1、内部类能够提供更好的隐蔽性。因为我们的内部类是可以用private和protected修饰的，所以在必要的时候我们可以保证在其它类里是无法创建当前内部类对象的。就好比我们常用的静态内部类的单例模式。
    2、通过内部类可以实现多继承。（用两个内部类分别继承两个不同的父类）
    3、用匿名内部类实现回调功能。
    4、可以避免修改接口而实现同一个类中两种同名方法的调用。

    静态内部类
    1、它的创建是不需要依赖于外围类的对象
    2、它不能使用任何外围类的非static成员变量和方法

    创建给外部类使用

## 9. 反射
    https://blog.csdn.net/briblue/article/details/74616922
    在运行状态中，对任意一个类，都能知道这个类的所有属性和方法，对任意一个对象，都能调用它的任意一个方法和属性。这种能动态获取信息及动态调用对象方法的功能称为java语言的反射机制。
    反射的作用：
    开发过程中，经常会遇到某个类的某个成员变量、方法或属性是私有的，或只对系统应用开放，这里就可以利用java的反射机制通过反射来获取所需的私有成员或是方法。

## 10. JVM调优
    1. 减少GC次数
    2. 举个JIT编译器优化的例子，当虚拟机发现某个方法被频繁运行时（或一个多次执行的循环体），就会把这些代码认定为“热点代码”，为提高效率，运行时，就会把这些代码编译成与本地平台相关的机器码，而完成这个任务的，就是JIT编译器。
    3. 解释器：这里的解释器作用就是将字节码一条一条翻译为机器码，它的特点是立即执行，节约内存
    4. 编译器：它的作用是把源程序的每一条语句都编译成机器语言，并保存为二进制文件。
    5. 语言无关的优化技术之一：公共子表达式消除
    6. 语言相关的优化技术之一：数组边界检查消除
    7. 最重要的优化技术之一：方法内联
    8. 最前沿的优化技术之一：逃逸分析

    JVM内存
        程序计数器(当前线程执行的位置)
        Java虚拟机栈(调用方法并执行方法，存储局部变量表，操作数栈，动态链接和返回地址)
        本地方法栈(native方法，JNI涉及到的)
        Java堆(线程共享)(存放对象实例)(直接内存是运行时虚拟机开辟出来的一部分)
        方法区(线程共享)(类信息，常量和静态变量)(运行时常量池是其中一部分)

## 11. classloader
    它负责将 Class 的字节码形式转换成内存形式的 Class 对象。
    JVM 运行并不是一次性加载所需要的全部类的，它是按需加载，也就是延迟加载。程序在运行的过程中会逐渐遇到很多不认识的新类，这时候就会调用 ClassLoader 来加载这些类。加载完成后就会将 Class 对象存在 ClassLoader 里面，下次就不需要重新加载了。
    比如你在调用某个类的静态方法时，首先这个类肯定是需要被加载的，但是并不会触及这个类的实例字段，那么实例字段的类别 Class 就可以暂时不必去加载，但是它可能会加载静态字段相关的类别，因为静态方法会访问静态字段。而实例字段的类别需要等到你实例化对象的时候才可能会加载。

    双亲委派
    JAVA: 的classloader JVM 中内置了三个重要的 ClassLoader，分别是 BootstrapClassLoader（负责加载 JVM 运行时核心类）、ExtensionClassLoader（负责加载 JVM 扩展类，比如 swing 系列、内置的 js 引擎、xml 解析器） 和 AppClassLoader。
    这三个 ClassLoader 之间形成了级联的父子关系，每个 ClassLoader 都很懒，尽量把工作交给父亲做，父亲干不了了自己才会干。每个 ClassLoader 对象内部都会有一个 parent 属性指向它的父加载器。
    因为这样可以避免重复加载，当父亲已经加载了该类的时候，就没有必要子ClassLoader再加载一次。
    Android:
        1.android中ClassLoader的种类。
        a.BootClassLoader：主要用来加载android FrameWork层的一些class字节码文件。
        d.BaseDexClassLoader：父类，PathClassLoader和DexClassLoader都是它的子类。
        b.PathClassLoader：主要用来加载已经安装到系统中的APK 文件中的class（与java中的AppClassLoader类似）。
        c.DexClassLoader：用来加载指定目录下的class字节码文件（与java中的自定义ClassLoader类似）。

    ClassLoader 里面有三个重要的方法 loadClass()、findClass() 和 defineClass()。

    热修复原理(ClassLoader的应用)<https://blog.csdn.net/github_37130188/article/details/89762543>
    1. 安卓在加载class时会通过双亲委托机制去加载一个类，先让父类去加载，如果找不到再让子类去加载某个类。
    2. 通过查看ClassLoader源码发现findClass方法是由每个子类自己实现的，比如BootClassLoader或者BaseDexClassLoader。而PathClassLoader是继承自BaseDexClassLoader的，它的findClass也是在BaseDexClassLoader里面实现的。
    3. BaseDexClassLoader的findClass里面使用了另一个对象DexPathList去查找对应的class，这是安卓里面特有的实现。在DexPathList对象里面有一个属性dexElements，dexElements是用于存放加载好了的dex数组的，查找class是从这个dexElements数组里面去找的。
    4. dexElements里面存放的是Element对象，findClass最终会交给Element去实现，Element又会交给Element里面的一个属性DexFile去实现。我看了下，最终是用native实现的。
    5. 回到上面的第3步中的DexPathList对象从dexElements数组里面查找class，从数组的前面往后找,找到了就返回结果，不再继续查找。
    6. 所以当我们把修复好bug了的class，搞成dex,然后通过反射等技术放到dexElements的最前面，这样系统在通过PathClassLoader找到class时，就能先找到我们放置的修复好bug的class，然后就不会再往后找了，相当于实现了热修复。这样有bug的class就不会被用了。应了一句古话，近水楼台先得月。
    7. 第6点中的反射，流程是：获取到PathClassLoader，然后反射获取到父类中的DexPathList对象，然后再反射到DexPathList对象中的dexElements数组。然后将补丁(dex)转为Element对象，插入到dexElements数组的前面(先复制出来，再合并，再通过反射放回去)。

## 12. new Integer(123)和123的区别(Integer.valueOf(123))
    123基本数据类型，使用较小的内存，但是在使用ArrayList的时候，会需要装箱。
    new Integer(123)会每次都去new一个新的对象
    Integer.valueOf(123)会去读缓冲池

    Integer是int的包装类；int是基本数据类型；
    Integer变量必须实例化后才能使用；int变量不需要；
    Integer实际是对象的引用，指向此new的Integer对象；int是直接存储数据值 ；
    Integer的默认值是null；int的默认值是0。

    （1）由于Integer变量实际上是对一个Integer对象的引用，所以两个通过new生成的Integer变量永远是不相等的（因为new生成的是两个对象，其内存地址不同）。
    （2）Integer变量和int变量比较时，只要两个变量的值是向等的，则结果为true（因为包装类Integer和基本数据类型int比较时，java会自动拆包装为int，然后进行比较，实际上就变为两个int变量的比较）
    （3）非new生成的Integer变量和new Integer()生成的变量比较时，结果为false。因为非new生成的Integer变量指向的是静态常量池中cache数组中存储的指向了堆中的Integer对象，而new Integer()生成的变量指向堆中新建的对象，两者在内存中的对象引用（地址）不同。
    （4）对于两个非new生成的Integer对象，进行比较时，如果两个变量的值在区间-128到127之间，则比较结果为true，如果两个变量的值不在此区间，则比较结果为false
    对于第4条的原因： java在编译Integer i = 100 ;时，会翻译成为Integer i = Integer.valueOf(100)。而java API中对Integer类型的valueOf的定义如下，对于-128到127之间的数，会进行缓存，Integer i = 127时，会将127这个Integer对象进行缓存，下次再写Integer j = 127时，就会直接从缓存中取，就不会new了。

## 13. 基本数据类型的字节长度
    1byte = 8bit, char = 2byte, short = 2byte, int = 4byte, long = 8byte, double = 8byte

## 14. 字节码<https://juejin.im/post/5b3782b1e51d4558dc4ae4a7>
    源文件就是.java文件。字节码就是.class文件，Java 代码通过 javac 命令编译成字节码。虚拟机会在内存中划分一块区域，将字节码拿到内存中运行。机器码和本地代码是计算机能够直接识别运行的代码，就是机器指令。众所周知，java的特点之一就是跨平台性，跨平台的结果是运行效率慢，JVM为了增快速度，将某些代码会编译成机器码，以此提高运行效率。

## 15. B树
    B树: 所有叶子节点均在同一层、叶子节点除了包含了关键字和关键字记录的指针外也有指向其子节点的指针只不过其指针地址都为null对应下图最后一层节点的空格子;
    B+树: （1）B+跟B树不同B+树的非叶子节点不保存关键字记录的指针，只进行数据索引，这样使得B+树每个非叶子节点所能保存的关键字大大增加；
    （2）B+树叶子节点保存了父节点的所有关键字记录的指针，所有数据地址必须要到叶子节点才能获取到。所以每次数据查询的次数都一样；
    （3）B+树叶子节点的关键字从小到大有序排列，左边结尾数据都会保存右边节点开始数据的指针。
    B*树: B+树节点满时就会分裂，而B*树节点满时会检查兄弟节点是否满（因为每个节点都有指向兄弟的指针），如果兄弟节点未满则向兄弟节点转移关键字，如果兄弟节点已满，则从当前节点和兄弟节点各拿出1/3的数据创建一个新的节点出来；

## 16. Java注解
    注解的作用：
    1）提供信息给编译器：编译器可利用注解来探测错误和警告信息
    2）编译阶段：软件工具可以利用注解信息来生成代码、html文档或做其它相应处理；
    3）运行阶段：程序运行时可利用注解提取代码

# 三方插件
    https://juejin.im/post/5e65ad276fb9a07cc01a3264 Android热门三方库源码面试宝典
## 1. Glide
    1. 如何监听View是否还存在页面中
    通过创建一个Fragment，寄宿在Activity上，以此来监听Activity的生命周期

    2. 图片的长宽是由什么确定的
    图片占用内存的计算公式：图片高度 * 图片宽度 * 一个像素占用的内存大小。 所以，计算图片占用内存大小的时候，要考虑图片所在的目录跟设备密度，这两个因素其实影响的是图片的宽高，android会对图片进行拉升跟压缩。
    而当我们通过代码，将这张图片加载进内存时，会先解析图片文件本身的数据格式，然后还原为位图，也就是 Bitmap 对象，Bitmap 的大小取决于像素点的数据格式以及分辨率两者了。

    3. 图片的信息是存储在哪里
    存储在图片文件数据中，读取的时候，不同的库会根据不同的情况进行压缩

    4. 为什么要在项目中使用这个库？
        1、多样化媒体加载：不仅可以进行图片缓存，还支持Gif、WebP、缩略图，甚至是Video。
        2、通过设置绑定生命周期：可以使加载图片的生命周期动态管理起来。
        3、高效的缓存策略：支持内存、Disk缓存，并且Picasso只会缓存原始尺寸的图片，内Glide缓存的是多种规格，也就是Glide会根据你ImageView的大小来缓存相应大小的图片尺寸。
        4、内存开销小：默认的Bitmap格式是RGB_565格式，而Picasso默认的是ARGB_8888格式，内存开销小一半。

    5. 三级缓存：强引用->软引用->硬盘缓存

## 2. Okhttp3
    工作原理
    OkHttp的拦截器链

## 3. EventBus
    观察者模式
    因为register是强引用，它会让对象无法得到内存回收，导致内存泄露。所以必须在unregister方法中释放对象所占的内存。

    1. EventBus2.x使用的是运行时注解，它采用了反射的方式对整个注册的类的所有方法进行扫描来完成注册，因而会对性能有一定影响。
    2. EventBus3.x使用的是编译时注解，Java文件会编译成.class文件，再对class文件进行打包等一系列处理。在编译成.class文件时，EventBus会使用EventBusAnnotationProcessor注解处理器读取@Subscribe()注解并解析、处理其中的信息，然后生成Java类来保存所有订阅者的订阅信息。这样就创建出了对文件或类的索引关系，并将其编入到apk中。
    3. 从EventBus3.0开始使用了对象池缓存减少了创建对象的开销。
    4.如果在EventBus的父类和子类同时注册监听会发生什么
    这意味着当子类「显示」实现父类的订阅方法的时候，如果此时发射指定 Event 的话，父类的订阅方法将不会执行，而仅会执行子类的订阅方法。

## 4. LeakCanary
    1.如何判断Activity被持有没有被销毁
    LeakCanary内存泄漏的监控原理是利用了WeakReference对象中的引用对象被回收时，会将其自己存储到ReferenceQueue队列，然后判断ReferenceQueue队列是否有值，来判断对象是否内存泄漏的。

# 网络传输
## 1. http和https，get和post，https建立过程
    1.http和https
    安全套接字层超文本传输协议HTTPS，为了数据传输的安全，HTTPS在HTTP的基础上加入了SSL(Secure Sockets Layer)协议，SSL依靠证书来验证服务器的身份，并为浏览器和服务器之间的通信加密。

    2.https建立过程
    客户端发送请求到服务器端
    服务器端返回证书和公开密钥，公开密钥作为证书的一部分而存在
    客户端验证证书和公开密钥的有效性，如果有效，则生成共享密钥并使用公开密钥加密发送到服务器端
    服务器端使用私有密钥解密数据，并使用收到的共享密钥加密数据，发送到客户端
    客户端使用共享密钥解密数据
    SSL加密建立
    证书是CA颁发给网站的，然后浏览器存有对应的证书

    3.HTTPS和HTTP的区别主要如下：
    1、https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
    2、http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
    3、http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
    4、http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

## 2. 浏览器输入一个url点击发送后发生了什么
    URL 解析:地址解析，输入的是 URL 还是搜索的关键字, HSTS, (安全检查，访问限制), 检查缓存
    DNS 查询:浏览器缓存, 操作系统缓存, 路由器缓存, ISP缓存, 根域名服务器，获取目标的IP地址
    TCP 连接:发送HTTP请求, TCP传输报文, IP协议查询Mac地址, 以太网协议
    处理请求:服务器监听请求, 处理请求（解析协议和验证身份）, 重定向, URL重写
    接受响应:浏览器收到服务器的资源相应，进行相关操作
    渲染页面:HTML, CSS, 渲染树, 布局绘制, JS编译执行

## 3. http1和http2的区别
    1. HTTP1.1和HTTP1.0相比较而言，
        1. 最大的区别就是增加了持久连接支持。在一个TCP连接上可以传送多个HTTP请求和响应。
        2. 支持断点传输。
        3. 1.1增加了更多的请求头和响应头来改进和扩充HTTP 1.0，
        4. HTTP 1.1还提供了与身份认证、状态管理和Cache缓存等机制相关的请求头和响应头。（200, 301, 302, 403, 404, 500）
    2. HTTP2.0
        1. 多路复用，一个连接上可以做多个request
        2. 二进制分帧，方便而且高效，1.1是基于文本解析
        3. 首部压缩，避免header的重复传输，本地缓存了相关的header，避免重复传输，浪费带宽
        4. 服务端推送（Server Push）
    3. HTTPS
    4. HTTP3.0
        1. 基于UDP传输
        2. QUIC协议

## 4. socket长连接
    server的生命周期大致如下：
    1、创建socket。
    2、绑定（bind）地址端口
    3、监听网络连接
    4、接受连接
    5、关闭连接

    client的生命周期则稍微简单点：
    1、创建socket
    2、绑定地址
    3、发起连接
    4、关闭连接

    使用心跳

## 5. TCP3次握手，4次挥手
    第一次握手：客户端发送网络包，服务端收到了。这样服务端就能得出结论：客户端的发送能力、服务端的接收能力是正常的。

    第二次握手：服务端发包，客户端收到了。这样客户端就能得出结论：服务端的接收、发送能力，客户端的接收、发送能力是正常的。 从客户端的视角来看，我接到了服务端发送过来的响应数据包，说明服务端接收到了我在第一次握手时发送的网络包，并且成功发送了响应数据包，这就说明，服务端的接收、发送能力正常。而另一方面，我收到了服务端的响应数据包，说明我第一次发送的网络包成功到达服务端，这样，我自己的发送和接收能力也是正常的。

    第三次握手：客户端发包，服务端收到了。这样服务端就能得出结论：客户端的接收、发送能力，服务端的发送、接收能力是正常的。 第一、二次握手后，服务端并不知道客户端的接收能力以及自己的发送能力是否正常。而在第三次握手时，服务端收到了客户端对第二次握手作的回应。从服务端的角度，我在第二次握手时的响应数据发送出去了，客户端接收到了。所以，我的发送能力是正常的。而客户端的接收能力也是正常的。

    1. 客户端发送一个FIN段，并包含一个希望接收者看到的自己当前的序列号K. 同时还包含一个ACK表示确认对方最近一次发过来的数据。
    2. 服务端将K值加1作为ACK序号值，表明收到了上一个包。这时上层的应用程序会被告知另一端发起了关闭操作，通常这将引起应用程序发起自己的关闭操作。
    3. 服务端发起自己的FIN段，ACK=K+1, Seq=L
    4. 客户端确认。ACK=L+1

## 6. Http请求中包含哪些内容<https://www.jianshu.com/p/80e25cb1d81a>
    1. HTTP请求包含了各种协议，请求头和请求数据

    2. respone: 状态行、消息报头、空行和响应正文。

## 7. WebView
    1. WebView与Android交互，JsBridge
        google推荐方案 JsInterface
        拦截并重写Js消息框
        对请求url进行拦截
    2. WebView中，主要漏洞有三类：
        任意代码执行漏洞
        密码明文存储漏洞
        域控制不严格漏洞
    3. WebView优化<https://www.jianshu.com/p/6179d51281da>
        1.合理使用WebView提供的几种缓存方式
        2.常用资源预加载
        3.WebView在Application中提前初始化
        4.实现WebView复用
        5.另开WebView进程
        6.DNS解析优化（接口与网页主域名一致）
        7.线上资源压缩、CDN加速
        8.静态直出，直接下发首屏html
        9.离线预推，下发离线包，并增量更新
        10.WebView创建与网络请求并行
        11.网页按节点局部刷新
        12.自定义实现图片资源缓存
        13.重新定义图片加载格式，shareP
        14.本地资源拦截替换

# 我的实践
## 1. 做过的项目的难点在哪？
    1.网络消息，架构设计，有能力，有承担，异步阻塞队列，使用的是TCP长连协议
    2.全局主题，工作量巨大，编码不规范引起的，体现吃苦耐劳，认真负责
    3.性能调优，使用Systrace，找到启动速度卡顿，主要是inflat和RecyclerView数据加载，掌握性能优化
    4. 通知分类，短信在国内已经成了一个接受通知的工具了。虽然大家都做了分类和拦截，但是还是有很多的广告无法拦截，有用的信息被沉没在其中。我们做的就是把其中的精华提取出来，能给用户一个更精准的信息通知和展示
    5. 单元测试，方便调试，不依赖编译和真机，确保方法的单一职责，降低重构的风险，强制分离页面和业务

## 2. 重点项目，网络消息，三个耗时过程，需要顺序执行，但是又只能丢到子线程中执行，所以仿造AsyncTask做了一个阻塞队列

## 3. 性能优化做了哪些优化
    android要求每帧的绘制时间不超过16ms
    ViewStub，懒加载，异步初始化SearchView（先是在异步线程里面去inflate这个searchview，再在数据加载完之后去replace），数据限流8条数据
    1. inflate的过程
        1. inflate的本质是读取xml文件，而IO操作就是相当的费时
        2. 创建实例View是通过ClassLoader.loadClass进行反射获取的
    2. 如何优化过度绘制
        原因：
        1. 测量、布局、绘制过程都会存在自顶而下遍历过程，所以如果布局的层级太多，会消耗更多的时间和资源
        2. 如果屏幕某个像素点在同一帧的时间内被绘制多次，会浪费大量的CPU和GPU资源，而又没有实际效果
        解决方案：
        1. 移除window background
        2. 移除控件中不需要的背景
        3. 减少透明度的使用（因为需要渲染两次）
        4. 使用ConstraintLayout减少布局层级（原理）
        5. 使用merge标签减少布局层级（原理）
        6. 使用ViewStub标签延迟加载（ViewStub是一个不可见的View类，不会进行onMeasure和onDraw）
        7. 减少自定义View的过度绘制,使用clipRect()
    3. 除了ViewStub，还有哪些优化方法inflat?
        使用Google的AsyncLayoutInflater(Android 24)
    4. ViewStub原理
        1. ViewStub 是一个看不见的，没有大小，不占布局位置的 View，可以用来懒加载布局。
        2. 当 ViewStub 变得可见或 inflate() 的时候，布局就会被加载（替换 ViewStub）。因此，ViewStub 一直存在于视图层次结构中直到调用了 setVisibility(int) 或 inflate()。把自己从parent中删除，那么获取的parent就为null。
        3. 在 ViewStub 加载完成后就会被移除，它所占用的空间就会被新的布局替换。
        4. 使用了弱引用管理对象的创建
        5. 在onMeasure中设置了setMeasuredDimension(0, 0);设置WILL_NOT_DRAW之后，onDraw()不会被调用，通过略过绘制的过程，优化了性能。
    5. merge标签
        可以替代根布局，达到减少层级，复用View的效果
        在inflate过程中，如果碰到merge，就会直接将子布局添加到parent中，把merge抛弃
    5. ToolBar的menu重复加载
    6. 头像和名字的刷新使用局部刷新

    6. 附件栏的滑动冲突解决
    自定义ViewGroup，和GridView，和ViewPager的滑动冲突
    外部拦截法，由父布局去拦截，不能拦截down或者up，只能拦截move，因为down拦截了，就无法知道target view是谁了
    内部拦截法，父View不拦截事件，由子View来决定事件拦截，如果子View需要此事件就直接消耗掉，如果不需要就交给父View处理。这种方法需要配合requestDisallowInterceptTouchEvent方法来实现。

    7. 信息搜索框优化卡顿
    动画优化，将animation改成animator
    RelativeLayout改成FrameLayout

    8. RecylerView滑动优化
    bindView的时候去load uri
    bindView的时候去new Class，比如说checkbox的listener
    bindView的时候去触发binder通讯，判断是否为默认的apk

    9. 批量删除优化
        1. 删除会话时会去更新同步通知状态
        2. 删除收藏信息，是调用了两次delete
        3. 删除时要同步远程数据库，需要获得远程的thread_id

    10. 通知优化
        1. 修改原生逻辑，不再弹出多个Conversation和Message，避免遍历耗时
        2. 只发最新的一条通知出来，不会将全部的未读conversation发出来

## 4.性能优化
    滑动卡顿
    内存优化
    内存泄漏

### 3.1 merge

## 4. 数据埋点上报的机制，做了哪些网络优化

## 5. 单元测试怎么写的，遇到了什么困难
    PowerMockmock私有属性和方法  测试私有属性和方法 mock final修饰的方法  mockStatic静态方法  还有就是它还可以whenNew来mock方法内部实例化的变量，然后像傀儡一样操作它。
    Robolectric这是一个专门测试android系统类相关的东西的，比如UI点击事件，Actvity和Service等四大组件生命周期，Dialog，Application，Bitmap，Resouce等进行行为测试，它之所以能测试他们，其实也是Mock了这些对象，然后可以动态代理这些对象，不过他用的不是mock,而是ShadowXX，Android系统的每个几乎都有一个相对应的ShadowXX，我们测试的时候得到对应的ShadowXX就可以像操作傀儡一样操纵原来的对象了，就连Loop都有ShadowLoop专门用来测试的时候操作和调度线程的。

## 6. 有什么办法提高第二次文件扫描的效率，从技术角度说明。
    多线程和文件过滤器？
    缓存，索引

# 待补充
## 2. pss内存
    应用实际运行占用内存
--------------------------------------
20. 信号量，栅栏，闭锁的区别，

32. 数据库升级的过程，是如何从旧表拷贝到新表

55. lamba

56. Kotlin
    协程，主要用于处理多线程的事情

60. flutter
平台兼容
响应式编程

34. RN

35. AutoSize

39. <https://segmentfault.com/a/1190000019513862> 开发工程师中级面试题

40. 时间复杂度计算logn

99. 我叫张家伟，目前就职于东方藏山资产管理有限公司，入职一年，主要负责公司保理系统的设计和开发。我工作认真负责，交代的每一件事情都能以高质量完成。并且乐于沟通，喜欢参与团队合作，周围的同事也乐于与我合作，有什么问题也会经常找我帮忙。我自学能力和适应能力强，之前自学过多个编程语言。都有用过它们完成一些小的项目。目前也在学习Kotlin。用kotlin进行需求开发。
    1.业务查询
    2.业务审批
    3.电子签章
    4.客户管理
    5.工作进度管理

    1.业务查询
    2.业务审批
    3.电子签章

00. 离职原因：1.工作太多，事情太多，没有时间总结，沉淀和提升自己 2.感受不到产品的价值，觉得需求太小没意义，bug标准太严发布不出去 3.学习技术的热情

职位意向
有哪些不足
有什么好的发展建议
用了哪些比较新的技术
从手机到互联网，需要怎么学习和发展
都用了哪些流行的插件

协程切换：https://juejin.cn/post/6981056016897015838
虚拟内存：https://www.jianshu.com/p/b34ecdfa94b5
HTTP：https://zhuanlan.zhihu.com/p/342311013


项目亮点和难点
1.表格
2.MPAndroid
3.拦截器刷新token
4.筛选框
5.组件化
6.依赖注入
