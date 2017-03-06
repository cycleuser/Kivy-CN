Title: Kivy Pack License
Date: 2017-03-07
Category: Kivy
Tags: Python,Kivy

# Kivy中文编程指南：授权协议

[英文原文](https://kivy.org/docs/guide/licensing.html)


#### 警告

这并不是一个律师咨询指南！ Kivy 的开发组织，本指南的作者以及参与者，对任何信息缺失、产生误导，以及任何基于这份指南的行为产生的任何后果都不负任何责任。这个指南只是提供一些信息，目的是帮助缺乏经验的用户。

你的代码本身并不一定需要包括协议信息和对其他用到的软件的版权声明，不过二进制文件就不一样了。


当你创建一个二进制文件（比如 exe，app 或者 APK 等等）的时候，里面包括了 Kivy 以及其他的一些依赖项目或者你的应用程序用到的其他的包，其中的某些就可能有声明需要你再自己的应用程序中进行版权信息声明。在你对这些二进制文件进行发布之前，一定要检查所有不属于你源代码的**创建出来的文件**，（比如 dll，pyd，so 等等）然后如果某一个文件需要有版权信息，记得加进去版权声明。这样你才能满足 Kivy 开发需要的版权要求。


## 依赖包[¶](https://kivy.org/docs/guide/licensing.html#dependencies "Permalink to this headline")

在 Kivy 支持的每个平台上都或多或少地用到了下面这些依赖包，所以你需要添加这些授权协议进去，基本都是只要你粘贴一段版权声明到你的应用中，而不能当作自己写了这些功能代码。

* [docutils](https://sf.net/p/docutils/code/HEAD/tree/trunk/docutils/COPYING.txt)
* [pygments](https://bitbucket.org/birkenfeld/pygments-main/src/tip/LICENSE)
* [sdl2](https://www.libsdl.org/license.php)
* [glew](http://glew.sourceforge.net/glew.txt)
* [gstreamer](https://github.com/GStreamer/gstreamer/blob/master/COPYING) (如果用到了再添加)
* 图像和音频库(例如 [SDL_mixer](http://hg.libsdl.org/SDL_mixer/file/default/VisualC/external/lib/x86))

对于图像和音频库，可能需要你手动去检查一下，一般都是以  `lib` 这三个字母开头的。这些程序的 `LICENSE*` 授权协议文件会在 PyInstaller 里面包含，但在 python-for-android 则没有，所以你得自己查找一下。


## Windows 操作系统 (PyInstaller)[¶](https://kivy.org/docs/guide/licensing.html#windows-pyinstaller "Permalink to this headline")

要使用 Windows API 功能， Kivy 使用了 [pypiwin32](https://pypi.python.org/pypi/pypiwin32) 。这个包是基于  [PSF 协议](https://opensource.org/licenses/Python-2.0)发布的。



### VS 可再发行组件[¶](https://kivy.org/docs/guide/licensing.html#vs-redistributables "Permalink to this headline")

使用 Visual Studio 编译的 Python（官方版本）使用了来自微软的一些文件，在特定条件下基于 CRT license 可以重新发布这些组件。包括这些文件名以及[Py2 CRT 协议](https://hg.python.org/sandbox/2.7/file/tip/Tools/msi/crtlicense.txt) 或者 [Py3 CRT 协议](https://hg.python.org/cpython/file/tip/Tools/msi/exe/crtlicense.txt)，主要看你用的是哪个版本的解释器，所以要针对你的发布对象来具体情况具体对待。

* [可再发行组件列表](https://msdn.microsoft.com/en-us/library/8kche8ah(v=vs.90).aspx)



### 其他链接库[¶](https://kivy.org/docs/guide/licensing.html#other-libraries "Permalink to this headline")

* [zlib](https://github.com/madler/zlib/blob/master/README)



#### 特别注意

对那些没有直接使用，但是打包的时候用到的包要列出，比如在 Windows 系统上面用的 PyInstaller。



## Linux[¶](https://kivy.org/docs/guide/licensing.html#linux "Permalink to this headline")

GNU/Linux 操作系统现在有好多发行版，所以没有一个能够通用给所有发行版的指南。这部分也属于 RPi （不知道是什么鬼东西）。然而可以简化成两种打包方式，（还是用 PyInstaller 来打包）提供包含的二进制文件，或者不提供。

如果包含了二进制文件，应该逐个检查这些文件，比如 so 为扩展名的，除了你的代码之外，要找到对应这些文件的授权协议。根据这个协议你可能需要在你的程序里面添加一条对应的版权信息。

如果没有包含二进制文件，比如你用 deb 等格式的文件进行打包，那就[把麻烦扔给你的用户了](https://kivy.org/docs/guide/licensing.html#avoid)。你可以自己决定是否要满足其他授权的要求，例如在你的应用中是否添加额外的版权信息。



## Android[¶](https://kivy.org/docs/guide/licensing.html#android "Permalink to this headline")

APK 实际上只是一个文件压缩包，所以可以解压缩这个文件（就像 Windows 里面那样做）然后检查每个文件。

`APK/assets/private.mp3/private.mp3/`这个文件夹内有所有包含的文件。大多数的都是和 Kivy 、Python 或者你的代码相关的，不过那些与这些无关的就需要检查一下了。

已知的包：

* [pygame](https://bitbucket.org/pygame/pygame/src/tip/LGPL) (如果用了旧的工具链)
* [sqlite3](https://github.com/ghaering/pysqlite/blob/master/LICENSE)
* [six](https://bitbucket.org/gutworth/six/src/tip/LICENSE)


有的包含的链接库是 Kivy 直接使用或者通过 Pygame/SDL2 来使用的，他们的位置在 `APK/lib/armeabi/`。大多数都是和依赖包相关，要么就是由 python-for-android 产生，并且可能就是 python-for-android 的一部分。例如`libapplication.so`等。




## Mac[¶](https://kivy.org/docs/guide/licensing.html#mac "Permalink to this headline")

Missing.
这部分 Kivy 官方文档没写其他内容。


## iOS[¶](https://kivy.org/docs/guide/licensing.html#ios "Permalink to this headline")

Missing.
这部分 Kivy 官方文档没写其他内容。




## 避免二进制文件[¶](https://kivy.org/docs/guide/licensing.html#avoiding-binaries "Permalink to this headline")

有一种方法也许能够避免这种很狗很麻烦的授权协议什么的鬼东西，就是不用任何第三方的鬼扯玩意来构建你的应用。你可以用 Python 来自己创立一个模块，在其中的 `__main__.py`都只有你自己的代码，而 `setup.py` 列出需要的依赖包。

这样你依然可以发布你的应用—就是你的**代码**，然后你就不用管任何其他的授权协议了。不过这样就更像是搭配使用，而不太算是发布程序了。这时候满足各种授权的依赖关系，就转移到你的程序的用户身上了，他们需要自行搞定运行环境来使用这个模块。如果你比较关心自己的用户，建议你还是花点时间来阅读一下[可能导致的后果](http://programmers.stackexchange.com/a/234295)。

