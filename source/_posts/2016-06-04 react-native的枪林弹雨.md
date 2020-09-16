---
title: 杂谈：react-native的枪林弹雨
date: 2016-06-04 20:19:53
author: Gossip
tags: ["Coding"]
description: react-native开发中碰到的问题及其解决方法
---

## react-native 的枪林弹雨

### 常见问题系列

1. Couldn't get the native call queue:bridge configuration isn't available.This shouldn't be possible.Congratulations.
   ![](http://i.stack.imgur.com/bj86C.png)
   我的解决方法：删除工作目录下的 node_modules 文件夹，之后备份你的 js 文件，然后在行命令中重新进行：

   ```bash
   cd ..
   react-native init
   ```

2. Unable to upload some APKs
   运行
   ```bash
   react-native run-android
   ```
   可能出现如下错误：
   ```bash
   Execution failed for task ':app:installDebug'.
   com.android.builder.testing.api.DeviceException: com.android.ddmlib.InstallException: Unable to upload some APKs
   ```
   此时需要确认：
   1. 如果你是在真机上运行，你的设备要确保连接正确（打开 USB 调试），可以用 adb services 来查看是否有该设备
   2. 如果你是用模拟器，保证它已经启动了（也可以用 adb services 来查看）
   3. 可能需要更改你的工作目录/android/build.gradle 中的
   ```bash
   com.android.tools.build:gradle:1.3.1
   ```
   改成
   ```bash
   com.android.tools.build:gradle:1.2.3
   ```
   然后再跑
   ```bash
   react-native run-android
   ```
   参考：[Unable to upload some APKs](http://www.hacksparrow.com/react-native-android-unable-to-upload-some-apks.html)
3.
