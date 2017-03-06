Title: Kivy VM
Date: 2017-03-6
Category: Kivy
Tags: Python,Kivy


# Kivy中文编程指南：Kivy 虚拟机


[英文原文](https://kivy.org/docs/guide/packaging-android-vm.html)

## 简介[¶](https://kivy.org/docs/guide/packaging-android-vm.html#introduction "Permalink to this headline")

目前，Kivy 的 Android 应用程序构建只能在配置有 python-for-android、 Android SDK 和 NDK 的 Linux 环境进行。这对于 Windows 或者 OS X 的用户来说，搭建起来就有点麻烦，所以 Kivy 官方提供了一个完全配置好的 [VirtualBox](http://www.virtualbox.org/) 虚拟机磁盘镜像，用来减轻自己搭建的哀伤与痛苦。

如果你对于虚拟机不太了解，建议去阅读以下[维基百科上面的虚拟机页面](http://en.wikipedia.org/wiki/Virtualization)。

## 上手[¶](https://kivy.org/docs/guide/packaging-android-vm.html#getting-started "Permalink to this headline")

1. 首先是到[ Kivy 下载页面](http://kivy.org/#download) 找到 **Virtual Machine** 这一部分。下载的文件超过 2GB ，解压缩之后是大概 6GB。解压缩文件之后别忘掉 vdi 虚拟磁盘的位置。
2. 根据你机器的操作系统版本，去[ VirtualBox 下载页面](https://www.virtualbox.org/wiki/Downloads) 下载对应的安装包，然后安装。
3. 启动 VirtualBox，点击新建按钮。然后选择操作系统为 “linux” ，版本设置为 “Ubuntu 64-bit”。
4. 在 “Hard drive” 硬盘这个选项，选择“Use an existing virtual hard drive file”，即使用已有的虚拟硬盘文件。这时候找到上面你下载并解压出来的那个 vdi 文件，选中使用它。
5. 到虚拟机设置的页面。在“Display -> Video” 显示器->显卡，这一部分，吧显存增加到至少 32MB 以上。启用 3D 加速来提高用户体验。
6. 启动虚拟机，然后看看桌面上的 readme 文件，根据指示操作就行了。



## 构建 APK[¶](https://kivy.org/docs/guide/packaging-android-vm.html#building-the-apk "Permalink to this headline")


虚拟机家在之后，就根据 python-for-android 打包指南里面的讲解来构建 APK 文件里了：[英文原版](https://kivy.org/docs/guide/packaging-android.html#packaging-your-application-into-apk)，[中文翻译版本](http://blog.cycleuser.org/kivy-pack-android.html)或者[知乎专栏的镜像](https://zhuanlan.zhihu.com/p/25571974)。也根本不用使用 git clone 下载什么的了，因为虚拟机里面的 python-for-android 已经安装配置好了，就在虚拟系统的 home 目录里面了。


## 提示建议[¶](https://kivy.org/docs/guide/packaging-android-vm.html#hints-and-tips "Permalink to this headline")

1. 共享文件夹
	通常情况下，你的开发环境和工具链都在宿主机中，而 APK 的构建在客户机里面。好在 VirtualBox 提供了共享文件夹的功能，允许你的客户机直接读取宿主机中的某个文件夹。
	可以选中‘Permanent’（永久挂载）和 ‘Auto-mount’（自动挂载）这两个选项，这样把构建好的 APK 文件复制到宿主机就更方便了。写一个小脚本就可以很简单地实现自动复制或者移动这个步骤。
2. 复制粘贴
	默认情况下，宿主机和客户机的剪贴板是不能共享的。可以在“Settings -> General -> Advanced”（设置->通用->高级）中启用 “bi-directional”（双向复制粘贴）的选项。
3. 虚拟机快照
	如果你正在用 Kivy 开发环境的分支，同步最新版本有时候可能会出问题（Kivy 开发者尽量在避免这种情况）。所以可以在更新之前建立一个虚拟机快照来避免这类问题。这能让你很方便恢复到之前能用的状态。
4. 补充内存
如果虚拟机分配的内存不够，可能会因为一些很神奇的错误导致编译失败，比如：
```Bash
arm-linux-androideabi-gcc: Internal error: Killed (program cc1)
```
如果出现了上面这种情况，检查一下 Kivy 虚拟机的剩余内存，如果内存不足，就在虚拟机设置里面多增加一些吧。




