---
layout:     post
title:      Android引入Kotlin
subtitle:   Kotlin的优点
date:       2020-01-07
author:     adarcy
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - kotlin
---

## Android引入Kotlin

### 1、引入原因
#### 1.1 kotlin的优点
- 更少的空指针异常
- 更少的代码量
- 更快的开发速度
- 一致的开发体验

#### 1.2 空安全
在Q3季度的崩溃汇中，java.lang.NullPointerException占到了73%，kotlin可以比较优雅的处理判空问题，避免一堆if判空的代码。

- 定义一个可空类型的变量或方法

		var a : Int = 12 //a不可为空  a=null会报错
		
		var b : String？ = 12// 使用b变量时会提示你需要进行空判断
		
		fun funNullMethod() : Int? {
		val str : String? = "123456"
		return str?.length
		}

#### 判空的常用方式有 ?. !!. let
#### ?. 

该符号的用法为：可空类型变量?.属性/方法。如果可空类型变量为null时，返回null

这种用法大量用于链式操作的用法中，能有效避免空引用异常（NullPointException），因为只要链式其中的一个为null，则整个表达式都为null

	//java 写法
	if（item != null）{
		item.isRecord = true;
		if(item.user != null){
			tv.setText(item.user.name);
		}
	}
	
	//kotlin写法  
	item?.isRecord = true
	tv.setText(item?.user?.name)

#### !!.
!!操作符可谓是给爱好空引用异常（NullPointException）的开发者使用，因为在使用一个可空类型变量时，在该变量后面加上!!操作符，会显示的抛出NullPointException异常

#### let
let操作符的作用：当时用符号?.验证的时候忽略掉null

let的用法：变量?.let{ ... }

	val arrTest : Array<Int?> = arrayOf(1,2,null,3,null,5,6,null)
	// 传统写法
	for (index in arrTest) {
	    if (index == null){
	        continue
	    }
	    println("index => $index")
	}
	
	// let写法
	for (index in arrTest) {
	    index?.let { println("index => $it") }
	}

### 2、引入kotlin
#### 2.1、引入方式
	//在project下的build.gradle中添加
	buildscript {
	    ext.kotlin_version = '1.3.50'
		...
	   }
	
	    dependencies {
	       ...
	        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
	    }
	}
	
	//在common module下的build.gradle中添加
	    api "org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
	
	//在app的build.gradle中添加
	apply plugin: 'kotlin-android'
	
	
	//在需要直接使用布局view Id的模块下的build.gradle中添加
	apply plugin: 'kotlin-android-extensions'


#### 2.2、使用kotlin开发新功能

我的评论与回复我的功能已经使用kotlin开发

### 3、使用注意
#### 3.1、kotlin-android-extensions
kotlin-android-extensions 是用来替代findViewById的插件，假设有一个布局文件 activity_main，那么在Activity类里面，只需要把布局文件import进去即可，import之后直接根据控件的id即可进行UI操作，如下：

	//在需要直接使用布局view Id的模块下的build.gradle中添加
	apply plugin: 'kotlin-android-extensions'
	
	//java
	TextView tv_title = findViewById(R.id.tv_title);
	TextView tv_confirm = findViewById(R.id.tv_confirm);
	tv_title.setText("我的评论");
	tv_confirm.setText("确定");
	
	//kotlin
	import kotlinx.android.synthetic.main.activity_main.* //这个是自动导入的
	tv_title.setText("我的评论")
	tv_confirm.setText("确定")

#### 3.2、kotlin-kapt

如果你的Kotlin代码里面有使用到注解，那么需要加入这个插件（kapt 即 Kotlin annotation processing tool，Kotlin 注解处理工具的缩写） 需要对注解的支持annotationProcessor替换成kapt

kapt注解处理任务并没有利用开启gradle的构建缓存，需要我们手动增加配置开启
开启方式：在项目的app module下的 build.gradle 文件增加如下代码

	apply plugin: 'kotlin-kapt'
	
	kapt {
	    useBuildCache = true
	}

	 
### 	 字符串拼接
- 更简洁的字符串
- 引入了字符串模版

加入了三个引号"""来方便长篇字符的编写

		String str2 = "line1\n" +
		                "line2\n" +
		                "line3";
                
        val str2 = """
    				line1
			        line2
			        line3
			        """
		
同时，Kotlin中引入了字符串模版，方便字符串的拼接，可以用$符号拼接变量和表达式

	fun testString2() {
	    val strings = arrayListOf("abc", "efd", "gfg")
	    println("First content is $strings")
	    println("First content is ${strings[0]}")
	    println("First content is ${if (strings.size > 0) strings[0] else "null"}")
	}

#### 3.3 全局变量的使用
全局变量需要使用 companion object包裹起来，否则会无法引用

	companion object{
	val TYPE_MY_COMMENT = 1
	val TYPE_MY_COMMENT_REPLY = 2
	}
	
### 常量和静态方法
	
#### 	常量
	
	Java中：
	   class StaticDemoActivity {
	         public static final String LOAN_TYPE = "loanType";
	         public static final String LOAN_TITLE = "loanTitle";
	    }
	Kotlin中：
	  class StaticDemoActivity {
      companion object {
           val  LOAN_TYPE = "loanType"
           val  LOAN_TITLE = "loanTitle"
    }
	
	或者
	
	  class StaticDemoActivity {
	      companion object StaticParams{
	            val  LOAN_TYPE = "loanType"
	            val  LOAN_TITLE = "loanTitle"
	    }
	}
	或者
	  class StaticDemoActivity {
	      companion object {
	         const val LOAN_TYPE = "loanType"
	         const val LOAN_TITLE = "loanTitle"
	    }
	}
注：const 关键字用来修饰常量，且只能修饰 val，不能修饰var, companion object 的名字可以省略，可以使用 Companion来指代

引用常量（这里的引用只针对于java引用kotlin代码）

	TestEntity类引用StaticDemoActivity中的常量
	
	   class TestEntity {
	        public TestEntity () {
	            String title = StaticDemoActivity.Companion.getLOAN_TITLE();
	    }
	  }
	
	  或者
	
	  class TestEntity {
	        public TestEntity () {
	            String title = StaticDemoActivity.StaticParams.getLOAN_TITLE();
	        }
	  }
	
	  或者
	
	  class TestEntity {
	        public TestEntity () {
	            String title = StaticDemoActivity.LOAN_TITLE;
	            String type= StaticDemoActivity.LOAN_TYPE;
	        }
	  }

#### 静态方法

	Java代码：
	      class StaticDemoActivity {
	          public static void test(){
	                、、、
	          } 
	      }
	Kotlin中：
	      class StaticDemoActivity {
	          companion object {
	               fun test(){
	                    、、、
	                }
	          }
	      }
	
	  或者
	
	       class StaticDemoActivity {
	          companion object StaticParams{
	              fun test() {
	                  、、、
	              }
	          }
	      }

引用静态方法（这里的引用只针对于java引用kotlin代码）

	TestEntity类引用StaticDemoActivity中的静态方法
	
	    class TestEntity {
	          public TestEntity () {
	                StaticDemoActivity.Companion.test();
	          }
	    }
	
	或者
	
	    class TestEntity {
	          public TestEntity () {
	             StaticDemoActivity.StaticParams.test();
	          }
	    }
companion object {}中用来修饰 静态常量，或者静态方法，单例等等

### data class 的使用
默认实现了copy、toString,还有componentN等方法

定义一个 data class 类

	data class Country(var id: Int, var name: String, var continent: String)
	
编译器自动实现的函数举例

- toString()

		var china = Country(0, "中国", "Asia")
		println(china) // Country(id=0, name=中国, continent=Asia)
	
- copy()

		var japan = china.copy(id = 1, name = "日本")
		println(japan) // Country(id=1, name=日本, continent=Asia)
	
- componentN()
	
		val (id, name, continent) = china
		println("$id --> $name，$continent") // 0 --> 中国，Asia

### 函数继承与实现、复写等
- 父类需要open才可以被继承(kotlin默认为final)
- 父类的方式、属性需要open才可以被覆写
- 接口、接口方法、抽象类默认为open
- 覆写父类（接口）成员需要override关键字
- 注意继承类时，实际上是调用了父类的构造方法

		abstract class Person(open val age: Int){
		    abstract fun work()
		}
			
		class MaNong(age: Int): Person(age){
		    override val age: Int
		        get() = 0
		    override fun work() {
		        println("我是码农，我在写代码")
		    }
		}
			
		class Doctor(override val age: Int): Person(age){
		    override fun work() {
		        println("我是医生，我在给病人看病")
		    }
}

### Object关键字
- 只有一个实例的类
- 不能自定义构造函数
- 可以实现接口、继承父类
- 本质上就是单例模式最基本的实现

		object TestKotlin
		------------------------------转化为java代码
		public final class TestKotlin {
		   public static final TestKotlin INSTANCE;
		
		   private TestKotlin() {
		   }
		
		   static {
		      TestKotlin var0 = new TestKotlin();
		      INSTANCE = var0;
		   }
		}

### companion object伴生对象、静态变量成员还有 kotlin包级成员
- kotlin允许不在类下面写成员变量跟方法，称为包级别对象/函数
- 每个类可以对应有一个伴生对象companion object
- 伴生对象companion object与java的static静态方法/静态成员的效果类似相同。
- kotlin中的静态成员考虑用包级函数、变量代替
- JvmField、JvmStatic、@file:JvmName(自己自定义的类名)

		package com.demo.dan.voice
		
		val Str = "包级成员"
		fun packFun(): String {
		    return "包级函数"
		}
		
		class TestKotlin {
		    companion object {
		        fun compainFun(): String {
		            return "伴生对象方法"
		        }
		        var Str = "伴生对象成员"
		    }
		}


		//--------------在kotlin中调用
		fun main() {
		    println(Str)//包级成员
		    println(packFun())//包级函数
		    TestKotlin.Str
		    TestKotlin.compainFun()
		}
		
		//----------------在java中调用
		public class TestJava {
		    public static void main(String[] args) {
		        TestKotlin.Companion.compainFun();
		        TestKotlin.Companion.getStr();
		    }

可以看到上面java调用kotlin的代码并不像我们java调用静态对象一样（中间多了层Companion）。
上面的java调用Kotlin可以在伴生对象中增加对应的注解：

		函数上加 @JvmStatic，
		变量上加@JvmField
		实现类似调用java静态成员/方法

		class TestKotlin {
		    companion object {
		      @JvmStatic
		        fun compainFun(): String {
		            return "伴生对象方法"
		        }
		        @JvmField
		        var Str = "伴生对象成员"
		    }
		}
		
		//----------------在java中调用
		public class TestJava {
		    public static void main(String[] args) {
		        TestKotlin.compainFun();
		        String str = TestKotlin.Str;
		    }
		}


#### java怎么调用kotlin的包级别对象呢？？？
答案：可以使用@file:JvmName(自己自定义的类名)

		@file:JvmName("TestKotlin1")
		package com.demo.dan.imoc_voice
		
		val Str = "包级成员"
		fun packFun(): String {
		    return "包级函数"
		}
		复制代码java调用：
		public class TestJava {
		    public static void main(String[] args) {
		        TestKotlin1.getStr();
		        TestKotlin1.packFun();
		    }
		}

### @Parcelize

Android中的对象需要类继承Parcelable接口来实现序列化，

传统的标准方法。让Person类继承Parcelable接口并按照Android Studio的要求实现相关API

	data class Person(val name: String, val age: Int, val email: String, val phone: Long) : Parcelable {
	    constructor(parcel: Parcel) : this(
	        parcel.readString(),
	        parcel.readInt(),
	        parcel.readString(),
	        parcel.readLong())
	
	    override fun writeToParcel(parcel: Parcel, flags: Int) {
	        parcel.writeString(name)
	        parcel.writeInt(age)
	        parcel.writeString(email)
	        parcel.writeLong(phone)
	    }
	
	    override fun describeContents(): Int {
	        return 0
	    }
	
	    companion object CREATOR : Parcelable.Creator<Person> {
	        override fun createFromParcel(parcel: Parcel): Person {
	            return Person(parcel)
	        }
	
	        override fun newArray(size: Int): Array<Person?> {
	            return arrayOfNulls(size)
	        }
	    }
	}
	
build.gradle添加插件
	
	apply plugin: 'kotlin-android-extensions'
		
build.gradle下android配置添加

	androidExtensions {
	    experimental = true
	}

使用@Parcelize之后

	@Parcelize
	data class PersonParcelize(val name: String, 
	                           val age: Int, 
	                           val email: String, 
	                           val phone: Long) : Parcelable
	                           
### View.OnClickListener
Kotlin对Lambda的完美支持，让我们写单函数时有了极速的提升。

	在Java中，对View添加一个点击事件时是这样写的：
	view.setOnClickListener(new View.OnClickListener() {
	    @Override
	    public void onClick(View v) {
	        v.setVisibility(View.VISIBLE);
	        ...
	    }
	});
	
	而在Kotlin中我们使用它时，可以这样：
	view.setOnClickListener(object :View.OnClickListener{
	    override fun onClick(v: View?) {
	        v.visibility = View.VISIBLE    
	        ...
	    }
	})

根据lambda表达式规则:

- Lambda表达一般使用“{ }”包围。
- 它的参数（如果有的话）在“->”前定义，参数类型可能是省略的。
- 函数体跟在“->”后面。

我们可把上面的代码简化为：

	view.setOnClickListener({ v ->
	    v.visibility = View.VISIBLE    
	    ...
	})
根据如果参数为函数并且是最后一个参数，可以将参数移到括号外面。

	view.setOnClickListener() { v ->
	    v.visibility = View.VISIBLE    
	    ...
	}
根据移动到括号外面之后如果括号内没有参数，括号可以省略不写。你见到的是这个。

	view.setOnClickListener { v ->
	    v.visibility = View.VISIBLE    
	    ...
	}
根据若函数参数对应的函数只有一个参数，在使用时，可以省略参数定义，直接使用“it”代替参数，你最终看到的是这个。

	view.setOnClickListener {
	    it.visibility = View.VISIBLE
	}

