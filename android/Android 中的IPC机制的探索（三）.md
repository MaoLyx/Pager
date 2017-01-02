#Android 中的IPC机制的探索（三）

>在这里将要探索Android 中的IPC（跨进程）的方式；之前探索了两种序列化和Binder的基本内容，到这里要真正的开始探索IPC的几种实现方式；
>

**********


##Bundle
四大组件的Activity、Service、Receiver都支持在使用Intent通过Bundle传递数据，因为Bundle是一个实现了Parcelable接口的一个类，所以他可以很方便的在不同的进程中传递。所以基于这一点，当我们启动了另一个进程的Activity、Service、Receicer的时候，我们就可以在Bundle中附加上我们需要传递的给远程进程的数据。当然所传递的数据也是要求是序列化了的。

##共享文件
共享文件在实现是跨进程同的即是两个进程通过读、写同一个文件来实现同一的文件来交换数据。比如：A进程把数据写入文件，然后B进程通过这个文件将数据获取。当然在具体实现的过程中要注意并发问题。

当然要注意一下SharedProference，它是Android中提供的轻量级储存方案，它的底层是XML键值对的格式；他的文件路径是当前包所在的data目录下，data/data/package name/shared_prefs(package name是当前包名)。因为系统对它有一定的缓存策略，即在内存中备份了一份SharedProference的文件，这使得在高并发的读写中会存在数据的丢失，所以不建议在IPC中使用。

##Messenger

先来看依稀Messenger信使的工作原理图。

![Messeger工作原理图](https://github.com/MaoLyx/Pager/blob/master/android/image/Messeger%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.png?raw=true)

Messenger我们可以通过他在不同的进程中传递一个Message对象，在Message中放入我们需要传递的数据可以了。Messenger的底层也是用AIDL来实现的。而它的具体实现如下：

* 服务端：1.创建一个Service来出理客户端的连接请求；2.创建一个Handler来创建一个Messenger对象；3.将这个Messenger对象底层的Binder通过onBind方法返回出去。
* 客户端：首先绑定服务端，然后用返回的Binder创建一个Messenger，然后用这个Messenger传递一个包含有用数据的Message对象到服务器。要服务器应答的话，在客户端创建一个Handle对象并用这个Handle创建一个Messenger，用Message的replyTo方法将这个传递给服务端，服务端就可以用这个Messenger响应客户端了。

##AIDL

使用AIDL详见，[《关于AIDL的基本要点》](http://blog.csdn.net/neil_or/article/details/53955802),和[ 《Android 中的IPC机制的探索（二）# Binder》](http://blog.csdn.net/neil_or/article/details/53956998#t2)相关内容。





