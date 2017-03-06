Title: Kivy Android
Date: 2017-03-07
Category: Kivy
Tags: Python,Kivy


# Kivy中文编程指南：KV Android 详细指南


[英文原文](https://kivy.org/docs/guide/android.html)

在 Android 设备上，只要是支持 OpenGL ES 2.0 （至少为 Android 2.2 以及其后的版本），就基本都能运行 Kivy 应用程序。OpenGL ES 2.0 基本上是现代设备的标准了；根据谷歌的报道说，至少有 [99.9% 的设备都是支持的](https://developer.android.com/about/dashboards/index.html)。

Kivy 的 APK 就是常规的 Android APP，和其他的 APP 一样可以四处发布，比如谷歌 Play 商店等等。在暂停或者重启的时候这些应用的行为也都很正常，下面要介绍道的是 Kivy APP 用到的 Android 服务和需要用到的大多数常规的 Java API。

下面的内容依次讲解了如何 [针对 Android 平台打包 APP](https://kivy.org/docs/guide/android.html#package-for-android)， [在设备上调试 APP](https://kivy.org/docs/guide/android.html#debug-android)，以及使用震动或者读取传感器等等  [Android API](https://kivy.org/docs/guide/android.html#using-android-apis)。




## 针对 Android 平台打包 APP[¶](https://kivy.org/docs/guide/android.html#package-for-android "Permalink to this headline")

Kivy 项目提供了针对 Android 平台打包 APP 所需的全部必备工具，可以构建单个的 APK 文件发布到谷歌 Play 市场之类的应用商店。详细内容可以参考[针对 Android 打包应用程序的英文原版文档](https://kivy.org/docs/guide/packaging-android.html#packaging-android)或者中文翻译版本：[个人博客地址](http://blog.cycleuser.org/kivy-pack-android.html)，[知乎专栏地址](https://zhuanlan.zhihu.com/p/25571974)。


## 在 Android 设备上调试 APP[¶](https://kivy.org/docs/guide/android.html#debugging-your-application-on-the-android-platform "Permalink to this headline")

通过 Android Logcat 流，可以观察代码的常规输出（比如 stdout，stderr），也可以查看常规的 Kivy 日志。这需要使用 adb 来查看，adb 包含在 [Android SDK](http://developer.android.com/sdk/index.html) 内。这首先就要求你再设备上开启**开发者模式**，然后启用 USB 调试功能来启用 adb，接着把设备连接到计算机，在终端中运行下面的命令：


```Bash
adb logcat
```

这样就能看到日志输出了，包括标准输出和出错信息（ stdout/stderr ）以及 Kivy 的日志。

如果你用 Buildozer 打包的 APP，那么有可能 adb 工具没有包含在你的 `$PATH`  环境变量中，这样上面的命令就可能没有效果。这时候可以用下面的方法：


```Bash
buildozer android logcat
```

上面这样就是运行了 Buildozer 伴随安装的 adb 工具，或者还可以去`$HOME/.buildozer/android/platform` 这个目录找到 Buildozer 伴随安装的 SDK。

或者还可以下载[Kivy Launcher](https://play.google.com/store/apps/details?id=org.kivy.pygame&hl=en)来运行和调试应用程序。如果用这种方法运行 Kivy 应用程序，可以在你的应用程序所在目录下找到一个名字为 `/.kivy/logs` 的子目录，里面就是日志文件。



## 使用 Android API[¶](https://kivy.org/docs/guide/android.html#using-android-apis "Permalink to this headline")

虽然 Kivy 是一个 Python 框架，Kivy 项目也还维护了一套用来调用常规 Java API 的工具，可以用来处理震动、传感器、发送短信或邮件等等。

对新用户来说，推荐阅读 [Plyer](https://kivy.org/docs/guide/android.html#plyer)。要想有更深入的使用或者调用一些目前没有封装的 API，可以直接使用 [Pyjnius](https://kivy.org/docs/guide/android.html#pyjnius)。Kivy 还内置了一个  [Android 模块](https://kivy.org/docs/guide/android.html#android-module) 来实现 Android 的一些基础功能。

在 [Kivy wiki](https://github.com/kivy/kivy/wiki#mobiles) 上可以找到用户提供的 Android 代码和样例。

### Plyer[¶](https://kivy.org/docs/guide/android.html#plyer "Permalink to this headline")

[Plyer](https://github.com/kivy/plyer) 是一个 Python 风格的，独立于平台的 API，用于使用各种平台上都普遍具有的功能，尤其是移动平台。其设计思路是你的应用可以简单地调用一个 Plyer 函数，例如给用户一个消息通知，然后 Ply会处理在不同的平台或者操作系统下分别如何把这件事完成。在 Plyer 的内部，在 Android 平台使用的是 Pyjnius，在 iOS 平台使用了 Pyobjus，在桌面平台又用了其他的特定 API。

例如，下面的代码就会让你的 Android 设备震动，或者当你在其他平台不恰当地使用的时候就会跑出一个 NotImplementError，例如在桌面平台等没有对应硬件的情况下：

```Python
from plyer import vibrator
vibrator.vibrate(10)  # vibrate for 10 seconds
```

Plyer 支持的 API 越来越多了，可以在 [ Plyer GitHub 页面的 README 文件](https://github.com/kivy/plyer)中查看完整的支持列表。

### Pyjnius[¶](https://kivy.org/docs/guide/android.html#pyjnius "Permalink to this headline")

Pyjnius 是一个 Python 模块，它允许你直接在 Python 中读取 Java 类，自动转换参数成正确的类型，还允许你把 Java 的运行结果转换给 Python。

Pyjnius 可以从 [它的 GitHub 地址](https://github.com/kivy/pyjnius)下载获得，并且有一份[详细的文档](http://pyjnius.readthedocs.org/en/latest/)。

下面的代码是一个简单的小例子，展示了如何使用 Pyjnius 来读取常规的 Android 震动 API，就跟上面 Plyer 的代码效果一样：

```Python
# 'autoclass' 接收一个Java 类，然后打包给 Python；
from jnius import autoclass

# Context 是 Android API 中一个常用的 Java 类；
Context = autoclass('android.content.Context')

# PythonActivity 是在 python-for-android 内由 Kivy bootstrap app 提供的一个类；
PythonActivity = autoclass('org.renpy.android.PythonActivity')

# 这里的 PythonActivity 存储了一个指向当前运行的 Activity 的引用；
# 我们要用它来读取 震动服务
activity = PythonActivity.mActivity

# 底下的这个振动器代码和 Java 里面基本一样的；
vibrator = activity.getSystemService(Context.VIBRATOR_SERVICE)

vibrator.vibrate(10000)  # 这个值是毫秒为单位，这里设置的 10 000 毫秒相当于 10 秒。
```

上面的代码直接用了 Java API 函数来调用了振动器，Pyjnius 自动把 API 转换出给了 Python 代码使用，而又把我们的调用都回传给了 Java。相比 Plyer 的实现，这种方法更繁琐一些，也更像 Java 的风格，在这个例子中没有什么优势。不过 Plyer 也并没有对 Pyjnius 的所有 API 都进行了封装。

Pyjnius 还有一个强大的功能就是实现 Java 接口，这在封装某些 API 的时候非常重要，不过这里就不详细讲这么多了，有兴趣的话去[ Pyjnius 的官方文档](http://pyjnius.readthedocs.org/en/latest/)来了解更深层次内容吧。


### Android 模块[¶](https://kivy.org/docs/guide/android.html#android-module "Permalink to this headline")

Python-for-android 项目中包含了一个 Python 模块（实际上是用 cython 封装的 Java）来读取一系列有限的 Android API。这个很大程度上已经被上面的 Pyjnius 和 Pyler 取代了，因为后者更加灵活方便，不过有时候可能这个模块还有些用处。所有可用的文档都可以在 [python-for-android 官方文档](http://python-for-android.readthedocs.org/en/latest/)中查阅到。

这其中就包括了计费/应用内购买的代码，以及创建或者读取某些 Android 服务的代码，其他工具目前还没能提供这方面的功能。


## 项目状态以及通过测试的设备[¶](https://kivy.org/docs/guide/android.html#status-of-the-project-and-tested-devices "Permalink to this headline")

前面的章节讲述了 Kivy 在 Android 系统的构建工具，以及他们各自的缺陷还有就是已知能够使用的设备。

Android 工具链现在挺稳定的，一定程度上基本能适用于各种设备了；Kivy 的最低要求是 OpenGL ES 2.0 以及 Android 2.2。这现在绝对是覆盖面很广泛了— Kivy 已经都可以在 Android 智能手表上面运行了。

当前在技术上存在的一个限制就是 Android 构建工具只能生成 ARM 平台的 APK 文件，这些文件不能运行于 X86 处理器的 Android 设备上，好在目前 这类 X86 的 Android 设备还不是主流。不过对 X86 处理器的 Android 设备的支持是后续要添加的。

因为目前 Kivy 基本上能在绝大多数 Android 设备上面良好运行了，所以之前的那个设备支持列表就关荣退休了—只要满足上面的要求的 Android 设备，基本就都能够使用。

