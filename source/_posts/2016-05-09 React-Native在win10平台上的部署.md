---
title: 杂谈：React-Native在win10平台上的部署
date: 2016-05-09 10:05:05
author: Gossip
tags: ["Coding"]
description: :)吐血部署react-native，各种百度，简直要死
---

**特别注意：牢记你的所有文件的路径不要出现中文，不然会出现未知的问题**
**默认查看本教程的看官们都有过一定开发经验~配置环境变量这些简单的工作就不用教了吧**

### 安装 JDK

这一点就不废话了，一般程序员都应该有 JDK……
之后将 JDK 的 bin 文件夹的路径加入 PATH 环境变量

### 安装 Android SDK 并下载重要组件：

1. 下载 Android SDK
   如果你已经安装过 eclipse 集成版本或者 Android Stutio 就默认已经安装了 SDK
   如果你没装过，需要去下载[android SDK](http://tools.android-studio.org/index.php/sdk)，下载完安装就行了。

2. 配置环境变量
   将 SDK 的 platform-tools 子目录加入 PATH 环境变量
   将 ANDROID_HOME 环境变量设置成的 SDK 的目录，比如我的：
   ![](http://jackie-image.oss-cn-hangzhou.aliyuncs.com/16-5-9/98466475.jpg)

3. 打开 Android SDK 下载重要组件：
   用行命令打开 SDK

   ```bash
   android sdk
   ```

   你需要勾选以下组件：

   - Tools/Android SDK Tools(>=24.3.3)
   - Tools/Android SDK Platform-tools(>=22)
   - Tools/Android SDK Build-tools(==23.0.1)
   - Android 6.0 (API 23)/SDK Platform(>=1)
   - Extras/Android Support Repository(>=29)

   下载之前:如果你没有科学上网工具，可以选择腾讯的[Bugly](http://android-mirror.bugly.qq.com:8080/include/usage.html)来代理下载
   之后就慢慢等待完成即可~

### 下载安装 node.js

去官网下一个安装（4.1 及以上版本），本教程是不会讲的（哼，自己去看[这篇博客](http://www.cnblogs.com/pigtail/archive/2013/01/08/2850486.html)吧

### 安装 react-native 命令行工具

在这之前，推荐你用淘宝的 npm 镜像来加速后面的过程：

```bash
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```

安装 react-native 命令行：

```bash
npm install -g react-native-cli
```

等待几分钟后即可完成

### 下载一个模拟器或者使用 Android Studio 自带的安卓模拟器

推荐用 bluestacks(虽然我觉得这个模拟器界面很 low)，因为使用 Android Studio 自带的 AVD 很麻烦，所以我也没用

### 创建并启动 React-Native

特别提示：这之前你可以先准备一部电影:)，因为过程将很漫长 hhhh

1. 创建一个工作的文件夹，如：MyProject，在该文件下使用 shift+右键，找到**在此处打开命令窗口**或者**Git Bash**并打开，输入以下命令：

   ```bash
   react-native init MyProject
   ```

   千万要耐心等待几~~分钟~~小时，而且还有很大可能失败……

2. 运行你的 package

   ```bash
   react-native start
   ```

   之后会出现
   ![](http://jackie-image.oss-cn-hangzhou.aliyuncs.com/16-5-9/58757400.jpg)
   之后需要保持该窗口，不要关闭
   打开[这个链接](http://localhost:8081/index.android.bundle?platform=android)如果能看到一堆 js 代码，说明已经启动成功（第一次访问的时候需要等待一会）

3. 打开你的安卓模拟器

4. 另外再打开一个 git 或者 cmd，进入到工程目录，运行

   ```bash
   react-native run-android
   ```

   第一次跑的时候要下载一个 gradle,会下载很长时间，出现很多白点点，你可以接着看上面没看完的那部电影 hhhh

   如果你看到有各种 failed，可以去[React Native 的常见问题](http://www.jianshu.com/p/13adec293492)以下文章找找原因。
   最常见的是找不到 device，需要检查你是否打开了虚拟机或者是否连接了真机，可以使用：

   ```bash
   adb shell
   ```

5. 在你的模拟器上调试
   你的模拟器上应该会出现一个 MyProject 的 app，打开之后一般都会出现红屏，不要慌张，因为还没完成。
   首先找到你的 ip 地址，比如 12.23.34.45
   需要摇晃设备，或者按 menu 键打开菜单
   点击 Dev Settings
   选 Debug server host for device
   输入 你的 IP 地址:8081，如:12.23.34.45:8081
   返回之后进行 Reload JS, 大概就能看到完成后的样子了

###最后
如果你有什么不懂的地方，或者遇到没有解决的问题，欢迎留言咨询~我会及时回复
