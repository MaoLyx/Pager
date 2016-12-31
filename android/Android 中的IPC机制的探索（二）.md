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

Binder是Android中跨进程的通信方式，该通讯方式在Linux中没有；从Android framework的角度说，Binder是ServiceManager链接各种Manager和相应的ManagerService的桥梁；从Android应用层来说，Binder是客户端和服务端进行通信的媒介，当binderService的时候，服务端会返回一个包含服务端业务调用的Binder对象，客户端就可以获取服务端提供的服务或数据，这里的服务包括普通服务和基于[AIDL](http://blog.csdn.net/neil_or/article/details/53955802)的服务。下面将从AIDL来分析Binder的工作原理。

我们在工程中建三个文件，Book.java  Book.aidl 和  IBookManager.aidl;

代码编译之后系统会自动的生产一个IBookManager.java 文件；下面我们就来看看这个类。

	/*
 	* This file is auto-generated.  DO NOT MODIFY.
 	* Original file: /Users/maohongyu/demo/BinderTest/app/src/main/aidl/com/maohongyu/	bindertest/IBookManager.aidl
 	*/
 	------------------------------------（以上是系统对文件的一些描述）
 	//包名
	package com.maohongyu.bindertest;
	
	//由此看的出来这是一个Java 接口
	public interface IBookManager extends android.os.IInterface
	{
	
	
	======================================（下是一个继承了Binder，实现了IBookManager自己接口的一个 Java抽象类）
	
	public static abstract class Stub extends android.os.Binder implements com.maohongyu.bindertest.IBookManager
	{
	
	//Binder的唯一标示，用当前的类名来表示；
	private static final java.lang.String DESCRIPTOR = "com.maohongyu.bindertest.IBookManager";
	
	/** 构造方法*/
	public Stub()
	{
	this.attachInterface(this, DESCRIPTOR);
	}
	
	
	/**
 	* Cast an IBinder object into an com.maohongyu.bindertest.IBookManager interface,
 	* generating a proxy if needed.（译文：将一个IBinder对象强转成IBookManager，如果需要会创建一个代理）
 	*/

	public static com.maohongyu.bindertest.IBookManager asInterface(android.os.IBinder obj)
	{
		if ((obj==null)) {
			return null;
		}
			android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
		if (((iin!=null)&&(iin instanceof com.maohongyu.bindertest.IBookManager))) {
			return ((com.maohongyu.bindertest.IBookManager)iin);
		}
		return new com.maohongyu.bindertest.IBookManager.Stub.Proxy(obj);
	}


	@Override 
	public android.os.IBinder asBinder()
	{
		return this;
	}
	
	
	@Override 
	public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException
	{
		switch (code)
		{
			case INTERFACE_TRANSACTION:
			{
			reply.writeString(DESCRIPTOR);
			return true;
			}
			case TRANSACTION_getBookList:
			{
				data.enforceInterface(DESCRIPTOR);
				java.util.List<com.maohongyu.bindertest.bean.Book> _result = this.getBookList();
				reply.writeNoException();
				reply.writeTypedList(_result);
				return true;
			}
			case TRANSACTION_addBook:
			{
				data.enforceInterface(DESCRIPTOR);
				com.maohongyu.bindertest.bean.Book _arg0;
				if ((0!=data.readInt())) {
					_arg0 = com.maohongyu.bindertest.bean.Book.CREATOR.createFromParcel(data);
				}
				else {
					_arg0 = null;
				}
				this.addBook(_arg0);
				reply.writeNoException();
				return true;
			}
		}
		return super.onTransact(code, data, reply, flags);
	}
	****************************************
	//**存根类的一个代理类*/
	private static class Proxy implements com.maohongyu.bindertest.IBookManager
	{
		private android.os.IBinder mRemote;
		
		Proxy(android.os.IBinder remote)
		{
		mRemote = remote;
		}
		
		@Override 
		public android.os.IBinder asBinder()
		{
		return mRemote;
		}
		
		public java.lang.String getInterfaceDescriptor()
		{
		return DESCRIPTOR;
		}
		
		//**这实现了本接口IBookManager的方法*/
		@Override 
		public java.util.List<com.maohongyu.bindertest.bean.Book> getBookList() throws android.os.RemoteException
		{
			android.os.Parcel _data = android.os.Parcel.obtain();
			android.os.Parcel _reply = android.os.Parcel.obtain();
			java.util.List<com.maohongyu.bindertest.bean.Book> _result;
			try {
				//将存根的描述写成TOKEN;
				_data.writeInterfaceToken(DESCRIPTOR);
				//掉用远程的transact方法；
				mRemote.transact(Stub.TRANSACTION_getBookList, _data, _reply, 0);
				
				_reply.readException();
				_result = _reply.createTypedArrayList(com.maohongyu.bindertest.bean.Book.CREATOR);
			}
			finally {
				_reply.recycle();
				_data.recycle();
			}
			return _result;
		}
		
		@Override 
		public void addBook(com.maohongyu.bindertest.bean.Book book) throws android.os.RemoteException
		{
			android.os.Parcel _data = android.os.Parcel.obtain();
			android.os.Parcel _reply = android.os.Parcel.obtain();
			try {
				_data.writeInterfaceToken(DESCRIPTOR);
				if ((book!=null)) {
				_data.writeInt(1);
				book.writeToParcel(_data, 0);
			}
			else {
				_data.writeInt(0);
			}
				mRemote.transact(Stub.TRANSACTION_addBook, _data, _reply, 0);
				_reply.readException();
			}
			finally {
				_reply.recycle();
				_data.recycle();
			}
		}
	
	}
	
	//这里是对代理实现跟本接口的方法进行编号
	static final int TRANSACTION_getBookList = (android.os.IBinder.FIRST_CALL_TRANSACTION + 0);	
	static final int TRANSACTION_addBook = (android.os.IBinder.FIRST_CALL_TRANSACTION + 1);
	}
	================================================ //Binder抽象类结束	
	
		// 一下是写在AIDL中的接口的方法；
	
	    public java.util.List<com.maohongyu.bindertest.bean.Book> getBookList() throws android.os.RemoteException;
	
	
	    public void addBook(com.maohongyu.bindertest.bean.Book book) throws android.os.RemoteException;
	}
	
简化一下也就是下面这个图：

![AIDL原理图]()
	
	

