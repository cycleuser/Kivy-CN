Title: Kivy 虚拟机
Date: 2017-03-13
Category: Work
Tags: Python,Kivy


# Kivy中文编程指南：更好用 Android 打包虚拟机


[英文原文地址](http://brizanmedia.com/wp/2016/07/10/the-fool-proof-guide/)

## 译者的废话


开头先给干货，本文提到的[虚拟机镜像的下载地址](https://pan.baidu.com/s/1mhIUz3q)。

从去年开始，我开始了 [Kivy 编程指南中文翻译项目](https://github.com/cycleuser/Kivy-CN)，把 Kivy Programming Guide 里面的全部内容翻译了一遍。大家可以去我的[知乎专栏](https://zhuanlan.zhihu.com/python-kivy)或者我的 [GitHub](https://github.com/cycleuser) 查看详细内容。

Kivy 的多平台支持以及使用 Python 这一新手友好的语言的两大特性都很棒，然而世事难求全，APK 生成和搭建有时候挺繁琐的，尤其对于一些不太熟悉 Linux 的纯新人。所以官方提供了很多文档，我也进行了翻译，包括[KV Android 详细指南](https://zhuanlan.zhihu.com/p/25576296),[打包为 Android 系统可执行文件](https://zhuanlan.zhihu.com/p/25571974)，以及一个[ Kivy 开发团队提供的 Android 打包用的虚拟机镜像](https://zhuanlan.zhihu.com/p/25583908)。国内的大拿 [nkiiiiid](https://github.com/nkiiiiid/) 也提供了一个他的定制版本的[虚拟机镜像](https://github.com/nkiiiiid/kivy-apk-)。更推荐用[nkiiiiid](https://github.com/nkiiiiid/) 提供的这个版本，16.04 用着更舒服。群里面大家也普遍喜欢用这个版本。


然后进有一位@郝好  的朋友提供了重要信息，说[brizanmedia.com](http://brizanmedia.com) 提供的这个 lubuntu 14.04.3 的 32bit 虚拟机镜像用着很不错。

然后作为常年搬运工的我，就顺手下载下来发到百度云，并且翻译一下原文咯。
原文有很多叹号，我个人不喜欢用叹号，不过在本文我还是忠于原文的风格，在措辞和符号上与原文保持一致。

废话完毕，以下是 [brizanmedia.com](http://brizanmedia.com) 的原文翻译。

##使用 Python + Kivy 创建 Android 应用的防呆指南~

Kivy 是一个支持多点触摸的 Python 框架，可以运行于 Windows，OS X，Linux ，iOS 以及 Android 系统。不过在 Android 上运行 Kivy，还挺麻烦的，因为需要先把代码打包成一个应用。下面的指南是让你从 Python 代码打包到 Android 应用的方法，一定能够成功的哦~


Kivy 官方的指南上面，有一个[如何给 Android 系统打包的指南文档](https://kivy.org/docs/guide/packaging-android.html)，（译者也翻译了一份[中文版本的 Android 打包指南](https://zhuanlan.zhihu.com/p/25571974)）。不过这些方法都不能用在 Windows 或者 OS X 上面，另外官方的指南很简略，略过了很多步骤。

本文提供的这个方案很简单，所有事情都在虚拟机里面解决，包括打包的步骤。目前来说，Kivy 团队确实做了开创性的工作，不过还有以下的问题：
1. 他们提供的虚拟机使用的是 Ubuntu 操作系统，这样性能开销比较大，用起来运行速度很慢；
2. 从代码打包生成应用的必备步骤并没有明确给出清晰透彻的指南。

于是本文作者自己创建了一个虚拟机镜像，尽可能提高运行速度，争取做到和本机原生操作接近的体验。

### 初始步骤

第一步就是要下载本文作者提供的 [Linux 虚拟机硬盘镜像](http://brizanmedia.com/wp/wp-content/LXLE14.04.3\(32bit\).vdi.zip)。这个镜像文件挺大的，4G 左右，不过值得你为下载它而付出的等待。（译者注：毕竟能用就不错，而且平心而论这个 Lubuntu 14.04.4 32bit 的虚拟机镜像确实速度比较快，性能开销压力不大。）然后你还需要下载[ Oracle VM Virtualbox](http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html) ，并且安装到你的系统中。关于怎么弄虚拟机这些就不给讲了，都很简单而且网上资料很多。


**下面这个特别重要**：安装完毕之后，运行 VirtualBox，然后创建一个新的虚拟机，使用刚刚下载的这个 vdi 文件作为硬盘。你唯一需要设置的是把操作系统设置为 Linux，版本**必须**设置为 Ubuntu 32bit。如果你需要进一步的指南，可以看[这个视频](https://www.youtube.com/watch?v=1P_l7iVKfgs)。这个视频展示了如何安装 VirtualBox 以及用一个已有的 vdi 文件创建新的虚拟机，你就可以按照这个指南来做。（译者注：国内也有很多类似的视频，推荐自己搜索一下，这里就不搬运原版视频了。）

### 开始构建吧！


打开虚拟机，启动到登录界面，**用户名和密码都是**`osboxes.org`。

![vbox1](http://brizanmedia.com/wp/wp-content/uploads/2016/07/vbox1.jpg)

这里顺路要感谢一下 [osboxes.org](http://www.osboxes.org/)。（译者注：这个网站分享各种已经装好的虚拟机镜像，VirtualBox 和 Vmware 的都有，非常赞，可以下载来当做各种测试环境。）

登陆之后就能看到桌面了：
![vbox2](http://brizanmedia.com/wp/wp-content/uploads/2016/07/vbox2-1024x640.jpg)

本文作者把所有构建 APK 的相关指南都放到了桌面壁纸上面，不过这里还是会用很多屏幕截图来给讲述一遍。


1\. 点击图示的按钮启动终端，（名字通常是 ROXTerm 之类的）。

![vbox3](http://brizanmedia.com/wp/wp-content/uploads/2016/07/vbox3-1024x640.jpg)

2\. 在终端中输入下面的命令，（或者也可以用向上的方向键来查看之前的输入历史，里面能找到，下同）：

```Bash
cd ~/Desktop/kivycode
```


输入之后，按回车键。这样就让你进入到 `kivycode` 这个目录了。你必须进入这个目录，这样接下来的第 4 步才能成功。

3\.  在终端中输入下面的命令：
```Bash
cp ~/Desktop/buildozer.spec ~Desktop/kivycode
```
输入之后还是按回车键。这回把 buildozer.spec 文件复制到这个 `kivycode` 目录以供使用。当然你也可以在图形界面下来复制粘贴过来。

4\. 在终端中输入下面的命令：
```Bash
buildozer android debug
```

输入之后按回车咯。这一步是真正在创建 APK 文件。如果所有步骤都没问题，你的 APK 文件就会被生成放到桌面上。接下来就把 APK 文件安装到你的 Android 设备上面去试试吧！

![linux4Python4Android](http://brizanmedia.com/wp/wp-content/uploads/2016/07/linux4Python4Android-1024x640.jpg)

以上这样，你就创建了第一个 Android 安装包！

### 接下来开始用你的代码来创建！

你刚刚构建过一个 APK 了，不过这个 APK 并没有怎么充分利用这个虚拟机。而且代码还不是你自己的。所以咱们来试试你自己的代码。

首先咱们要让虚拟机能和外界交换文件。本文作者的选择是使用了一个外置硬盘。把外置硬盘插到机器（的宿主系统）中，然后把要变异的内容复制进去。接下来把 USB 设备从宿主机器移除，添加给虚拟机，这个操作反过来也可以，只需要在 VirtualBox 菜单栏中的 `Devices -> USB Devices` 中对选中的设备进行点击即可。


![vbox4](http://brizanmedia.com/wp/wp-content/uploads/2016/07/vbox4-1024x640.jpg)

磁盘就会出现在虚拟机系统中了：

![vbox5](http://brizanmedia.com/wp/wp-content/uploads/2016/07/vbox5-1024x640.jpg)

如果你没有外置磁盘，可以用浏览器来把文件传递到谷歌硬盘之类的云盘，然后虚拟机里面再下载。

![vbox8](http://brizanmedia.com/wp/wp-content/uploads/2016/07/vbox8-1024x640.jpg)


VirtualBox 本来还是有一个共享文件夹功能的，可以让宿主机和虚拟机之间共用一个文件夹，不过本文作者没弄明白怎么搞定这个功能。如果有人能搞定这个请告诉**本文作者**怎么实现，非常感谢。（译者注：^_^，不用留言给中文版这里，是英文原版作者不会实现共享文件夹。）

现在文件能够拷入拷出了，就可以编译你自己的代码了。把你要编译的代码放到桌面上的 `kivycode` 这个目录。只要把这个文件夹里面的所有文件都用你的代码文件替换了，就可以打包你自己的应用了。视频之类的一些东西还需要额外打包设定，你还得使用 Kivy 框架，不过目前来看，Python 2.7 的代码都能打包成功没问题。

![vbox6](http://brizanmedia.com/wp/wp-content/uploads/2016/07/vbox6-1024x640.jpg)

编译你自己代码之前的最后一件事了，就是把 `buildozer.spec` 这个文件从桌面复制到 `kivycode` 文件夹，然后对副本进行必要的编辑。各种选项相关的信息可以参考 [Buildozer 的官方在线文档](https://buildozer.readthedocs.io/en/latest/specifications.html)。

比如你可能要修改加载图像 loading.png 以及图标文件 icon.png 等等，反正就那些了。接下来你就可以把你的 Python 代码构建成 Android 的 APK，然后从虚拟机的桌面找到这个 APK ，安装到你的 Android 设备上面来试试了。

好好玩吧！
