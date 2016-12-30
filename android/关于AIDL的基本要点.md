#什么叫做AIDL

AIDL (Android Interface Definition Language)

|AIDL 		  |IPC     |多个应用程序|多线程
|binder     |只有IPC  |没有多线程  |多个应用程序
|Messenger  |只有IPC  |没有多线程 | 


##在Android Studio中创建AIDL
	1.右键单击Java点击弹出菜单，在New菜单中选择folder——>AIDL Foler;
	2.右键单击AIDL Foler, new --->AIDL--->AIDL File;
	3.reduild 重新编译后可以使用；
##在AIDL中传递的数据类型
AIDL可以传递的数据类型有：Byte,int,long,boolean,foalt,double,chart,String,ChartSequence,List,Map,Parcelable;

	
Note:

* android 5.0 后系统不允许使用隐式的挑战方式启动服务。
* 在parcelble反序列化过程中，取值的先后顺序要和序列化的先后顺序一致；
* AIDL 中传递List Map ,在传递参数的过程中要在数据类型前面定义是in 、out、inout;(意思是描述数据是输入修改的，还是输出修改的，还是输入输出都可以修改的)；
* 自定义数据类型要在AIDL中申明parcelable；
* 在使用自定义数据类型的时候，不论是否处在同一个包内，在使用时依然要导包；
* Android Studio总声明的AIDL和实现了Parcelable的Java实体类的包名要一样；