Title: Kivy Pack iOS
Date: 2017-03-07
Category: Kivy
Tags: Python,Kivy

# Kivy中文编程指南：打包为 iOS 系统可执行文件

[英文原文](https://kivy.org/docs/guide/packaging-ios.html)


#### 特别注意

目前还只能用 Python 2.7 来针对 iOS 平台打包应用程序。 Python 3.3 以上的支持还在开发中。


`The overall process for creating a package for IOS can be explained in 4 steps:`

总体上创建一个 iOS 应用程序需要四步：
（译者注：对，你没有看错，官方文档就是写的 4 steps，所以我特地留下了上面这行原文，可见估计 Kivy 的开发者们也没太注意这点小错误吧。。。）

1. 针对 iOS 编译 Python 和 需要的模块；
2. 创建一个 Xcode 项目，链接你的源代码；
3. 进行定制修改。



## 预先准备[¶](https://kivy.org/docs/guide/packaging-ios.html#prerequisites "Permalink to this headline")

首先要安装一些依赖包，比如 cython，autotools 等等。Kivy 的开发者推荐你使用 [Homebrew](http://mxcl.github.com/homebrew/) 来安装这些依赖包：

```Bash
brew install autoconf automake libtool pkg-config
brew link libtool
sudo easy_install pip
sudo pip install cython==0.23
```

更多细节参考 [IOS Prerequisites](https://kivy.org/docs/guide/packaging-ios-prerequisites.html#packaging-ios-prerequisites)。一定要在下一步开始之前保证依赖关系都满足了。

## 编译发布版[¶](https://kivy.org/docs/guide/packaging-ios.html#compile-the-distribution "Permalink to this headline")

Open a terminal, and type:

打开终端，输入下面的命令：

```Bash
git clone git://github.com/kivy/kivy-ios
cd kivy-ios
./toolchain.py build kivy
```

Python 发型版中大部分内容都会被打包到 python27.zip 这个文件中。如果遇到了问题，可以参考[谷歌论坛用户组](https://groups.google.com/forum/#!forum/kivy-users) 或者 [kivy-ios 项目页面](https://github.com/kivy/kivy-ios)。



## 创建一个 Xcode 项目[¶](https://kivy.org/docs/guide/packaging-ios.html#create-an-xcode-project "Permalink to this headline")

在进行下一步之前，要保证你的程序运行起点是一个名为 main.py 的文件。

Kivy 官方提供了一个脚本，可以创建一个初始的 Xcode 项目。在下面的代码样例中，把 Touchtracer 这个换成你的项目名字。名字一定不能有空格或者其他非法字符。（译者注：吐槽一下，那什么算是非法字符倒是说说啊。）


```Bash
./toolchain.py create <title> <app_directory>
./toolchain.py create Touchtracer ~/code/kivy/examples/demo/touchtracer
```


#### 特别注意
上面这一步中，应用程序的路径一定要用完整路径。

接下来会有一个名字为` <title>`-ios 的目录被创建，里面就是 Xcode 项目了。可以打开这个项目：

```Bash
open touchtracer-ios/touchtracer.xcodeproj
```

然后点击 Play 运行，就可以了。

#### 特别注意

每次点击 Play 的时候， 你的应用目录都会同步到` <title>`-ios/YourApp 这个目录。不要直接对这个目录进行修改。


## 更新 Xcode 项目project[¶](https://kivy.org/docs/guide/packaging-ios.html#updating-an-xcode-project "Permalink to this headline")

举个例子，加入你要在你的项目中添加 numpy，但在之前创建这个 Xcode 项目的时候没有编译进去。那首先就构建 numpy ：


```Bash
./toolchain.py build numpy
```

然后更新一下你的 Xcode 项目：
```Bash
./toolchain.py update touchtracer-ios
```

这样所有相关的链接库、框架就都被添加到你的 Xcode 项目里去了。


## 定制修改[¶](https://kivy.org/docs/guide/packaging-ios.html#customize "Permalink to this headline")

有很多方法能对你的应用进行修改和设置。可以参考 [kivy-ios](http://www.github.com/kivy/kivy-ios) 的文档来查看更详细的信息。


## 已知的问题[¶](https://kivy.org/docs/guide/packaging-ios.html#known-issues "Permalink to this headline")

目前关于 iOS 打包的所有已知问题都可以在 [Kivy-iOS 的 GitHub issues 页面](https://github.com/kivy/kivy-ios/issues) 查看到。如果你遇到的问题不在其中，请创建一个新的 issue，Kivy 开发者会尽快跟进处理。

已知的问题中绝大部分都太技术性了，没必要在这里写了，其中一个重要的问题是目前没办法移除一些链接库（比如 SDL_Mixer) ，因为 Kivy 项目需要这些链接库。在后续的版本中， Kivy 开发者会解决掉这些问题。



## FAQ[¶](https://kivy.org/docs/guide/packaging-ios.html#faq "Permalink to this headline")



### 程序异常退出![¶](https://kivy.org/docs/guide/packaging-ios.html#application-quit-abnormally "Permalink to this headline")

默认情况下，所有控制台和文件中的 print 语句都被忽视了。如果你运行程序的时候碰到异常状况了，可以激活 log 日志功能，在 main.m 文件中把下面这一行去掉注释：


```Bash
putenv("KIVY_NO_CONSOLELOG=1");
```
然后你就可以在 Xcode 控制台中看到 Kivy 的日志了。




### 渣果公司怎样才能接受一个 Python APP?[¶](https://kivy.org/docs/guide/packaging-ios.html#how-can-apple-accept-a-python-app "Permalink to this headline")

Kivy 开发者将所有链接库的 app 二进制文件合并成了一个名为 libpython 的二进制文件。这就意味着所有二进制模块都会提前价值，所以不会有动态加载。


### 是否已经向渣果的 App store 提交过 Kivy 应用?[¶](https://kivy.org/docs/guide/packaging-ios.html#have-you-already-submited-a-kivy-application-to-the-app-store "Permalink to this headline")

是，例如下面的就是：

* [Defletouch on iTunes](http://itunes.apple.com/us/app/deflectouch/id505729681),
* [ProcessCraft on iTunes](http://itunes.apple.com/us/app/processcraft/id526377075)

更详细的列表可以参考 [Kivy wiki](https://github.com/kivy/kivy/wiki/List-of-Kivy-Projects).







