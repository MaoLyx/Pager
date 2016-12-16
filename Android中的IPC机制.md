#Android 中的IPC机制的探索

>这篇文章是我学习Android 源码的一个笔记，一是为了以后查找翻阅方便；二希望能帮助到其他人。


##什么是IPC
IPC 是Inter-Process Communication 的简写，译为进程间通讯；说到这里先讲下什么是**进程**，什么是**线程**。先说说**线程**，**线程**是CUP调度的最小单位也是一种有限的系统资源。 而**进程**指的是一个执行单元及是一个应用程序；在一个进程中可以只有一个线程（即一个主线程），也可以有多个线程。在Android的应用中，应用启动后就有一个主线程（UI线程），在主线程中的才控制各个界面的控件。如果在UI线程中做了大量的耗时操作会导致UI卡顿的现象，严重会引起ANR错误。

##Android中的多进程
在Android中开启多进程非常的简单只需要在AndroidManifest中给四大组件声明android:process属性就可以开启多进程了；但是开启多进程后也会带来一些意向不到的问题。
###Android的开启多进程的方法
在Android中开启多进程的方法就只有一种，就是在AndroidManifest中对四大组件声明android:process属性；在具体实现的操作层面上有三个不同的方法；在下面对一串代码中可一一展示。

       <activity android:name="com.example.maohongyu.myapplication.FirstActivity">
            <intent-filter >
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <activity 
        	  android:name="com.example.maohongyu.myapplication.SecondActivity"
              android:process=":process2"/>
        <activity 
        	  android:name="com.example.maohongyu.myapplication.ThirdActivity"
              android:process="com.example.maohongyu.myapplication.thirdProcess"/>

以上是以Activity为例示范多进程的开启方法，当然Service，BroadcastReceiver,ContentProvider等都可以通过设置Android:process=“”属性来开启一个线程。

在上面的例子中三个Activity的process属性的设置略有不同下，面就具体数下他的不同之处。

**FirstActivity  :** FirstActivity的申明代码中并没有设置process属性，在这个时候，系统把他运行在默认的进程中，默认的进程名为应用的包名。

**SecondActivity :** SecondActivity的android:process属性写的是":process2",这时系统会创建一个新的进程运行这个Activity。而这个新的进程名为: 包名+：process2。在SecondActivtiy属性中的**":"**的含义是在当前的进程名前加上包名的意思，所以process完整的进程名为com.example.maohongyu.myapplication:process2。而且以**":"**开头的进程为当前应用的私有进程，其他应用的组件不能和他跑在同一个进程中。

**ThirdActivity  :** ThirdActivity的process属性是以一个完整的方式写的，所以不会再前面加其他额外的东西，而且他不是一**"："**开头的的进程属于全局进程，其他应用可以通过分析UID的方式跟他分享数据。
###多进程机制的运行机制
在Android中系统为每一个应用或着是说每一个进程都独立分配一个的虚拟机，不同的虚拟机在内存上分配不同的内存地址，所以这到导致不同的虚拟机上访问同一个内的对象会产生不同的的副本，这也就导致了静态成员和单例模式失效。一般的来说多进程模式下会导致如下的一些问题：

* 静态成员和单例完全失效
* 线程同步机制失效
* SharedPreference可靠性下降
* Application会被多次的创建



第一个问题在上面已经说清楚了；第二个问题和第一个问题情况类似既然都不是一个内存地址了所以同步锁或是其他的操作也是无效的；第三个是Shared不支持两个进程同时的读写操作；最后一个就不用说了。