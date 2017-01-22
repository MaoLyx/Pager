#Dagger2 的基本使用
> 话不多说直接开整
>
>[Dagger2的官方文档](https://google.github.io/dagger/)



##Dagger2 

###在项目中添加依赖
在Android Studio项目中依赖Dagger2 如下



1. 首先在project的build.gradle中添加

		dependencies {
   		 ... // 其他classpath
    	classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8' //添加apt命令
		}

2. 然后在Module的build.gradle中添加如下代码：
		
		apply plugin: 'com.neenbedankt.android-apt'//添加apt命令
		
		dependencies {
		    apt 'com.google.dagger:dagger-compiler:2.0.2' //指定注解处理器
		    compile 'com.google.dagger:dagger:2.0.2'  //dagger公用api
		    provided 'org.glassfish:javax.annotation:10.0-b28'  //添加android缺失的部分javax注解
		}

###Dagger2的注释

Dagger2 是基于Java注解来实现依赖注入的，那么在这里先总结一下Dagger2中的注解。Dagger2中的注解包括：@Inject, @Module, @Provides, @Component, @Qulifier, @Scope, @Singleten。

* **@injectr &nbsp;:**@inject有两个作用，一个是告诉Dagger2为期提供依赖；二是用来标记构造函数，在Dagger2需要这个类的时候找到这个类的构造函数并创建实例，
