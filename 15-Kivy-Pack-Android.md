Title: Kivy Pack Android
Date: 2017-03-06
Category: Kivy
Tags: Python,Kivy

# Kivy中文编程指南：打包为 Android 系统可执行文件

[英文原文](https://kivy.org/docs/guide/packaging-android.html)


你可以通过 [python-for-android](https://github.com/kivy/python-for-android) 这个项目来打包一个 Android 应用。本页面详细讲解如何下载和打包，可以在你自己的机器上直接进行（参考[此页面](https://kivy.org/docs/guide/packaging-android.html#packaging-your-application-into-apk)），或者使用预先构建好的[Kivy Android 虚拟机](https://kivy.org/docs/guide/packaging-android-vm.html#kivy-android-vm)，或者使用[Buildozer](https://kivy.org/docs/guide/packaging-android.html#buildozer) 来自动化完成整个过程。还可以参考 [针对 Kivy Launcher 进行打包](https://kivy.org/docs/guide/packaging-android.html#packaging-your-application-for-kivy-launcher) 这样就不用编译就能运行 Kivy 应用。

对新手，Kivy 官方推荐使用 [Buildozer](https://kivy.org/docs/guide/packaging-android.html#buildozer) ，这是制作完整 APK 的最简单的途径。或者也可以使用 [Kivy Launcher](https://kivy.org/docs/guide/packaging-android.html#packaging-your-application-for-kivy-launcher) 这个应用来运行你的 Kivy 应用，而不用编译了。

Kivy 应用可以[发布到 Android 应用市场](https://kivy.org/docs/guide/packaging-android.html#release-on-the-market)，比如谷歌的 Play 市场等等，只需要额外几步来创建一个完整签名的 APK 就可以了。

Kivy 项目包含了一系列读取 Android API 的工具，可以实现震动、传感器读取、信息发送等等功能。相关的详细信息都可以参考[ Kivy 的 Android 专题页面](https://kivy.org/docs/guide/android.html)。

#### 特别注意

Android 平台目前已经支持 Python 3 了，不过还处于实验阶段。



## Buildozer[¶](https://kivy.org/docs/guide/packaging-android.html#buildozer "Permalink to this headline")

Buildozer 是一个将整个构建过程自动化的工具。它会下载和设置 python-for-android 需要的所有依赖项目，包括 Android 的 SDK 和 NDK，然后会构建 APK ，这个 APK 可以自动推送到设备上。

目前 Buildozer 只能用在 Linux 上面，而且还不是正式版，处于测试阶段，发布的是 alpha 版本，不过目前用起来还不错，能显著简化 APK 构建的过程。

可以到[ Buildozer 的项目页面](https://github.com/kivy/buildozer) 下载获取 Buildozer。

```Bash
git clone https://github.com/kivy/buildozer.git
cd buildozer
sudo python2.7 setup.py install
```

上面的命令就会把 Buildozer 安装到你的操作系统中。接下来就是到你的项目目录然后运行如下命令：

```Bash
buildozer init
```

这会在你的目录下创建一个名为 buildozer.spec 的文件，这个文件是控制项目构建选项的。估计你需要编辑修改一下这个文件，比如设置你应用的名字等等。在这里可以设置传递给 python-for-android 的全部或者大部分参数。

安装[ Buildozer 的依赖项目](https://buildozer.readthedocs.io/en/latest/installation.html#targeting-android)。

最后一步了，连接上你的 Android 设备然后运行下面的命令：

```Bash
buildozer android debug deploy run
```

这样就可以创建、推送 APK 到你的设备上，然后就可以自动运行了。

Buildozer 有很多可以控制的选项和工具，对你都会游泳，上面这些步骤只是创建 APK 的最简单的方法。可以到[ Buildozer 的官方文档页面](http://buildozer.readthedocs.org/en/latest/)查看完整说明。也可以看看[ Buildozer 项目页面](https://github.com/kivy/buildozer)的 README 文件。

## 通过 python-for-android 打包[¶](https://kivy.org/docs/guide/packaging-android.html#packaging-with-python-for-android "Permalink to this headline")

你还可以直接用 python-for-android 来打包应用，这样你可以有更多控制选项，但需要手动下载和设置 Android 工具链。

参考[python-for-android 官方文档](https://python-for-android.readthedocs.io/en/latest/quickstart/) 查看全部细节。


## 针对 Kivy Launcher 打包[¶](https://kivy.org/docs/guide/packaging-android.html#packaging-your-application-for-the-kivy-launcher "Permalink to this headline")

[Kivy launcher](https://play.google.com/store/apps/details?id=org.kivy.pygame&hl=en) 是一个 Android 应用，可以运行存储到 SD 卡里面的 Kivy 样例。可以用下面的方法来安装 Kivy launcher：

1. 前往[谷歌 Play 市场中的 Kivy Launcher 页面](https://market.android.com/details?id=org.kivy.pygame)；
2. 点击安装；
3. 选择你的设备，然后就搞定了。

如果你的设备无法访问谷歌 Play 市场（我大天朝么。。。），可以从[ Kivy 官网的下载页面](http://kivy.org/#download) 手动下载安装 APK 文件。

安装了 Kivy launcher 之后，就可以把你的 Kivy 应用放到外置存储的 Kivy 文件夹中，（通常是在 `/sdcard` 目录下），例如：

```Bash
/sdcard/kivy/yourapplication>
```

`<yourapplication>`你的应用应该是一个**文件夹**，包含以下文件：

```Bash
# 程序主文件：
main.py
# Kivy 需要的关于你应用的信息：
android.txt
```
以下信息是 android.txt 这个文件**必须包含**的：

```Bash
title=<Application Title> #这是应用的标题
	author=<Your Name>  #这是作者签名
orientation=<portrait|landscape> #设定屏幕方向为水平或竖直
```

上面这些都是非常基础的设置。如果你要用上面的工具来构建自己的 APK，还得调整很多其他的设定。


### 安装样例项目[¶](https://kivy.org/docs/guide/packaging-android.html#installation-of-examples "Permalink to this headline")

Kivy 自带了很多歌样例，可以先用这些来试一试 Kivy Launcher。可以用如下的方法来运行：


1. 下载 [`Kivy demos for Android`](https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/kivy/kivydemo-for-android.zip)
2. 解压缩然后进入目录`kivydemo-for-android`
3. 把目录内所有子目录等内容复制到 `/sdcard/kivy` 这个目录
4. 运行 Kivy Launcher 然后从样例中选择一个来试试，Pictures, Showcase, Touchtracer, Cymunk 等等都可以。


## 发布到应用市场[¶](https://kivy.org/docs/guide/packaging-android.html#release-on-the-market "Permalink to this headline")

如果你用 Buildozer 或者 python-for-android 构建了 APK 文件，就可以创建一个 release 版本来发布到谷歌 Play 市场或者其他 Android 应用市场。

要想发布，就必须在运行 Buildozer 的时候添加上 `release` 参数，例如 `buildozer android release`，如果用了 python-for-android 就在 build.py 进行编译的时候加上`--release`。

这样就能在 `bin` 目录里面创建一个正式发布的 release 版的 APK 文件，需要你做好签名和 zipalign 压缩优化（译者注：zipalign，优化apk应用程序的工具，使包内未压缩的数据能够有序的排列）。

上面这些操作的详细过程可以参考 [Android 官方文档](https://developer.android.com/studio/publish/app-signing.html#signing-manually) ，所有用到的工具都在 Android SDK 里面了。


## 设定 Android [¶](https://kivy.org/docs/guide/packaging-android.html#targeting-android "Permalink to this headline")

Kivy 的设计定位是提供跨平台的相同操作体验，因此也有一些清晰地设计特点。Kivy 包含了自己独有的一套控件，在默认情况下，用所有需要的依赖包和链接库来构建 APK 文件。

也可以指定特定的 Android 功能，可以直接进行，也可以用（某些）跨平台的方式来实现。更多细节可以参考[ Kivy 的 Android 专题页面](https://kivy.org/docs/guide/android.html) 中关于使用 Android API 的部分。
