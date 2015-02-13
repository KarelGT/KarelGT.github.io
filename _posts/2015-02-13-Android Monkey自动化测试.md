---
layout: post
title: Android Monkey自动化测试
description: "Android系统使用Monkey进行自动化测试"
modified: 2015-02-13
tags: [Android, Monkey, 测试]
---

### 用来开发Android的测试机屏幕坏了…左右两边的触摸都无效了，导致在屏幕两边的按钮都按不动。<br/>
之前记得Android的Monkey可以发送Key事件，于是就去查了下能不能发送点击事件。顺便再了解一下Monkey。

##$ adb shell monkey -p your.package.name -v 500
这个是最基础的命令，作用是向指定应用发送500个随机事件。
* -p后面是测试应用的包名。
* -v代表输出内容的详细程度，每增加一个-v，详细程度就提升一个级别，输出的内容也就更多。最多可以有3个-v。
* 500代表随机事件的数量。

##adb shell monkey -p your.package.name1 -p your.package.name2 -p your.package.name3 --ignore-crashes --throttle 500 -v 10000 
更厉害点，用来测试多个应用组合，比如A应用转跳到B应用再转跳到C应用。
* 3个-p对应3个不同的应用包名。
* --ignore-crashes代表测试过程中发生crash，测试不中断，直到事件全部结束。
* --throttle 500代表每次事件间隔500ms，传入顺序必须要在事件数之前。

##adb shell monkey -v -v -v -f [script file] [run script count]
实现模拟触摸事件靠的就是这个啦~通过这个命令去执行脚本中定义的事件。
* -f后面跟要调用的脚本路径，脚本需要存放在Android设备上，不是电脑上哈。
* [run script count]是脚本执行次数

来看下我的测试脚本

{% highlight %}
<?xml version="1.0" encoding="utf-8"?>
Start of Script
type = user
count= 1
speed= 1
start data >>

UserWait(100)
*DispatchPointer*(1,1, *0*, *470*, *750*, 0, 0, 0, 0, 0, 0, 0)
UserWait(100)
*DispatchPointer*(1,1, *1*, *470*, *750*, 0, 0, 0, 0, 0, 0, 0)
{% endhighlight %}

DispatchPointer就是触摸事件，其中第三个参数0表示按下，1表示抬起。<br/>
第四、第五个参数表示触摸事件的x坐标，y坐标。<br/>
这样按下，抬起的一对才算一个完整的点击事件。

如果要发送Key事件的话使用*DispatchPress(KEYCODE_HOME)*，键值可以在Android API文档的[KeyEvent](http://developer.android.com/reference/android/view/KeyEvent.html)找到。

>参考文档：<br/>
* [https://sites.google.com/site/terrylai14/home/android-monkey_test](https://sites.google.com/site/terrylai14/home/android-monkey_test)
* [http://developer.android.com/tools/help/monkey.html](http://developer.android.com/tools/help/monkey.html)
* [http://developer.android.com/reference/android/view/KeyEvent.html](http://developer.android.com/reference/android/view/KeyEvent.html)
