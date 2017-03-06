Title: Kivy Pack Mac
Date: 2017-03-02
Category: Kivy
Tags: Python,Kivy

# Kivy中文编程指南：打包为 Mac 系统可执行文件

[英文原文](https://kivy.org/docs/guide/packaging-osx.html)

#### 特别注意

本文所提供的打包 Kivy 应用程序的方法必须在 OS X 系统内进行，而且不再支持 32 位平台。

## 使用 Buildozer

```Bash
pip install git+http://github.com/kivy/buildozer cd /to/where/I/Want/to/package buildozer init
```
（译者注：这里的/to/where/I/Want/to/package 就是你要打包的应用所在目录。）

然后就还是修改 buildozer.spec 文件，在里面添加好你的应用需要用到的信息。添加依赖包的位置在 requirements= 的那个位置。

默认情况下，requirements 位置所指定的 Kivy 版本会被忽略掉。

如果你在应用程序目录下有 Kivy.app （/Applications/Kivy.app），那就会用这个来打包。如果没有，还可以从 Kivy.org 下载最新版来用了。

如果你要用 Python3 来打包，就就直接从 Kivy.org 的下载页面下载 Kivy3.7z 这个包，然后把它解压缩到应用目录 /Applications 下，命名为 Kivy.app，然后运行：


```Bash
buildozer osx debug
```

打包好应用之后，就可以移除用不上的包了，比如如果你不用视频功能，那就可以去掉 gstreamer。同理，其他的用不上的功能，也都可以去掉，这就可以保证打包出来的应用能够有尽量小的体积，足够运行就好了。

作为示例，我们用这个方法打包好了一个应用，Python2 的版本大概 9MB 多一点，Python3 的是 15MB 左右，可以在[官方提供的谷歌网盘](https://drive.google.com/drive/folders/0B1WO07-OL50_alFzSXJUajBFdnc)下载来体验一下。（译者注：如果你身处大陆而无法访问谷歌这种不存在的网站，那么可以试试[我的百度网盘分享](https://pan.baidu.com/s/1gfBSD4B)。）

就这么多，动手试试吧。

目前的 Buildozer 使用了 Kivy SDK 来打包你的应用程序。如果你想对你的程序进行更深入的修改定制，而 buildozer 不足以满足你的需求，你可以试试直接使用 SDK，下面就要详细介绍一下这部分。


## 使用 Kivy SDK

从 1.9.0 版本开始，Kivy 就开始发布针对 OS X 平台的自我包含的便捷包。

用下面描述的方法就可以使用 Kivy SDK 来打包和发布应用程序了，要添加一些诸如 SDL2 或者 GStreamer 之类的包也都更简单了。

1 首先要确保有未修改过的原版 Kivy SDK，也就是从下载页面获取的 Kivy.app 这个文件。

2 然后运行下面的命令：

```Bash
mkdir packaging
cd packaging
packaging> git clone https://github.com/kivy/kivy-sdk-packager
packaging> cd kivy-sdk-packager/osx
osx> cp -a /Applications/Kivy.app ./Kivy.App
```

#### 特别注意

上面这一步是至关重要的，一定要确保目录和权限都没有问题。`cp -rf` 这样的命令也能实现复制，但会让应用程序无法运行，并且在后续步骤中导致各种错误。


3 接下来就是要把你用 Kivy.app 编译好的应用程序包含进目标文件夹，使用如下命令：

```Bash
osx> ./package-app.sh /path/to/your/app_folder_name>/
```
<app_folder_name> 就是你应用程序的名字。

这个命令会把 Kivy.app 复制成 <app_folder_name>.app，并把应用程序的一份编译好的副本包含进去。


4 就这些了，你的应用程序已经打包完毕，可以拿出去安装了。接下来可以按照下面的方法对你的应用进行更进一步的定制了。

### 安装模块

OS X 上的 Kivy 包邮自己的虚拟环境，当你使用 kivy 命令来运行的时候就会激活这个虚拟环境。如果要安装额外的一些模块，可以用如下命令：

```Bash
kivy -m pip install
```


### 模块和文件安到哪了？

在 Kivy.app 这个文件内部，虚拟环境位置如下：

```Bash
Kivy.app/Contents/Resources/venv/
```

如果你安装了一个安装二进制的模块，比如 kivy-garden。那么这些二进制文件只能在虚拟环境中才是可用的，就比如你先运行了下面这个命令：

```Bash
kivy -m pip install kivy-garden
```


然后安装的 garden lib 就只在你激活这个虚拟环境的时候才可用。

```Bash
source /Applications/Kivy.app/Contents/Resources/venv/bin/activate garden install mapview deactivate
```

### 安装二进制文件

这个比较简单，就把二进制文件复制到虚拟目录下的 bin 文件夹就可以了。（Kivy.app/Contents/Resources/venv/bin/）



### 包含其他框架

Kivy.app 已经自带了 SDL2 和 Gstreamer 这两个框架。要包含其他框架可以参考下面的方法：

```Bash
git clone http://github.com/tito/osxrelocator
export PYTHONPATH=~/path/to/osxrelocator
cd Kivy.app
python -m osxrelocator -r . /Library/Frameworks/<Framework_name>.framework/ \
@executable_path/../Frameworks/<Framework_name>.framework/
```

Do not forget to replace  with your framework. This tool osxrelocator essentially changes the path for the libs in the framework such that they are relative to the executable within the .app, making the Framework portable with the .app.

一定别忘了把上面样例中的 <Framework_name>  替换成你要安装的框架名字。osxrelocator 这个工具可以改变框架中的链接库的路径，这样就能让它们指向 .app 文件内的可执行文件，也就让此框架成为 .app 文件内可用的内置框架了。


#### 缩小应用体积

现在这个应用程序的体积可能已经相当大了，好在很多没有用上的部分可以从包中移除。

举例来说，如果你没有使用 Gstreamer，就可以从你应用程序的 .app 文件内的 /Contents/Frameworks 目录中把它删除掉。类似的像是在 /Applications/Kivy.app/Contents/Resources/kivy/ 目录下的 examples，tools，docs 等等这些文件夹都可以删掉的。

这样就可以让你的包只包含你的应用程序用到的内容。

#### 修改设置

通过修改应用程序的 .app 文件内 /Contents/info.plist 这个文件，就可以修改图标和其他的设置了。


#### 创建 DMG

用如下命令就可以创建一个 DMG 镜像文件了：

```Bash
osx> ./create-osx-dmg.sh YourApp.app
```

一定要注意末尾没有额外的 /。 这样就能生成一个压缩的 DMG 文件，能进一步缩小应用发布时候的体积了。



## 使用 PyInstaller，无 HomeBrew

首先是安装 Kivy 和依赖包，不用 HomeBrew 的方法可以在[官方文档](http://kivy.org/docs/installation/installation.html#development-version) 或者[译者的博客](http://blog.cycleuser.org/kivy-installment-tutorial.html)或者[译者的专栏](https://zhuanlan.zhihu.com/p/24644473)中查找。

安装好了Kivy 以及依赖包之后，就需要安装 PyInstaller 了。

（译者注：PyInstaller 目前不支持 Python3.6，时间为2017-03-02。）

假设用一个名为 testpackaging 的文件夹：


```Bash
cd testpackaging
git clone http://github.com/pyinstaller/pyinstaller
```

在这个目录中创建一个名为 touchtracer.spec 的配置文件，然后添加如下的代码到该文件中：


```Bash
# -*- mode: python -*-

block_cipher = None
from kivy.tools.packaging.pyinstaller_hooks import get_deps_all, hookspath, runtime_hooks

a = Analysis(['/path/to/yout/folder/containing/examples/demo/touchtracer/main.py'],
             pathex=['/path/to/yout/folder/containing/testpackaging'],
             binaries=None,
             win_no_prefer_redirects=False,
             win_private_assemblies=False,
             cipher=block_cipher,
             hookspath=hookspath(),
             runtime_hooks=runtime_hooks(),
             **get_deps_all())
pyz = PYZ(a.pure, a.zipped_data,
             cipher=block_cipher)
exe = EXE(pyz,
          a.scripts,
          exclude_binaries=True,
          name='touchtracer',
          debug=False,
          strip=False,
          upx=True,
          console=False )
coll = COLLECT(exe, Tree('../kivy/examples/demo/touchtracer/'),
               Tree('/Library/Frameworks/SDL2_ttf.framework/Versions/A/Frameworks/FreeType.framework'),
               a.binaries,
               a.zipfiles,
               a.datas,
               strip=False,
               upx=True,
               name='touchtracer')
app = BUNDLE(coll,
             name='touchtracer.app',
             icon=None,
         bundle_identifier=None)
```


把路径改到你的相对路径：

```Bash
a = Analysis(['/path/to/yout/folder/containing/examples/demo/touchtracer/main.py'],
            pathex=['/path/to/yout/folder/containing/testpackaging'],
...
...
coll = COLLECT(exe, Tree('../kivy/examples/demo/touchtracer/'),
```

然后运行如下命令：

```Bash
pyinstaller/pyinstaller.py touchtracer.spec
```



把这里的 touchtracer  替换成你的应用名称。之后就可以在 dist 文件夹下看到你的 .app 文件了。


## 使用 PyInstaller + HomeBrew



#### 特别注意

打包你的应用程序的时候，你定要在你要兼容的最老版本的 OS X 系统上进行。



### 完整指南

1 安装 [Homebrew](http://brew.sh/)

2 安装 Python:

```Bash
brew install python
```

#### 特别注意
如果要用 Python3 ，就用 `brew install python3` ，然后把下文中的 `pip` 改成 `pip3` 就可以了。



3 (Re)install your dependencies with `--build-bottle` to make sure they can be used on other machines:

```Bash
brew reinstall --build-bottle sdl2 sdl2_image sdl2_ttf sdl2_mixer
```


#### 特别注意

如果你的项目依赖 Gstreamer 或者其他的链接库，一定要按照下文的方法添加 `--build-bottle` 来安装。

4 安装 Cython 和 Kivy:

```Bash
pip install -I Cython==0.23
USE_OSX_FRAMEWORKS=0 pip install -U kivy
```



5 安装 PyInstaller:

```Bash
pip install -U pyinstaller
```

6 使用到 main.py 的路径来打包应用：

```Bash
pyinstaller -y --clean --windowed --name touchtracer \
      --exclude-module _tkinter \
      --exclude-module Tkinter \
      --exclude-module enchant \
      --exclude-module twisted \
      /usr/local/share/kivy-examples/demo/touchtracer/main.py


#### 特别注意

这样不能把额外的图像和声音文件复制进去。要添加这些内容还是要创建一个专门的`.spec`配置文件。

### 编辑`.spec` 配置文件

咱们用的这个配置文件是 touchtracer.spec ， 位置在刚刚运行了 pyinstaller 的目录。

需要对配置文件中的 COLLECT() 调用的部分进行修改，要添加上 touchtracer 用到的资源（比如touchtracer.kv， particle.png，等等）。修改这一行，添加一个 Tree() 对象。这个 Tree 会搜索 touchtracer 目录下的所有文件，并添加到你的包当中。 COLLECT 的那部分代码应该大概如下所示：


```Bash
coll = COLLECT(exe, Tree('/usr/local/share/kivy-examples/demo/touchtracer/'),
               a.binaries,
               a.zipfiles,
               a.datas,
               strip=None,
               upx=True,
               name='touchtracer')
```

这样会把需要的文件都添加进去，这样 PyInstaller 就能包含需要用到的 Kivy 文件了。弄妥了之后，你的 spec 配置文件就可以执行了。


### 使用 spec 来构建并打包成 DMG


1 打开终端。

2 进入到 PyInstaller 的目录，然后用如下命令进行构建：

```Bash
pyinstaller -y --clean --windowed touchtracer.spec
```

3 运行来试试：

```Bash
pushd dist
hdiutil create ./Touchtracer.dmg -srcfolder touchtracer.app -ov
popd
```

4 然后就能在 dist 目录下找到 Touchtracer.dmg 这个文件了。

### 额外的链接库

#### GStreamer

如果你的项目需要 GStreamer，那就运行如下命令：

```Bash
brew reinstall --build-bottle gstreamer gst-plugins-{base,good,bad,ugly}
```

#### 特别注意

如果你的项目需要对 Ogg Vorbis（音频压缩格式，类似于MP3，完全免费、开放和没有专利限制，支持多声道） 的支持，一定要在上面的命令后加上 `--with-libvorbis`。

如果你用的是通过 HomeBrew 安装的 Python，那就还需要下面这一步，除非[这个问题](https://github.com/Homebrew/homebrew/pull/46097)弄妥了：

```Bash
brew reinstall --with-python --build-bottle https://github.com/cbenhagen/homebrew/raw/patch-3/Library/Formula/gst-python.rb
```