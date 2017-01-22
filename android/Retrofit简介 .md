# Retrofit 


![Retrofit](http://upload-images.jianshu.io/upload_images/1307647-0f7152781754a167.png?imageMogr2/auto-orient/strip%7CimageView2/2)

Retrofit 是Square的一个开源网络访问工具类；这里结合官方文档来介绍一下他的具体使用。[官方文档地址](http://square.github.io/retrofit/)

#导入Retrofit 到Android Studio项目中去

在项目中的app.build文件中添加依赖：

	compile 'com.squareup.retrofit2:retrofit:2.1.0'

加载之后就可以使用Retrofit了。

#Retrofit的使用

* 创建一个接口将网络访问的API写在里面，这个接口的命名按照官方文档的格式后缀一Service结尾；
		
		public interface HttpService{
			@GET("/")
			Call<String> getBaidu();
			
		}
		
* 创建Retrofit实例，代码如下：
	
	
		Retrofit retrofit =new Retrofit.Builder()  //返回Retrofit对象
									.baseUrl("http://www.baidu.com")//设置根地址
									.addConverterFactory(GsonConverterFactory.create())//解析拿到的数据
									.build();//完成创建
* 利用Retrofit实例闯将一个实现了步骤1的接口的文档；通过service调用相应的方法得到一个Call对象。
	
		HttpService service = retrofit.create(HttpService.class);
		Call<String> baidu = service.getBaidu();
		
* 然后同步或异步的执行Call对象访问网络；
		
------

>Call instances can be executed either synchronously or asynchronously. Each instance can only be used once, but calling clone() will create a new instance that can be used.

以上是官方文档对Call对象的一段描述，其大概意思是Call对象能同步或异步的发起网络请求但是每个对象只能发起一次请求，可以通过clone（）方法创建新的实例继续发起请求。

备注：以下是Square提供的常用数据解析裤：

* Gson: com.squareup.retrofit2:converter-gson
* Jackson: com.squareup.retrofit2:converter-jackson
* Moshi: com.squareup.retrofit2:converter-moshi
* Protobuf: com.squareup.retrofit2:converter-protobuf
* Wire: com.squareup.retrofit2:converter-wire
* Simple XML: com.squareup.retrofit2:converter-simplexml
* Scalars (primitives, boxed, and String): com.squareup.retrofit2:converter-scalars
		
	