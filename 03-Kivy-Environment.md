Title: Kivy Environment
Date: 2017-02-5
Category: Kivy
Tags: Python,Kivy


# Kivy中文编程指南：环境变量

[英文原文](https://kivy.org/docs/guide/environment.html)


Kivy的初始化和很多行为都可以通过各种环境变量来控制。


例如，若要严格设定用PIL进行文本渲染，可以通过如下方式来实现：


```Bash
$ KIVY_TEXT=pil python main.py
```
(译者注：PIL，Python Imaging Library, Python 下常用的绘图库) 


所有的这些环境变量的修改设定都需要在导入Kivy之前进行，具体如下所示：



```Python
import os
os.environ['KIVY_TEXT'] = 'pil'
import kivy
```







## 路径控制


* 从Kivy1.0.7版本开始提供



You can control the default directories where config files, modules and kivy data are located.

Kivy的配置文件、模块以及数据存储的默认目录，都可手动设定所在位置。


>KIVY_DATA_DIR


这个是Kivy的数据目录，默认值为/data 。


>KIVY_MODULES_DIR

这个是Kivy的模块目录，默认值为/modules 。

>KIVY_HOME

这个是Kivy的HOME目录，该目录是用来存放本地配置文件的，必须是一个可以写入的位置。对应不同系统也有不同位置：

	* Desktop: /.kivy
	* Android: /.kivy
	* iOS: /Documents/.kivy

* 从Kivy1.9.0版本开始提供





>KIVY_SDL2_PATH


这个变量若设定了，编译Kivy的时候就会使用该位置的SDL2库文件，而不再使用系统的库文件。在环境变量PATH的开头部位就要设定好这个变量，这样在运行一个Kivy应用的时候才能也使用相同的SDL2库文件。



* 从Kivy1.9.0版本开始提供





####特别注意

刚刚这个SDL2路径是用来编译Kivy的。运行程序的话就用不着了。











## 配置文件


>KIVY_USE_DEFAULTCONFIG

若设定了此环境变量，Kivy会读取制定的配置文件。


>KIVY_NO_CONFIG

若设定了此环境变量，Kivy将不会读取也不会写入任何配置文件。也适用于用户配置文件夹的位置。（译者注：这句话我还没弄明白，因为没有这样尝试。）

>KIVY_NO_FILELOG

若设定了此环境变量，日志将不再输出到文件内。


>KIVY_NO_CONSOLELOG

若设定了此环境变量，日志将不再输出到控制台。


>KIVY_NO_ARGS

若设定了此环境变量，命令行传递的参数将不会被Kivy解析和使用。也就是说，可以不用 – 定义符，就能随便创建一个使用自己参数的脚本或者应用：


```Python
import os
os.environ["KIVY_NO_ARGS"] = "1"
import kivy
```





* 从Kivy1.9.0版本开始提供



## 限定Kivy.core核心，使用特定版本

Kivy.core会尝试使用所在平台的最优实现。如果要测试或者定制安装，你可能要把选择器设定为某个特定版本的kivy.core。




>KIVY_WINDOW

这一变量是用来设定如何创建窗口，可用值：sdl2, pygame, x11, egl_rpi



>KIVY_TEXT

这一变量是用来设定如何渲染文本，可用值：sdl2, pil, pygame, sdlttf



>KIVY_VIDEO

这一变量是用来设定如何渲染视频，可用值：pygst, gstplayer, pyglet, ffpyplayer, ffmpeg, gi, null



>KIVY_AUDIO

这一变量是用来设定如何播放声音，可用值：sdl2, gstplayer, pygst, ffpyplayer, pygame, gi, avplayer



>KIVY_IMAGE

这一变量是用来设定如何读取图像，可用值：sdl2, pil, pygame, imageio, tex, dds, gif



>KIVY_CAMERA

这一变量是用来设定如何读取摄像头，可用值：videocapture, avfoundation, pygst, opencv



>KIVY_SPELLING

这一变量是用来设定拼写，可用值： enchant, osxappkit



>KIVY_CLIPBOARD


这一变量是用来设定剪切板管理组件，可用值：sdl2, pygame, dummy, android









## 设置单位



>KIVY_DPI

这个是用来设定Metrics.dpi的dpi值的。

* 从Kivy1.4.0版本开始提供



>KIVY_METRICS_DENSITY

这个是用来设定Metrics.density，像素密度。

* 从Kivy1.5.0版本开始提供



>KIVY_METRICS_FONTSCALE

这个是用来设定Metrics.fontscale，字体大小。

* 从Kivy1.5.0版本开始提供
 





## 图形输出



>KIVY_GL_BACKEND

此变量用于设定使用的OpenGL后端，更多细节参考[cgl](http://kivy.org/docs/api-kivy.graphics.cgl.html#module-kivy.graphics.cgl "kivy.graphics.cgl").

>KIVY_GL_DEBUG

此变量用于设定是否对OpenGL调用进行日志记录，更多细节参考[cgl](http://kivy.org/docs/api-kivy.graphics.cgl.html#module-kivy.graphics.cgl "kivy.graphics.cgl").

>KIVY_GRAPHICS

此变量用于设定是否使用OpenGL ES2，更多细节参考[cgl](http://kivy.org/docs/api-kivy.graphics.cgl.html#module-kivy.graphics.cgl "kivy.graphics.cgl").

>KIVY_GLES_LIMITS

此变量用于设定是否强制设定GLES2（默认值为启用，设置为1）。如果设定为false，Kivy将不再兼容GLES2。（译者注：这部分我不懂，就直接生硬翻译了原文，建议大家参考一下原文去理解。）如果设置为true，可能有下表中所示的潜在的不兼容情况：



| Mesh indices | If true, the number of indices in a mesh is limited to 65535 |
| ------------- |:-------------:|
| Texture blit | When blitting to a texture, the data (color and buffer) format must be the same format as the one used at the texture creation. On desktop, the conversion of different color is correctly handled by the driver, while on Android, most of devices fail to do it. Ref: [https://github.com/kivy/kivy/issues/1600](https://github.com/kivy/kivy/issues/1600) |



* 从Kivy1.8.1版本开始提供




>KIVY_BCM_DISPMANX_ID

此变量是针对Raspberry Pi树莓派平台的，用于设定所选择的视频输出端口。默认值为0，下面列表中是在vc_dispmanx_types.h这个头文件中存储的可供选择的变量值：

* 0: DISPMANX_ID_MAIN_LCD
* 1: DISPMANX_ID_AUX_LCD
* 2: DISPMANX_ID_HDMI
* 3: DISPMANX_ID_SDTV
* 4: DISPMANX_ID_FORCE_LCD
* 5: DISPMANX_ID_FORCE_TV
* 6: DISPMANX_ID_FORCE_OTHER

（译者注：上面0-6分别是不同的显示输出端口，相信很容易看懂，大家探索一下吧。）



