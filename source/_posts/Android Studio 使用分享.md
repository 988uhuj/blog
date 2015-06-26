title: "Android Studio 使用分享"
date: 2015-06-26 12:05:50
tags: Android
---
新建，导入的时候gradle需初始化，会下载当前项目所需的gradle版本，迟迟下载不下来可通过离线安装来完成。
1.在用户目录下，进入如下目录，目录中会列出当成电脑中所拥有的所有gradle版本。
> ~\\.gradle\wrapper\dists

2.如果是要安装gradle2.2.1的版本，则进入2.2.1的目录，将在官网下载完的gradle的zip包覆盖到目录下并解压缩。同时将part的后缀名修改为ok即可。

<!-- more -->

---

新导入的git，svn工程无法显示版本管理，可通过下面方式打开。
> VCS->Enabled Version Control Integration

---

Android中.so 文件在Studio中存放的目录默认为
> src/main/jinLibs

相对应的还有assets目录
> src/main/assets

---
在lib目录中添加jar需手动sync，才可将jar包添加进依赖

---
加载本地aar，aar可放在任一目录，这里将其放在libs目录底下
``` java
repositories {
	mavenCentral()
	flatDir {
		dirs 'libs'
	}
}
    
compile project(name':xxlibrary',ext:'aar')
```

---
gradle中使用SNAPSHOT的包时，gradle会默认缓存该包24小时，如果不需要缓存，则可以通过如下配置来改变缓存时间。http://stackoverflow.com/questions/13565082/how-can-i-force-gradle-to-redownload-dependencies
``` java
configurations.all {
    resolutionStrategy {
        cacheChangingModulesFor 0, 'seconds'
    }
}

dependencies {
    compile group: "group", name: "projectA", version: "1.1-SNAPSHOT", changing: true
}
```

---
在library中最小版本号与当前最小版本不一致时，可通过overrideLibrary忽略library的最小版本来合并清单
[更多Manifest合并](http://tools.android.com/tech-docs/new-build-system/user-guide/manifest-merger#TOC-tools:overrideLibrary-marker)
```
<uses-sdk tools:overrideLibrary="com.xxx.mylibrary" />
```

---
依赖传递, aar默认不引入arr的依赖，但会自动引入jar的依赖
``` java
compile ('com.github.chenupt.android:dragtoplayout:1.2.1@aar'){
    transitive=true;
}
```

---
签名
``` java
    signingConfigs {
        release {
            // read from signing.properties
			storeFile file("temp.file")
            storePassword ""
            keyAlias ""
            keyPassword ""
        }
    }
```

---

偶尔studio会出现如下报错
> no debuggable applications

可通过下面方法解决
> tools->android->Enable ADB Integration


