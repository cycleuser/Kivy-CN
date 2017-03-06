Title: Kivy Installment Tutorial
Date: 2016-12-30
Category: Kivy
Tags: Python,Kivy


# Kivy 中文安装指南

译者的话：
接触Python有一段时间了，之前翻译过[ThinkPython2E](http://blog.cycleuser.org/thinkpython-zhong-wen-ban.html)，我也仍然还是个很菜很弱很入门的外行人。
我接下来翻译的关于Kivy的各种内容，不出意外的话也必将充满了各种低级错误。
如果这些错误有影响到大家阅读理解，提前表示一下歉意。
特别希望大家能把错误的地方指出来，让我学习的同时也及时改正。
我的编程水平很差，然而我热爱计算机这一工具，所以我喜欢做各种探索；
我的英语水平也差，不过我喜欢从英语世界发现有意思的事物并且分享给中文世界的朋友们。
本次翻译将仅仅翻译官方正式版部分的安装指南，每夜版以及更进阶的安装内容，不做翻译。
为什么呢？
一来是我自身水平有限，二来你都折腾每夜版了还不好好学英文还要看别人翻译也太不靠谱了对不对？

以下是对 Kivy 官方网站安装文档部分的翻译，下面三个链接是原文地址：
[Installation on Windows](https://kivy.org/docs/installation/installation-windows.html)
[Installation on Linux](https://kivy.org/docs/installation/installation-linux.html)
[Installation on Mac OS](https://kivy.org/docs/installation/installation-osx.html)

# [Windows系统安装Kivy指南](https://kivy.org/docs/installation/installation-windows.html)

自从1.9.1版本开始，Kivy 官方提供了二进制 [wheels](https://wheel.readthedocs.org/en/latest/) 文件，可以用安装Kivy以及所需的一切依赖包到一个已经安装好的Python环境中，在下文会有讲解。

此外还有每夜版wheels文件，可供用户安装，或者用于将之前安装的Kivy更新到新版本；另外本文的后文中也会讲解如何将Kivy安装到**自定义位置**，而不是安装到默认的site-packages文件夹。


####特别注意

【译者注：这段内容是对官方文档比较忠实的翻译和还原，其中提到的MinGW和Python3.5的兼容问题，翻译者没遇到也没有测试过，因为没有Windows的机器。在Mac和Linux上3.5和3.6都成功安装了Kivy，运行过程也没发现问题。】


目前因为MinGW和Python3.5的兼容问题，在Windows平台上还没有办法通过Python3.5来使用Kivy，至少相当一段时间内是没指望了，更多细节参考[这里的这个issue](http://bugs.python.org/issue4709)。 想要解决这个问题，需要用MSVC来编译的3.5，不过目前还没能实现，所以如果你搞定了MSVC编译的话一定反馈一下。


##必要前提

要使用Kivy，首先就得安装[Python](https://www.python.org/downloads/windows/)。Python有好多版本，你可以同时安装其中的好多个，如果你在其中某一个版本的Python里面要使用Kivy，就要在这个版本里面单独按照一次Kivy，其他版本要使用Kivy需要另外再进行安装，就是说每一次安装Kivy只对一份Python环境有效。




## 安装过程

安装了Python之后，打开命令行工具cmd，然后按照下面的命令来进行Kivy的安装。

1  首先要保证已经安装了最新的pip和wheel：

```Bash
python -m pip install --upgrade pip wheel setuptools
```




2  然后安装必要的依赖包(其中gstreamer大小接近90MB，如果不需要用，就可以跳过不安装这个包:



```Python
python -m pip install docutils pygments pypiwin32 kivy.deps.sdl2 kivy.deps.glew
python -m pip install kivy.deps.gstreamer --extra-index-url https://kivy.org/downloads/packages/simple/
```








3  如果上一步都成功了没什么报错，就可以按照Kivy了:




```Bash
python -m pip install kivy
``` 





4   在环境变量中添加一些路径到PATH来避免遇到[各种issues](https://github.com/kivy/kivy/issues/3957) (在你的python.exe所在的路径下运行下面的命令):




```Bash
set PATH=%PATH%;%cd%\share\sdl2\bin;%cd%\share\glew\bin
```




到现在为止就搞定了，你就可以在这份Python环境中通过import kivy命令来导入和使用Kivy了。



####特别注意

如果你遇到了**permission denied**或者**访问被拒绝**之类的错误提示，你可以试试[以管理员权限来运行命令行工具cmd](https://technet.microsoft.com/en-us/library/cc947813%28v=ws.10%29.aspx)。



# [Linux系统安装Kivy指南](https://kivy.org/docs/installation/installation-linux.html)



## 使用包管理器进行安装
   
本节是给各种发行版用对应的deb或者rpm之类的包进行安装s .deb/.rpm/...



### Ubuntu / Kubuntu / Xubuntu / Lubuntu (13.10 Saucy Salamander以及之后更新的版本)

1  首先要根据你的喜好来选择一个PPA源添加到你的系统里：

（译者注：这里稳定版和每夜版二选一就可以，如果要体验最新特性，可以使用每夜版，但是如果用于长期使用追求稳定，推荐用稳定版，二者千万不要同时添加，避免出现混乱和错误。）

```Bash
sudo add-apt-repository ppa:kivy-team/kivy #稳定版
sudo add-apt-repository ppa:kivy-team/kivy-daily  #每夜版
```


2  然后就要用包管理器来更新一下包列表了：


```Bash
sudo apt-get update
```




3  更新列表完毕之后，如果没有错误，就可以安装了：

```Bash
sudo apt-get install python-kivy #Python2 用这个来安装
sudo apt-get install python3-kivy #Python3 要加一个3  
sudo apt-get install python-kivy-examples #可选的样例代码
``` 





### Debian (8.0 Jessie或者更新的版本)

####特别注意
  Debian 7 Wheezy 已经不支持了，你至少要升级到Debian 8 Jessie 才能安装Kivy。

1  通过Synaptic新立得包管理器把下面的PPA源添加到你的sources.list列表中，手动添加也可以：

* Jessie/Testing:

```Bash
#稳定版:

deb http://ppa.launchpad.net/kivy-team/kivy/ubuntu trusty main

#每夜版:

deb http://ppa.launchpad.net/kivy-team/kivy-daily/ubuntu trusty main 
```


* Sid/Unstable:
 
```Bash
#稳定版:

deb http://ppa.launchpad.net/kivy-team/kivy/ubuntu utopic main 

#每夜版:
deb http://ppa.launchpad.net/kivy-team/kivy-daily/ubuntu utopic main 
```





2  添加了源之后，就是要添加一些GPG key到你的apt keyring里面了，运行下面的命令：

```Bash
#非root用户:
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A863D2D6

#root用户:
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A863D2D6
```

3  然后跟Ubuntu里面那个类似，更新列表完毕之后，如果没有错误，就可以安装了：

```Bash
sudo apt-get update #安装之前一定要先这样更新一下列表
sudo apt-get install python-kivy #Python2 用这个来安装
sudo apt-get install python3-kivy #Python3 要加一个3  
sudo apt-get install python-kivy-examples #可选的样例代码
``` 




###Mint/Bodhi/Suse/Gentoo  这部分省略
Mint的安装基本与对应的Ubuntu版本一样，Bodhi估计用的人不多我懒得翻译了，OpenSuse/Gentoo 的用户应该比较有经验了还用得着我翻译么？

### Fedora

1  在终端里添加repo(注意版本不要弄错):

```Bash
#Fedora 18:
sudo yum-config-manager  --add-repo=http://download.opensuse.org\
/repositories/home:/thopiekar:/kivy/Fedora_18/home:thopiekar:kivy.repo

#Fedora 17:
sudo yum-config-manager --add-repo=http://download.opensuse.org\
/repositories/home:/thopiekar:/kivy/Fedora_17/home:thopiekar:kivy.repo

#Fedora 16:
sudo yum-config-manager --add-repo=http://download.opensuse.org\
/repositories/home:/thopiekar:/kivy/Fedora_16/home:thopiekar:kivy.repo
```


2  跟Ubuntu和Debian里面一样，添加源之后还是要用包管理器更新一下包列表。

3  更新列表之后通过包管理器就可以安装**python-Kivy**和**python-Kivy-examples**了。


# 在虚拟环境中安装



## 必备的依赖包



### Cython

一定要注意，这里超级重要，不同版本的Kivy只能用特定版本的Cython配合才能使用，二者一定要匹配，关系如下表：

| Kivy | Cython |
| --- | --- |
| 1.8 | 0.20.2 |
| 1.9 | 0.21.2 |
| 1.9.1 | 0.23 |







## SDL2框架相关的依赖包



### 以Ubuntu为例

在下面的命令样例中，用**python**和**python-dev**就表明是给**Python2**安装，而**python3**和**python3-dev**是针对**Python3**的。

```Bash
# 先要安装这些必备的组件，注意上面刚提示过，如果要用Python3，在python后面加上一个3就可以了:
sudo apt-get install -y \
python-pip \
build-essential \
git \
python \
python-dev \
ffmpeg \
libsdl2-dev \
libsdl2-image-dev \
libsdl2-mixer-dev \
libsdl2-ttf-dev \
libportmidi-dev \
libswscale-dev \
libavformat-dev \
libavcodec-dev \
zlib1g-dev
```









####特别注意

在某些特定的Linux版本上，你可能会收到一个与ffmpeg包有关的错误信息。这种情况下，就可以用libav-tools替换掉上文命令中的ffmpeg，另一种解决方法是使用ppa来安装ffmpeg，使用的命令如下所示：


```Bash
sudo add-apt-repository ppa:mc3man/trusty-media
sudo apt-get update
sudo apt-get install ffmpeg
```








### 要在虚拟环境中安装Kivy了




首先要确保Pip, Virtualenv 和 Setuptools 这几个包都更新到最新：

```Bash
sudo pip install --upgrade pip virtualenv setuptools
```

然后创建一个名为"kivyinstall"的新的虚拟环境，这时候你有两种选择：

* 第一种是用系统自带的默认Python解释器：

```Bash
virtualenv --no-site-packages kivyinstall
```

* 第二种是设定一个指定位置的Python解释器，这里举例就假设要用的解释器路径在 /usr/bin/python2.7

```Bash
virtualenv --no-site-packages -p /usr/bin/python2.7 kivyinstall
```

建立好虚拟环境之后，就是进入里面了：

```Bash
. kivyinstall/bin/activate
```

千万要注意，这里要安装正确的Cython版本：

```Bash
pip install Cython==0.23
```

然后就可以在这个虚拟环境里面安装Kivy的稳定版了：

```Bash
pip install kivy
```

如果要用开发版本的Kivy，就换成下面的命令来安装：

```Bash
pip install git+https://github.com/kivy/kivy.git@master
```











## 古老的PyGame相关的依赖包



### Ubuntu 系统

首先还是要安装一大堆必要的系统组件了：


```Bash
sudo apt-get install -y \
python-pip \
build-essential \
mercurial \
git \
python \
python-dev \
ffmpeg \
libsdl-image1.2-dev \
libsdl-mixer1.2-dev \
libsdl-ttf2.0-dev \
libsmpeg-dev \
libsdl1.2-dev \
libportmidi-dev \
libswscale-dev \
libavformat-dev \
libavcodec-dev \
zlib1g-dev
```



### Fedora 系统

Fedora 系统就要用yum来实现组件安装：


```Bash
sudo yum install \
make \
mercurial \
automake \
gcc \
gcc-c++ \
SDL_ttf-devel \
SDL_mixer-devel \
khrplatform-devel \
mesa-libGLES \
mesa-libGLES-devel \
gstreamer-plugins-good \
gstreamer \
gstreamer-python \
mtdev-devel \
python-devel \
python-pip
```



### OpenSuse 系统
这里示范的是用zypper作为包管理器：

```Bash
sudo zypper install \
python-distutils-extra \
python-gstreamer-0_10 \
python-enchant \
gstreamer-0_10-plugins-good \
python-devel \
Mesa-devel \
python-pip

sudo zypper install -t pattern devel_C_C++
```










### 然后又到了在虚拟环境中安装Kivy的时候了


首先要确保Pip, Virtualenv 和 Setuptools 这几个包都更新到最新：


```Bash
sudo pip install --upgrade pip virtualenv setuptools
```

然后创建一个名为"kivyinstall"的新的虚拟环境，这时候你有两种选择：

* 第一种是用系统自带的默认Python解释器：

```Bash
virtualenv --no-site-packages kivyinstall
```

* 第二种是设定一个指定位置的Python解释器，这里举例就假设要用的解释器路径在 /usr/bin/python2.7

```Bash
virtualenv --no-site-packages -p /usr/bin/python2.7 kivyinstall
```

建立好虚拟环境之后，就是进入里面了：

```Bash
. kivyinstall/bin/activate
```

千万要注意，这里要安装numpy以及正确的Cython版本：

```Bash
pip install numpy
pip install Cython==0.23
```

这里注意，如果你不想用sdl2，而想要用pygame，你可以通过export USE_SDL2=0来强制使用pygame。这样一来，Kivy在安装过程中找不到sdl2的链接，就会自动设置这个值为0，然后尝试用pygame来构建。

```Bash
pip install hg+http://bitbucket.org/pygame/pygame
```

接下来就可以在这个虚拟环境里面安装Kivy的稳定版了：

```Bash
pip install kivy
```

如果要用开发版本的Kivy，就换成下面的命令来安装：

```Bash
pip install git+https://github.com/kivy/kivy.git@master
```










### 虚拟环境中安装额外的包





在该虚拟环境中安装开发版的buildozer：

```Bash
pip install git+https://github.com/kivy/buildozer.git@master
```

安装开发版plyer

```Bash
pip install git+https://github.com/kivy/plyer.git@master
```

其他的两个可能用到的包：

```Bash
pip install -U pygments docutils
```











## 从命令行中启动

Kivy官方提供的样例代码中有一些是可以在安装配置好Kivy环境后立即就能运行的，这些例子就集成在Kivy包之内。所以，如果你要尝试这些样例，你得实现确定好easy_install把你当前在用的Kivy安装到了哪里：


```Bash
python -c "import pkg_resources; print(pkg_resources.resource_filename('kivy', '../share/kivy-examples'))"
```

然后估计你会得到一个路径了，类似下面这样：
（译者注：这个路径是根据上面那个命令来输出的，每个人不同配置都产生不同结果，千万别无脑复制哦！）

```Bash
/usr/local/lib/python2.6/dist-packages/Kivy-1.0.4_beta-py2.6-linux-x86_64.egg/share/kivy-examples/
```



然后你知道位置了，就进入这个路径，然后运行一下样例吧。
比如你可以尝试一下触控追踪的样例touchtracer：

```Bash
cd <path to kivy-examples #把这里替换成你自己的kivy-examples目录
cd demo/touchtracer
python main.py
```


这还有一个图片示意程序pictures：

```Bash
cd <path to kivy-examples #把尖括号内容替换成你自己的kivy-examples目录
cd demo/pictures
python main.py
```






If you are familiar with Unix and symbolic links, you can create a link directly in your home directory for easier access. For example:

如果你对Unix和符号链接比较熟悉，你可以把这个目录在你的home目录里面创建一个链接，这样以后访问更方便，举例如下：


1  通过上面演示过的命令获取样例代码所在位置；

2  把获取的路径补齐到下列命令中，然后粘贴到终端中：




```Bash
ln -s <path to kivy-examples ~/#把尖括号内容替换成你自己的kivy-examples目录
cd ~/kivy-examples 
```




3  接下来你就可以用如下这种特别简单的方式来访问样例代码了：




```Bash
cd ~/kivy-examples
```


如果你想更省事，把Kivy程序当做常规脚本来运行（比如输入./main.py），或者双击来运行，你就需要创建一个正确的Python链接。例如下面这样：

```Bash
sudo ln -s /usr/bin/python2.7 /usr/bin/kivy
```



或者如果你想要在某个虚拟环境中运行Kivy，那给对应该环境的Python做个链接就行了：

```Bash
sudo ln -s /home/your_username/Envs/kivy/bin/python2.7 /usr/bin/kivy
```


还没完，接下来你还要在每一个main.py的开头添加如下内容作为第一行：



```Bash
#!/usr/bin/kivy
```



####特别注意

一定要小心哈，Windows系统下的Python保存的文件结尾类型很可能是(CR-LF)，Linux系统不会忽略掉其中的<CR，并且依然当做文件名字的一部分来读取。这就会导致很多乱七八糟的出错信息，所以记得先确定把文件转换成Unix风格的结尾。





## 设备权限


当你启动app的时候，Kivy会用到[Mtdev](http://wiki.ubuntu.com/Multitouch)来搜索是否有可用的多指触摸设备，如果找到就拿来用作输入。然而这类设备的使用权通常都被严格限制到了特定的用户或者用户组。

如果你的用户没有这些权限，Kivy就会记下一个错误，然后给出一个与这些设备相关的警告，大概如下所示：

```Bash
Permission denied:'/dev/input/eventX'
```

所以你要使用这些信息，就必须赋予当前用户或者用户组所必要的权限。可以通过如下命令实现：

```Bash
sudo chmod u+r /dev/input/eventX
```


上面这个是给当前用户赋予权限，如果要给当前用户组权限，可以用下面这个命令实现：


```Bash
sudo chmod g+r /dev/input/eventX
```



这个授权是非永久性的，这次授权后可用，以后又要重新授权才能用。所以有个更好的永久解决方案，就是把当前用户添加到有权限的用户组中。例如，在Ubuntu系统里面，你可以把这个用户添加到input这个用户组：

```Bash
sudo adduser $USER input
```


####特别注意

修改完用户权限之后，你要注销然后再登录才能使用这些权限。






# [Mac系统安装Kivy指南](https://kivy.org/docs/installation/installation-osx.html)



## 使用官方提供的Kivy.app


####特别注意



官方提供的Kivy.app仅仅适用于OS X 10.7以及更新版本的系统（都是64位的）。对10.7之前的系统以及10.7的32位系统的用户，你就只能自己手动安装各种组件了。建议通过 [homebrew](http://brew.sh/) 来安装。



对OS X 10.7 64位版本以及更新版本系统，Kivy官方提供了一个Kivy.app的包，里面集成好了所有需要的依赖包。可以从[这个链接](http://kivy.org/#download)来下载压缩包，解压缩之后就能发现一个名为**Kivy.app**的应用文件。
 
 

要怎么安装呢？具体思路如下：

 
 
 1  从[官网的这个链接](http://kivy.org/#download)下载压缩包，其中Kivy2.7z用的是Python 2，而Kivy3.7z用的是Python 3。
 
 
 2  使用解压缩工具把压缩包进行解压，可以试试[Keka](http://www.kekaosx.com/)这个应用。
 
 
 3  把解压缩出来的Kivy2.app或者Kivy3.app这两个文件当中选择一个，重命名成Kivy.app，复制到应用程序目录/Applications 下，这个过程可以在终端中通过下面的命令来实现
 
```Bash
sudo mv Kivy2.app /Applications/Kivy.app
``` 

 
 4   然后是创建一个名为kivy的系统链接，以便于方便访问kivy环境来启动app：
  
```Bash
ln -s /Applications/Kivy.app/Contents/Resources/script /usr/local/bin/kivy
``` 
  
 
 5   样例代码以及所有常规的Kivy工具都可以在/Applications/Kivy.app/Contents/Resources/kivy 这个目录里面找到了。


 6   译者注：你完全可以同时拥有Kivy2.app和Kivy3.app，可以不重命名他们，而直接把这两个都复制到/Applications/下，然后用如下方式分别创建名为kivy2和kivy3的链接（这样以后你就可以通过kivy2来使用Python2版本的Kivy，而用kivy3来使用Python3版本的Kivy了。）：

```Bash
ln -s /Applications/Kivy2.app/Contents/Resources/script /usr/local/bin/kivy2
ln -s /Applications/Kivy3.app/Contents/Resources/script /usr/local/bin/kivy3
``` 
 
 

现在你就可以在终端中用Kivy脚本文件来启动Kivy的app了，也可以把你的main.py直接拽到终端中就能运行了。


### 安装模块

OS X上的Kivy使用自己集成的一个python环境，只在你用kivy命令的时候才被激活。所以要在这里安装模块，要在pip命令前面加上kivy -m的前缀，如下所示（记得把<modulename>替换成你要安装的模块名）：

```Bash
kivy -m pip install <modulename>
```




### 这些模块安装到哪里了呢？


安装位置在Kivy.app目录内的venv目录下：

```Bash
Kivy.app/Contents/Resources/venv/
```


如果你安装一个二进制的模块，例如kivy-garden，这些二进制文件就只能在venv一级以上的目录使用：


```Bash
kivy -m pip install kivy-garden
```


上面这个命令安装的garden的链接库文件，只有通过如下命令激活这个虚拟环境了才能使用：


```Bash
source /Applications/Kivy.app/Contents/Resources/venv/bin/activate
garden install mapview
deactivate
```


### 二进制文件安装
直接复制到/Applications/Kivy.app/Contents/Resources/venv/bin/这个目录就行了。





### 安装其他框架

Kivy.app自带了SDL2和Gstreamer这两个框架。要增加其他的框架让Kivy使用，可以按照如下思路实现：

```Bash
git clone http://github.com/tito/osxrelocator
export PYTHONPATH=~/path/to/osxrelocator
cd /Applications/Kivy.app
python -m osxrelocator -r . /Library/Frameworks/<Framework_name>.framework/ \
@executable_path/../Frameworks/<Framework_name>.framework/
```


一定要记得把<Framework_name>替换成你需要的框架名。osxrelocator这个工具是用来改变框架中的链接库目录，这样就可以让这些框架可以在Kivy.app中使用了。






### 启动任意一个Kivy应用


要运行Kivy应用，只要把源码拖拽到Kivy.app图标上，就可以了。样例代码目录中的任何Python文件都可以拿来试试。





### 从命令行启动


如果要在命令行中运行Kivy，把Kivy.app复制到应用目录后，双击Make Symlinks script 这个脚本文件，就可以了。要测试是否成功，可以按照如下方式：
 
 
 1  打开终端，输入：
 
```Bash
kivy
```
 
你就应该能得到一个Python解释器环境了。
 
 
 2  然后在这个Python解释器内输入如下代码：
 
```Python
import kivy
```
 
 
如果什么反应都没有，没有出错，那就说明搞定了。ut errors, it worked.
 
 
 3  经过上面的验证，说明配置成功了。这样在命令行终端中运行Kivy应用就很简单了，只是执行一下脚本就可以，如下所示：
 
```Bash
kivy yourapplication.py
``` 
 
 


## 使用HomeBrew安装Kivy


使用HomeBrew和Pip也可以安装Kivy，具体步骤如下所示：
 
 
1  首先要先安装[homebrew](http://brew.sh/)，然后安装必备组件：

```Bash
brew install sdl2 sdl2_image sdl2_ttf sdl2_mixer gstreamer
``` 
 

 
2  然后是通过pip安装cython 0.23和kivy（一定要注意，要确保设置环境变量USE_OSX_FRAMEWORKS=0）：
 
```Bash
pip install -I Cython==0.23
USE_OSX_FRAMEWORKS=0 pip install kivy
``` 
 
 
 
 3  如果不想安装稳定版而想使用开发版，第二步就要改用如下命令了： 
 
```Bash
USE_OSX_FRAMEWORKS=0 pip install https://github.com/kivy/kivy/archive/master.zip
``` 
 
 
 
 
 
 
 
 





## 使用MacPorts和Pip来进行安装



####特别注意

如果你希望自己的Kivy应用能够支持视频播放，就得手动安装gstreamer。可以供过MacPorts来安装[py-gst-python port](https://trac.macports.org/ticket/44813). 用MacPorts和Pip安装Kivy的过程如下：

 
 
 1  安装[Macports](https://www.macports.org/)
 
 
 2  安装Python 3.4并且设定成默认的：
 
 
```Bash
port install python34
port select --set python python34
```
 
 
 
 
 
 
 3  然后安装Pip并设置为默认：
  
```Bash
port install pip-34
port select --set pip pip-34
```
 
 

 
 
 4   使用[Macports](https://www.macports.org/)安装必备组件：
 
 
 
```Bash
port install libsdl2 libsdl2_image libsdl2_ttf libsdl2_mixer
``` 
 
 
 
 
 
 
 5   使用Pip安装cython 0.23和kivy（一定要注意，要确保设置环境变量USE_OSX_FRAMEWORKS=0）:
 
 
 
```Bash
pip install -I Cython==0.23
USE_OSX_FRAMEWORKS=0 pip install kivy
```
 
 
 
 
 
 
 
 
 6   如果不想安装稳定版而想使用开发版，第二步就要改用如下命令了：

```Bash
USE_OSX_FRAMEWORKS=0 pip install https://github.com/kivy/kivy/archive/master.zip
``` 


####特别注意

如果你在Mac系统下使用Kivy-Designer的时候遇到如下错误：

```language
[WARNING           ] stderr:     from designer.app import DesignerApp
[WARNING           ] stderr:   File "/Users/cycleuser/kivy-designer/designer/app.py", line 27, in <module>
[WARNING           ] stderr:     from kivy.garden.filebrowser import FileBrowser
[WARNING           ] stderr: ImportError: No module named filebrowser
```

那么不要犹豫，肯定是Garden的安装位置你没有调整，你需要参考我的[这篇文章](http://blog.cycleuser.org/kivy-on-mac-importerror-no-module-named-filebrowser.html)来解决这个问题。
