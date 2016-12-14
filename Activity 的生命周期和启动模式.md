#**Activity 的生命周期和启动模式**

>最近突然闲下来了，突然想想做了Android那么久一直没有时间来系统的整理一下Android的一些知识。所以在这里从简单看开始算是对自己的一个总结。希望推陈出新，也能帮到刚刚学习的Android的同学们。



__________________________
##Activity 的生命周期
说到**Activity**的生命周期相信有过点Android基础的同学都能脱口而出Activity生命周期的几个回调。  
```
onCreate(); ---> onStart();--->onResume();--->onPause();--->onStop();---> onDestroy();
```
但是这些回调你真的弄懂他们了吗？

*onCreate();* 和 *onDestroy();* 是一组对应的回调他们是在Activity创建和销毁的时候调用的。

*onStart();* 或是*onReStart();* 和 *onStop();*是一组对应的回调，他们对应在Activity可见和不时掉用。*onStart();* 只会在创建后调用一次之后*onReStart();*替代*onStart();&nbsp;&nbsp;*而*onStop();*只是在Activity**不可见**时调用；

*onResume();*和*onPause();*这组回调是Activity获得焦点时和失去焦点是的一组回调；

总结下*onStart();* 或是*onReStart();*是一组基于是否可视的角度设计的API,而*onResume();*和*onPause();*是基于**Activity**是否在前台而设计的API。


-
##Activity 的启动模式

Activity的启动模式是Android为了避免系统重复创建Activity实例而采取的一种策略；Activity的启动模式有4种。分别是**standard**，**singleTop**,**singleTask**,**singleInstance**。

**standard  :** 标准模式（默认）  在这种情况下每一次启动一个Activity系统都会创建一个Activity的实例，不管任务占中是否存在这个Activity的实例；

**singleTop  ：**  栈顶复用    在这种情况下，系统在启动Activity是会在任务占中查找是否有这个Activity的实例并且是位于任务栈顶的；如果没有没有位于栈顶就创建一个新的实例，如果在栈顶就不穿件行的实例，直接调用onNewItent()这个回调；要注意的是这个时候，Activity的*onCreate和onStart()*方法是不会被系统调用的。

**singleTask  :**  栈内复用模式   在这中模式下，系统在启动Activity时，会先在栈内查找Activty实例，如果有的话不再次创建Activity的实例对象，而是直接把站内的实例对象推到栈顶，值得一提的是如果该实例之上还有其他Activity的实例的话系统会将他们统统的Kill掉；

**singleInstance  :**   单例模式    在这个模式下Activty只能单独的存在于一个单独的任务栈中；


