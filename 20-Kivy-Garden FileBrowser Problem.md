Title: Kivy-Desinger on Mac ImportError: No module named filebrowser
Date: 2016-12-31
Category: Kivy
Tags: Python,Mac,Kivy



##解决Mac系统上Kivy-Desinger因Garden安装位置不匹配导致的filebrowser无法导入的问题


根据官方文档，首先要


```Bash
kivy -m pip install -U watchdog pygments docutils jedi gitpython six kivy-garden
```



然后
```Bash
garden install filebrowser
```


然后你以为一切都很好，尝试运行Kivy-Designer，你会遇到类似下面的错误提示：

```Bash
[WARNING           ] stderr:     from designer.app import DesignerApp
[WARNING           ] stderr:   File "/Users/cycleuser/kivy-designer/designer/app.py", line 27, in <module>
[WARNING           ] stderr:     from kivy.garden.filebrowser import FileBrowser
[WARNING           ] stderr: ImportError: No module named filebrowser
```



这是因为，garden在Mac OS X下安装的位置是Linux下的 ~/.kivy/garden 目录下，而在Mac OS X下这个位置是无效的，必须要手动复制到 /Applications/Kivy.app/Contents/Resources/.kivy/garden 目录下。

读到这里你应该就能解决问题了，仔细阅读官方文档，虽然在garden安装位置这个bug上并没什么用。





_____

下面的内容是我在去年时候无脑尝试的记录，仅仅作为教训有参考意义而已，不要像我一样蠢。
分割线下面的内容就不用看了，没什么意义~


_____

At first, I ran the commands below to install Kivy on my Mac:
>最开始是安装依赖包，我用的是 brew，官方这么推荐就这么用了哈：

```Bash
brew install sdl2 sdl2_image sdl2_ttf sdl2_mixer gstreamer
pip install -I Cython==0.21.2
USE_OSX_FRAMEWORKS=0 pip install git+https://github.com/kivy/kivy.git@1.9.0
```
After that, I download the Kivy dmg from the official website.
>然后呢，就去官网下载最新的那个 kivy 的 dmg，这个是用来在 Mac 上面建立一个 Kivy 官方给打包好的 Python 虚拟环境，就不用自己折腾了。最下面哪句点击 MakeSymlinks 就是用来建立系统映射的一个脚本，到时候在终端直接输入 kivy 就是运行的 kivy.app 内部的一个 Python2.7了，而不用自己折腾配置了。你看这一句英文我翻译解释出这么多，是因为我觉得身边的小白蛮多，解释清楚点比较好。
>（虽然英文版的也是我写的，但我懒得写的那么细，网上英语资料很多，就让他们自己搜去吧。。。）

```
Download the latest version from http://kivy.org/#download
Double-click to open it
Drag the Kivy.app into your Applications folder
Double click the makesymlinks script.
```

Then I used git to download the kivy-designer.
I thought it would work.
So I typed in the command below following the official guide with hope:
>接着就用 git 下载来 kivy-designer。
>满心开心希望能用了。
>所以就根据官方指南输入下面的命令，满眼星星的期待呢：

```Bash
kivy main.py
```
But I got this error:
>尼玛给老子来了个错误：

```Bash
[WARNING           ] stderr: ImportError: No module named filebrowser
```

Details here:
>细节是这样的：

```Bash
[INFO              ] Logger: Record log in /Applications/Kivy.app/Contents/Resources/.kivy/logs/kivy_15-12-29_18.txt
[INFO              ] Kivy: v1.9.0
[INFO              ] Python: v2.7.10 (default, Jul 14 2015, 19:46:27) 
[GCC 4.2.1 Compatible Apple LLVM 6.0 (clang-600.0.39)]
[INFO              ] Image: Providers: img_tex, img_imageio, img_dds, img_gif, img_sdl2 (img_pil, img_ffpyplayer ignored)
[INFO              ] Factory: 173 symbols loaded
[INFO              ] Text: Provider: sdl2
[INFO              ] OSC: using <multiprocessing> for socket
[INFO              ] Window: Provider: sdl2
[INFO              ] GL: OpenGL version <2.1 INTEL-10.6.33>
[INFO              ] GL: OpenGL vendor <Intel Inc.>
[INFO              ] GL: OpenGL renderer <Intel HD Graphics 4000 OpenGL Engine>
[INFO              ] GL: OpenGL parsed version: 2, 1
[INFO              ] GL: Shading version <1.20>
[INFO              ] GL: Texture max size <16384>
[INFO              ] GL: Texture max units <16>
[INFO              ] Window: auto add sdl2 input provider
[INFO              ] Window: virtual keyboard not allowed, single mode, not docked
[WARNING           ] stderr: Traceback (most recent call last):
[WARNING           ] stderr:   File "main.py", line 2, in <module>
[WARNING           ] stderr:     from designer.app import DesignerApp
[WARNING           ] stderr:   File "/Users/cycleuser/kivy-designer/designer/app.py", line 27, in <module>
[WARNING           ] stderr:     from kivy.garden.filebrowser import FileBrowser
[WARNING           ] stderr: ImportError: No module named filebrowser
```



Yep, I found that the filebrowser was missing.
So I run:
>对呗，filebrowser 没安装是吧，那就安装咯。
>就运行一下安装工具就是了：


```Bash
kivy -m pip install -U watchdog pygments docutils jedi gitpython six kivy-garden
garden install filebrowser
```





If you run：
>当然，最开始其实我运行的是：

```Bash
pip install -U watchdog pygments docutils jedi gitpython six kivy-garden
```
instead of
>而没注意官方告诉的要运行：

```Bash
kivy -m pip install -U watchdog pygments docutils jedi gitpython six kivy-garden
```
you may waste a lot of time...
Because you can still run
>这样就会浪费好多时间。。。
>因为两种方式安装之后，会发现都能运行下面的命令来安装 filebrowser ：

```Bash
garden install filebrowser
```
But the location of garden would be in the user path of "~/.kivy/garden" instead of the right location as below:
>但安装位置是不一样的！！！如果像我那样安装就跑到用户目录下面安装了，而不是安装在正确位置，下面的位置是正确位置：

```Bash
/Applications/Kivy.app/Contents/Resources/.kivy
```

The kivy.app run when you type " kivy main.py " from the location below:
>当运行 kivy.app 的时候，跑的是 kivy.app 路径里面的 Python，也会在这个里面找 filebrowser，如果安装到用户路径去了，当然就找不到了啊：

```Bash
/Applications/Kivy.app/Contents/Resources/.kivy
```

So if you install garden and filebrowser in the home dir, you can copy the garden dir from " ~/.kivy " to " /Applications/Kivy.app/Contents/Resources/.kivy ".
>所以如果你跟我一样安装到用户目录了，就把东西从" ~/.kivy "复制到" /Applications/Kivy.app/Contents/Resources/.kivy "就可以了。

Then try to run "kivy main.py" and everything is OK now.
>然后再运行"kivy main.py"，就发现可以跑界面设计工具了。

Always remember to add "kivy -m " before "pip install"!
I hope that this could help other on similar problems.
>一定要记住，在"pip install"的前面要添加"kivy -m "，这样才能安装到 Kivy.app 的路径内，而不是系统路径中！
>唉，希望能帮助其他遇到类似问题的小伙伴吧。

I hope kivy can give a tip about the location of garden installed so we careless people may save a lot of time.
>其实安装 garden 的时候 kivy 如果能给提示一下安装路径该有多好，这样起码容易注意到这个问题了。
