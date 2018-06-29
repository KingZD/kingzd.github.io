---
title: Xposed 入坑篇
tags: Xposed 
categories:
    - Xposed
blogexcerpt: 近些日子，受到高人指点，突然想入坑学习学习 Xposed 于是乎在网上搜集一些入门例子 其中遇到了些小问题 记录下来 可能帮助同样遇到此问题的你
---

近些日子，受到高人指点，突然想入坑学习学习 Xposed 于是乎在网上搜集一些入门例子 其中遇到了些小问题 记录下来 可能帮助同样遇到此问题的你
https://www.52pojie.cn/thread-690818-1-1.html
http://www.freebuf.com/articles/web/156944.html
https://www.jianshu.com/p/8fbf9e88eb54 （[@WrBug](https://www.jianshu.com/u/348b77e56202)）
https://www.52pojie.cn/thread-533120-1-1.html
深受启发，于是乎想自己按照教程来写写。同时感谢以上几位大佬的教程

首先我安装了比较新的[XposedInstaller_3.1.5.apk](https://share.weiyun.com/57vAGwz)
安装成功后如下图
![device-2018-04-12-101001.png](https://upload-images.jianshu.io/upload_images/5223850-f13695c22e04c29e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)

接下来开始敲代码了 美滋滋(皮一下很开心) 上一张整个工程的图

![QQ截图20180412102021.png](https://upload-images.jianshu.io/upload_images/5223850-16221197238999ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以下是Test和Tutorial的代码
```
package com.zed.xposed.demo;

import de.robv.android.xposed.IXposedHookLoadPackage;
import de.robv.android.xposed.XposedBridge;
import de.robv.android.xposed.callbacks.XC_LoadPackage;

/**
 * Created by zed on 2018/4/11.
 */

public class Test implements IXposedHookLoadPackage {
    @Override
    public void handleLoadPackage(XC_LoadPackage.LoadPackageParam lpparam) throws Throwable {
        XposedBridge.log("Loaded Test app: " + lpparam.packageName);
    }
}

```
因为我在学习kotlin 所以后面应该会有两个版本代码
```
package com.zed.xposed.demo

/**
 * Created by zed on 2018/4/11.
 */
import de.robv.android.xposed.IXposedHookLoadPackage;
import de.robv.android.xposed.XposedBridge;
import de.robv.android.xposed.callbacks.XC_LoadPackage.LoadPackageParam;

open class Tutorial : IXposedHookLoadPackage {
    override fun handleLoadPackage(lpparam: LoadPackageParam?) {
        XposedBridge.log("Loaded app: " + lpparam?.packageName);
    }

}
```
新建xposed_init的文本文档 里面只用声明下你的两个类文件 包名+类名
com.zed.xposed.demo.Tutorial
com.zed.xposed.demo.Test

最后是清单文件
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.zed.xposed.demo">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <meta-data
            android:name="xposedmodule"
            android:value="true" />
        <meta-data
            android:name="xposeddescription"
            android:value="Hook log test" />
        <meta-data
            android:name="xposedminversion"
            android:value="82" />


        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

build.gradle配置  
```
dependencies {
    compile 'de.robv.android.xposed:api:82'
    compile 'de.robv.android.xposed:api:82:sources'
}
```
一切都在美好的方向了 build一下安装 重启后 打开了之后

![QQ截图20180412102953.png](https://upload-images.jianshu.io/upload_images/5223850-05839017c53a4de0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)

？？？ What Fuck (me is 逗比)
# Android Studio (Gradle-based)

The Xposed Framework API is published on Bintray/jCenter: [https://bintray.com/rovo89/de.robv.android.xposed/api](https://bintray.com/rovo89/de.robv.android.xposed/api)
That makes it easy to reference it by simply adding a Gradle dependency in your `app/build.gradle`:

```source-groovy-gradle
repositories {
    jcenter();
}

dependencies {
    provided 'de.robv.android.xposed:api:82'
}
```

**It is very important that you use `provided` instead of `compile`!** The latter would include the API classes in your APK, which can cause issues especially on Android 4.x. Using `provided` just makes the API classes usable from your module, but there will only be references to them in the APK. The actual implementation will be *provided* when the user installs the Xposed Framework.

In most cases, the `repositories` block already exists, and there are usually some dependencies already. In that case, you just need to add the `provided` line to the existing `dependencies` block.

There is also documentation available for the API (see below). Unfortunately, I didn't find any good way to enable automatic download of the API sources, except using *both* of these lines:

```source-groovy-gradle
provided 'de.robv.android.xposed:api:82'
provided 'de.robv.android.xposed:api:82:sources'
```

The way Gradle caches the files, Android Studio will set up the second jar as source for the first one automatically. Better recommendations are welcome!

Please make sure to **disable Instant Run** (`File -> Settings -> Build, Execution, Deployment -> Instant Run`), otherwise your classes aren't included directly in the APK, but loaded via a stub application which Xposed can't handle.


赶紧把手捂住脸 偷偷修改下app/build.gradle下的依赖引用
还有个错误情况就是 你只引用了
```
compile 'de.robv.android.xposed:api:82'
```

也会出现一个错误 日志内容如下
```
04-11 22:36:23.839 I/Xposed  ( 3386): -----------------
04-11 22:36:23.839 I/Xposed  ( 3386): Starting Xposed version 89, compiled for SDK 21
04-11 22:36:23.839 I/Xposed  ( 3386): Device: Custom Phone - 5.0.0 - API 21 - 768x1280 (unknown), Android version 5.0 (SDK 21)
04-11 22:36:23.839 I/Xposed  ( 3386): ROM: vbox86p-userdebug 5.0 LRX21M 17 test-keys
04-11 22:36:23.839 I/Xposed  ( 3386): Build fingerprint: generic/vbox86p/vbox86p:5.0/LRX21M/17:userdebug/test-keys
04-11 22:36:23.839 I/Xposed  ( 3386): Platform: x86, 32-bit binary, system server: yes
04-11 22:36:23.839 I/Xposed  ( 3386): SELinux enabled: no, enforcing: no
04-11 22:36:26.060 I/Xposed  ( 3386): -----------------
04-11 22:36:26.060 I/Xposed  ( 3386): Added Xposed (/system/framework/XposedBridge.jar) to CLASSPATH
04-11 22:36:26.084 I/Xposed  ( 3386): Detected ART runtime
04-11 22:36:26.089 I/Xposed  ( 3386): Found Xposed class 'de/robv/android/xposed/XposedBridge', now initializing
04-11 22:36:26.158 I/Xposed  ( 3386): Loading modules from /data/app/com.zed.xposed.demo-1/base.apk
04-11 22:36:26.158 E/Xposed  ( 3386):   Cannot load module:
04-11 22:36:26.158 E/Xposed  ( 3386):   The Xposed API classes are compiled into the module's APK.
04-11 22:36:26.158 E/Xposed  ( 3386):   This may cause strange issues and must be fixed by the module developer.
04-11 22:36:26.158 E/Xposed  ( 3386):   For details, see: http://api.xposed.info/using.html

```

说了那么多废话了 总结一下就是 如果你没有正常出现以下界面，请多多注意是不是你的依赖jar没有配置好 不要急
![QQ截图20180412104010.png](https://upload-images.jianshu.io/upload_images/5223850-a1d1560fd1eef8dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)


以上是一个入门级 新手的入坑记录