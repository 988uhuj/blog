title: Android开发工具
date: 2015-09-10 17:31:23
tags: 
- Android
- Tool
---
##[Stetho](https://github.com/facebook/stetho)
![Stetho](http://facebook.github.io/stetho/static/images/inspector-discovery.png)
####功能
* 网络抓包
* 数据库操作
* 视图检查
* dumpapp

<!--more-->

####使用
应用配置
```java
public class MyApplication extends Application {
  public void onCreate() {
    super.onCreate();
    Stetho.initialize(
      Stetho.newInitializerBuilder(this)
        .enableDumpapp(
            Stetho.defaultDumperPluginsProvider(this))
        .enableWebKitInspector(
            Stetho.defaultInspectorModulesProvider(this))
        .build());
  }
}
```
网络抓包
```java
OkHttpClient client = new OkHttpClient();
client.networkInterceptors().add(new StethoInterceptor());
```

Chrome浏览器打开 chrome://inspect/#devices

####运行结果
![](http://facebook.github.io/stetho/static/images/inspector-network.png)



##[LeakCanary](https://github.com/square/leakcanary)
![LeakCanary](https://corner.squareup.com/images/leakcanary/leaktrace.png)


检测内存泄露
不需要使用MAT或者YourKit之类的工具分析内存
弱引用->gc->检测泄露
[简单原理](https://github.com/GavinCT/SimpleLeakCanary/)


##[hugo](https://github.com/JakeWharton/hugo)
查看函数运行时间以及入参出参

####使用
```java
buildscript {
  repositories {
    mavenCentral()
  }

  dependencies {
    classpath 'com.jakewharton.hugo:hugo-plugin:1.2.1'
  }
}
apply plugin: 'com.jakewharton.hugo'
```

```java
@DebugLog
public String getName(String first, String last) {
  SystemClock.sleep(15); // Don't ever really do this!
  return first + " " + last;
}
```
####结果
```java
V/Example: ⇢ getName(first="Jake", last="Wharton")
V/Example: ⇠ getName [16ms] = "Jake Wharton"
```


##[dbinspector](https://github.com/infinum/android_dbinspector)
在未root手机上查看本地数据库
![](https://camo.githubusercontent.com/5a2f9fe0725a29ff92f941daa814a65f2bdc193c/68747470733a2f2f7261772e6769746875622e636f6d2f696e66696e756d2f616e64726f69645f6462696e73706563746f722f6d61737465722f73637265656e73686f74732e706e67)


####使用
```java
debugCompile 'im.dino:dbinspector:(insert latest version)@aar'
```


##[lint](http://tools.android.com/tips/lint)
代码检查

##[Dexposed](https://github.com/alibaba/dexposed)
AOP, 日志记录，性能统计，安全控制，事务处理，异常处理等。
[使用](http://www.jianshu.com/p/14edcb444c51)
##[augmented-traffic-control](https://github.com/facebook/augmented-traffic-control)
Facebook宣布开源移动网络测试工具ATC，该工具支持利用Wi-Fi网络模拟2G、2.5G、3G以及LTE 4G移动网络环境，让测试工程师们能够快速对智能手机和App在不同国家地区和应用环境下的性能表现进行测试。

##开发者工具
* 显示布局边界
* 过度绘制
* GPU呈现模式分析
* 不保留活动