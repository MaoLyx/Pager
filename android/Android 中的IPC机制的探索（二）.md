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

