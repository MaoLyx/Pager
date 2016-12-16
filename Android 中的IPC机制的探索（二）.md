#Android 中的IPC机制的探索（二）

>这篇文章将IPC中基本的三个内容：Serializable、Parcelable接口和Binder,在熟悉这些内容以后才能更好的去理解跨进程通讯的各种方式；


-------


##Serializable接口

**Serializable**接口是Java提供的一个序列化接口，点进源码看就知道这是一个空标识接口。使用**Serializable**来实现序列化也非常的简单。在一个类中实现Serialiazable接口然后在类中指定一个serialVersionUID就可以了。（不指定serialVersionUID也行但是在后面的使用中可能会出现错误）


##Parcelable接口
