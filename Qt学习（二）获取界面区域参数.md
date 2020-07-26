---
title: Qt学习（二）获取界面区域参数
date: 2020-01-17 18:15:58
tags:
- Qt
- C++
---

在完成项目的时候要获取屏幕区域大小的参数，发现搜索到的方法在官方文档中并不推荐，琢磨一会后总结对比了下这两种方法。

<!----more---->

# 一、部分名词解释

* 应用区矩形：指的是整个应用程序的矩形框
* 客户区矩形：指的是应用区矩形除去标题栏的矩形框
* 设备屏幕矩形：指的是设备屏幕的分辨率
* 可用桌面矩形：指的是设备屏幕除去底边系统菜单栏的矩形框

![](https://kylinnnnn.github.io/img/Qt01.png)

# 二、获取设备屏幕大小参数

这里根据我自己查阅的资料提供新老两种方法。

### 1、老方法

首先获取桌面对象，再获取桌面的矩形区域对象。

```c++
QDesktopWidget *pDesktop = QApplication::desktop();
QRect deskRect = QApplication::desktop()->availableGeometry();
qDebug() << deskRect.width();
qDebug() << deskRect.height();
```

最后经由矩形框对象`deskRect`即可访问`height`和`width`等参数。

但是此种方法在目前`Qt5`的环境中并不被推荐，原因的话，Qt官方文档中只是说过时所以不推荐，更深层的原因暂不清楚。个人猜测可能是当存在分屏时`pDesktop`指向的屏幕可能不明确的问题。

![](https://kylinnnnn.github.io/img/Qt02.png)

### 2、新方法

新方法也即目前Qt官方文档所推荐的用法。

首先获取存有所有可用屏幕的列表，再从列表中确定要获取参数的屏幕，继而访问参数。

```c++
QList<QScreen*> pList;
pList = QGuiApplication::screens();
QScreen *b;
b = pList.first();
qDebug() << b->availableGeometry().width();
qDebug() << b->availableGeometry().height();
```

列表部分还不熟，用起来比较僵硬，在从列表中确定屏幕那感觉应该是有更直接方便的方法，而不需要像我这样用得这么糙。

![](https://kylinnnnn.github.io/img/Qt02.png)

对比新老方法我们可以看出，两种方法得区别就在于后者多了一步去确定要访问得是哪一个屏幕。这也是我上面推测官方文档中不推荐老方法的原因之一。

