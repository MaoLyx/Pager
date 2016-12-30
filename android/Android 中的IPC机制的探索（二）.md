#Android 中的IPC机制的探索（二）

>这篇文章将IPC中基本的三个内容：Serializable、Parcelable接口和Binder,在熟悉这些内容以后才能更好的去理解跨进程通讯的各种方式；


-------


##Serializable接口

**Serializable**接口是Java提供的一个序列化接口，点进源码看就知道这是一个空标识接口。使用**Serializable**来实现序列化也非常的简单。在一个类中实现Serialiazable接口然后在类中指定一个serialVersionUID就可以了。不指定serialVersionUID也行但是在后面的使用中可能会出现错误。


##Parcelable接口
这是Android提供的一种新的序列化接口，只要一个类实现了这个接口就可被序列化了。


	package com.maohongyu.parcelabletest;

	import android.os.Parcel;
	import android.os.Parcelable;

	/**
	 * Created by maohongyu on 16/12/20.
 	 */
	public class Book implements Parcelable {
    private int bookID;

    private String name;

    private String Author;

    public Book(int bookID, String name, String author) {
        this.bookID = bookID;
        this.name = name;
        Author = author;
    }

    public Book() {
    }

    protected Book(Parcel in) {
        bookID = in.readInt();
        name = in.readString();
        Author = in.readString();
    }

    public static final Creator<Book> CREATOR = new Creator<Book>() {
        @Override
        public Book createFromParcel(Parcel in) {
            return new Book(in);
        }

        @Override
        public Book[] newArray(int size) {
            return new Book[size];
        }
    };

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeInt(bookID);
        dest.writeString(name);
        dest.writeString(Author);
    }

    @Override
    public String toString() {
        return "Book{" +
                "bookID=" + bookID +
                ", name='" + name + '\'' +
                ", Author='" + Author + '\'' +
                '}';
    }
	}

Parcelable的主要方法如下图：

![主要方法](https://raw.githubusercontent.com/MaoLyx/Pager/master/android/image/parcelable%E4%B8%BB%E8%A6%81%E6%96%B9%E6%B3%95.png)

>在实际的Android开发中，如果不涉及到OI和网络传输序列化一般就使用parcelable，而涉及OI的话选用Serializable。


##Binder

Binder是Android中跨进程的通信方式，该通讯方式在Linux中没有；从Android framework的角度说，Binder是ServiceManager链接各种Manager和相应的ManagerService的桥梁；从Android应用层来说，Binder是客户端和服务端进行通信的媒介，当binderService的时候，服务端会返回一个包含服务端业务调用的Binder对象，客户端就可以获取服务端提供的服务或数据，这里的服务包括普通服务和基于AIDL的服务。