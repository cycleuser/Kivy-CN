Title: Kivy IDE
Date: 2017-02-19
Category: Kivy
Tags: Python,Kivy


# 在各种流行的 Python IDE 中配置 Kivy 集成开发环境


 * [英文原文](https://github.com/kivy/kivy/wiki/Setting-Up-Kivy-with-various-popular-IDE's)


## 在 Windows 系统上配置 PyCharm 使用 Kivy


从 1.9.1 开始， Kivy 就可以安装到你系统中已有的 Python 解释器中，所以在 Windows 系统上面的安装非常简单直接。


 * 1 在 Windows 系统上安装 Kivy；可以参考[英文原版安装指南](http://kivy.org/docs/installation/installation-windows.html)或者[我博客里的安装指南](http://blog.cycleuser.org/kivy-installment-tutorial.html),或者参考[我的知乎专栏](https://zhuanlan.zhihu.com/p/24632231?refer=python-kivy)。
 * 2 然后就在 PyCharm 里面建立或者打开你的项目就可以了。



理论上就这么简单，两步就搞定了。如果你有多个 Python 解释器，那就需要选择安装了 Kivy 的那个才行。具体步骤是在 PyCharm 里面，按照如下流程操作：

 * 1 File 文件 -> Settings 设置 -> Project 项目 -> Project Interpreter 项目解释器  
 * 2 取消掉当前的项目解释器，选择你要用的安装了 Kivy 的 Python 解释器就好了。

这就完毕了，接下来就可以用 Kivy 来玩耍了。


## 在 macOS 系统上配置 PyCharm 使用 Kivy



Kivy 开发团队推荐 macOS 用户使用 [homebrew](http://brew.sh) 来安装 Kivy。这很简单，在 Mac 系统里面打开 Terminal 终端就可以了。下面的代码输入或者复制粘贴到终端提示符后面就可以了。
要注意这些代码特别长，所以可能因为文字换行等等导致出现多行的情况，这时候一定要注意要复制完整。
除了用 brew 和 pip 来安装 Kivy 的方法之外，还可以参考其他的详细安装指南，比如[我博客里的安装指南](http://blog.cycleuser.org/kivy-installment-tutorial.html)或者[我的知乎专栏](https://zhuanlan.zhihu.com/p/24644473?refer=python-kivy)。


* 1 安装 Homebrew

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

* 2 通过 Brew 来安装 Python 3

```bash
brew install python3
```

或者 Python 2
```bash
brew install python
```

* 3 安装 Kivy （参考[我博客里的安装指南](http://blog.cycleuser.org/kivy-installment-tutorial.html)或者[我的知乎专栏](https://zhuanlan.zhihu.com/p/24644473?refer=python-kivy)）。这里一定要注意，下面的命令使用了 pip3 ，意思是说安装到了 Python3 里面，如果你要用 Python2 ，那就把下面命令中的 pip3 替换成 pip：

```bash
brew install hg sdl sdl2 sdl2_image sdl2_ttf sdl2_mixer gstreamer
pip3 install --upgrade pip
pip3 install cython
USE_OSX_FRAMEWORKS=0 pip3 install kivy
```


PyCharm 应该能选择你默认的 Python 解释器，然后（估计差不多）就是你刚刚把 Kivy 座位一个模块来安装好的那个解释器。

可以通过下面的路径来对已有模块进行检查： File 文件 -> Settings 设置 -> Project 项目 -> Project Interpreter 项目解释器


## Kivy 语言自动补齐以及代码高亮

ID 为 Xuton 的一位好心朋友开发了一个扩展文件，可以提供 Kivy 语言的自动补齐和代码高亮，安装方法如下：


* 下载[这个文件](https://github.com/Zen-CODE/kivybits/blob/master/IDE/PyCharm_kv_completion.jar?raw=true)；
* 打开 Pycharm 的主菜单，点击 File 文件 -> Import 导入 ( 或者 Import Settings 导入设置)；
* 选择刚刚下载的那个 jar 文件，然后 PyCharm 会弹出一个对话框让你来确认，点击 OK 就行了。
* 重新启动 PyCharm ，看看是不是有效果了？

# 更多其他 IDE

这些我就不翻译了，大家可以去看看。

* [How to use Python Tools for Visual Studio to develop and run Kivy Applications](https://groups.google.com/forum/#!topic/kivy-users/tZTuUzUHBwc/)
* [kivy-eclipse-and-pydev-also-pypy](http://stackoverflow.com/questions/9768489/kivy-eclipse-and-pydev-also-pypy)
* [How to Set up Wing IDE for Kivy on Windows](http://www.blog.pythonlibrary.org/2013/11/18/how-to-set-up-wing-ide-for-kivy-on-windows/)
* [Kv language definition file for gedit](https://wiki.gnome.org/Projects/GtkSourceView/LanguageDefinitions?action=AttachFile&do=view&target=kv.lang)


