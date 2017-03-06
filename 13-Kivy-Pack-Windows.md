Title: Kivy Pack Windows
Date: 2017-03-01
Category: Kivy
Tags: Python,Kivy

# Kivy中文编程指南：打包为 Windows 系统可执行文件

[英文原文](https://kivy.org/docs/guide/packaging-windows.html)

#### 特别注意

本文档仅适用于`1.9.1`以及更新版本的 Kivy。

要打包 Windows 平台的应用程序，**只能在 Windows 操作系统下完成**。另外一定要注意，本文后续内容中都是在 **wheels** 安装的 Kivy 下通过测试的，如果你用其他安装方法，参考结尾部分吧。

打包出来的程序是 32 位还是 64 位**只取决于**你打包使用的 Python，而不取决于 Windows 操作系统的版本。

## 依赖包

* **最新版本**的Kivy （参考安装指南 [Installation on Windows](https://kivy.org/docs/installation/installation-windows.html#installation-windows)）
* PyInstaller 3.1或者更新的版本 (`pip install --upgrade pyinstaller`)。

（译者注：PyInstaller 目前（2017年03月01日）还不支持 Python 3.6 哦~，好多朋友都坑到这里了，所以推荐使用 3.5.2。）

# PyInstaller 的基本用法

本节内容是要让 PyInstaller（3.1或者更新版本）包含 Kivy 的 Hook（钩子， Windows 消息处理机制的一个平台）。要覆盖默认的 Hook ，下面的样例代码需要稍微修改一下。参考 [ 覆盖默认 Hook](https://kivy.org/docs/guide/packaging-windows.html#overwrite-win-hook)。

## 打包一个简单的 APP

这个例子里面，咱们要把样例中的 **touchtracer** 这个项目进行打包，并且添加一个自定义图标。这里 Kivy 的样例目录要注意，如果是用 wheels 安装的，在 `python\\share\\kivy-examples` 这个位置，如果从 github 上面下载的，就在 `kivy\\examples` 这个位置。为了避免混乱，这里就用 `examples-path` 来指代这个目录的完整路径。然后 touchtracer 这个样例在 `examples-path\\demo\\touchtracer` 这个文件夹里，代码文件是 `main.py`。


###1 确保 Python
打开命令行确保 Python 包含在环境变量内，也就是说，输入 `python` 会出现解释器提示符。（译者注：cmd 或者 powershell 都可以，更推荐用后者，语法和 Bash 比较相似。）

###2 创建文件夹
在要打包 APP 的位置创建一个文件夹。比如咱们这次就创建一个名字为 `TouchApp` 的文件夹，然后用类似 `cd TouchApp` 这样的命令[进入到这个新建目录内](http://www.computerhope.com/cdhlp.htm)。之后输入：


```Python
python -m PyInstaller --name touchtracer examples-path\demo\touchtracer\main.py
```

还可以增加一个 icon.ico 文件到这个应用目录，这样就可以让程序有自己的图标了。如果没有自己的 .ico 图标文件，可以把你的 icon.png 文件转换成 ico，用这个 [ConvertICO](http://www.convertico.com/) 在线的应用就可以了。保存 icon.ico 到 touchtracer 这个目录里面，然后输入：

```Python
python -m PyInstaller --name touchtracer --icon examples-path\demo\touchtracer\icon.ico examples-path\demo\touchtracer\main.py
```

更多其它选项，请参考  [PyInstaller 官方说明](http://pythonhosted.org/PyInstaller/)。

###3 编辑配置文件
在 `TouchApp` 里面会有一个配置文件 `touchtracer.spec`。咱们需要编辑修改一下这个文件，在里面增加一些依赖包的 hook，这样才能保证正确创建 exe。接下来就是打开编辑器了，爱用啥都行，然后在配置文件的开头添加上下面这句：（这里是假设用的是 sdl2， 现在 Kivy 默认使用这个）

```Python
from kivy.deps import sdl2, glew
```

然后，用搜索，找到 `COLLECT()` 这个位置，添加上 touchtracer 用到的其他文件（touchtracer.kv, particle.png,等等）：修改示例中的行位置，添加一个 `Tree()` 对象，例如这里的是 `Tree('examples-path\\demo\\touchtracer\\')`。这个 `Tree()` 会搜索在当前这个 touchtracer 文件夹的所有文件，并添加到你最终打包的程序中。



要添加额外的依赖包，就要在 COLLECT 的**第一个关键词参数的前面**，为每一个依赖包的路径添加一个 Tree 对象。例如下面的就是以 `*[Tree(p) for p in(sdl2.dep_bins + glew.dep_bins)]` 为例：


```Python
coll = COLLECT(exe, Tree('examples-path\\demo\\touchtracer\\'),
                   a.binaries,
                   a.zipfiles,
                   a.datas,
                   *[Tree(p) for p in (sdl2.dep_bins + glew.dep_bins)],
                   strip=False,
                   upx=True,
                   name='touchtracer')
```

###4 进行构建
接下来就用 `TouchApp` 里面这个 spec 配置文件来进行构建了：

```Python
python -m PyInstaller touchtracer.spec
```

###5 生成位置
编译好的包会在 TouchApp\dist\touchtracer 这个目录。


## 使用 gstreamer 创建一个视频应用


接下来就是修改一下上面的这个样例了，这回要打包的 APP 是一个使用了 gstreamer 的视频应用。咱们这回用样例中的视频播放器的例子 `videoplayer`，代码在`examples-path\widgets\videoplayer.py`。另外创建一个名字为 `VideoPlayer` 的文件夹，然后在命令行中进入到这个文件夹，之后操作如下：


```Python
python -m PyInstaller --name gstvideo examples-path\widgets\videoplayer.py
```

这回要修改 `gstvideo.spec` 这个文件。跟上文的方法类似，也就是把 gstreamer 的依赖包放进去：

```Python
from kivy.deps import sdl2, glew, gstreamer
```

然后就是增加 `Tree()` 来包含好要用的视频文件，`Tree('examples-path\\widgets')` 和 gstreamer 依赖都得弄好，大概如下所示：

```Python
coll = COLLECT(exe, Tree('examples-path\\widgets'),
               a.binaries,
               a.zipfiles,
               a.datas,
               *[Tree(p) for p in (sdl2.dep_bins + glew.dep_bins + gstreamer.dep_bins)],
               strip=False,
               upx=True,
               name='gstvideo')
```

接下来就是使用 `VideoPlayer` 文件夹中的这个 spec 配置文件来进行构建了：

```Python
python -m PyInstaller gstvideo.spec
```

然后你就能在 `VideoPlayer\dist\gstvideo` 这个位置找到 gstvideo.exe 这个文件了，运行一下就能播放视频了。

#### 特别注意

如果你用了 Pygame，或者你打包的程序需要 Pygame，那在你的 spec 文件里面就还得添加如下的代码，在 import 导入语句的后面添加（详情参考 [kivy issue #1638](github.com/kivy/kivy/issues)）：

```Python
def getResource(identifier, *args, **kwargs):
    if identifier == 'pygame_icon.tiff':
        raise IOError()
    return _original_getResource(identifier, *args, **kwargs)

import pygame.pkgdata
_original_getResource = pygame.pkgdata.getResource
pygame.pkgdata.getResource = getResource
```


# 覆盖默认 Hook


## 包含/移除视频音频以及缩小应用体积

PyInstallers 默认会将 Kivy 用到的所有核心模块和这些模块的依赖包，**全部**都添加成 hook，比如音频，视频，拼写等等（然而 gstreamer 的 dll 还是需要你用 `Tree()` 来手动添加，参考上文）。有的 Hook 并没有用到或者想要缩小应用的体积，就都可以尝试着移除一些模块，比如如果没有用到音频和视频，就可以用自定义的 Hook了。

Kivy 在[`hookspath()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.hookspath "kivy.tools.packaging.pyinstaller_hooks.hookspath") 提供了可选的 Hook。

此外，当且仅当 PyInstaller 没有默认的 hook 的时候，就必须得提供一个[`runtime_hooks()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.runtime_hooks "kivy.tools.packaging.pyinstaller_hooks.runtime_hooks")。 覆盖 hook的时候，这个[`runtime_hooks()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.runtime_hooks "kivy.tools.packaging.pyinstaller_hooks.runtime_hooks") 不需要覆盖。


可选自定义的[`hookspath()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.hookspath "kivy.tools.packaging.pyinstaller_hooks.hookspath") hook不包含任何 Kivy 的 provider。要添加电话，就要用[`get_deps_minimal()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.get_deps_minimal "kivy.tools.packaging.pyinstaller_hooks.get_deps_minimal") 或者 [`get_deps_all()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.get_deps_all "kivy.tools.packaging.pyinstaller_hooks.get_deps_all")来添加。可以看看相关的文档以及[`pyinstaller_hooks`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#module-kivy.tools.packaging.pyinstaller_hooks "kivy.tools.packaging.pyinstaller_hooks")来了解更多信息。不过[`get_deps_all()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.get_deps_all "kivy.tools.packaging.pyinstaller_hooks.get_deps_all")跟默认的 hook一样，都是把所有 provider 都添加进去；而[`get_deps_minimal()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.get_deps_minimal "kivy.tools.packaging.pyinstaller_hooks.get_deps_minimal") 只添加在应用程序运行的时候加载了的内容。

这两个方法都提供了一个 Kivy 隐藏导入列表，以及排除的导入，可以传递出来给 `Analysis`。

还可以生成一个自定义 hook，一个个列出每一个 kivy 的 provider 模块，然后把其中用不上的就注释掉就行了。

参考 [`pyinstaller_hooks`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#module-kivy.tools.packaging.pyinstaller_hooks "kivy.tools.packaging.pyinstaller_hooks").

要在上面的例子中使用自定义 hook，要按照下面给出的范例来修改，以`hookspath()` 和 `runtime_hooks`（必要情况下），然后是`**get_deps_minimal()` 或者 `**get_deps_all()`来制定好各种 provider。


例如，增加了导入语句 `from kivy.tools.packaging.pyinstaller_hooks import  get_deps_minimal, get_deps_all, hookspath,runtime_hooks` ，然后按照如下方式修改`Analysis`：

```Python
a = Analysis(['examples-path\\demo\\touchtracer\\main.py'],
             ...
             hookspath=hookspath(),
             runtime_hooks=runtime_hooks(),
             ...
             **get_deps_all())
```

上面这个实际上跟默认 hook 一样包含全部了。或者可以：

```Python
a = Analysis(['examples-path\\demo\\touchtracer\\main.py'],
             ...
             hookspath=hookspath(),
             runtime_hooks=runtime_hooks(),
             ...
             **get_deps_minimal(video=None, audio=None))
```
这样就是移除了声音视频的 provider，这就只加载了用到的核心模块了。

关键就是要提供自定义的 [`hookspath()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.hookspath "kivy.tools.packaging.pyinstaller_hooks.hookspath")，这个默认并不会列出全部的 kivy provider，而是手动的来设定隐藏导入的模块和需要用的 provider，通过[`get_deps_minimal()`](https://kivy.org/docs/api-kivy.tools.packaging.pyinstaller_hooks.html#kivy.tools.packaging.pyinstaller_hooks.get_deps_minimal "kivy.tools.packaging.pyinstaller_hooks.get_deps_minimal")来移除用不上的模块 (比如上面的是声音影像)。





## 其他安装方式

前面的这些例子都用到了 `*[Tree(p) for p in (sdl2.dep_bins + glew.dep_bins + gstreamer.dep_bins)],` 这样的语句来保证 PyInstaller 把所有依赖包用到的 dll 都添加进去。如果不是用 wheel 的方法安装的 Kivy，那么这些命令就很可能失败，比如 `kivy.deps.sdl2` 可能就无法导入。这时候，你就必须得找到这些 dll 文件的位置，然后手动地一个个传递给 `Tree` 类，传递方法和上面说的基本差不多了。

（译者注：Windows 平台还是推荐用 wheel 二进制安装，省心多了。）


