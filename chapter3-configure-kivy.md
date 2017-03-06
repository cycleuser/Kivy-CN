Title: Kivy Configure

Date: 2017-02-6

Category: Kivy

Tags: Python,Kivy





\# Kivy中文编程指南：配置修改



\[英文原文\]\(https://kivy.org/docs/guide/config.html\)



Kivy的配置文件是一个名为config.ini的文本，符合\[标准INI格式\]\(http://en.wikipedia.org/wiki/INI\_file\)。







\#\# 找到配置文件位置



Kivy的配置文件存放在环境变量KIVY\_HOME所制定的位置：



\`\`\`Bash

KIVY\_HOME&gt;/config.ini

\`\`\`





在桌面平台上，默认的位置如下：



\`\`\`Bash

HOME\_DIRECTORY&gt;/.kivy/config.ini

\`\`\`







所以，假设你的用户名是“tito”，在各个操作系统下的配置文件位置则如下所示：



\* Windows: \`C:\Users\tito\.kivy\config.ini\`

\* OS X: \`/Users/tito/.kivy/config.ini\`

\* Linux: \`/home/tito/.kivy/config.ini\`



（译者注：这里要注意，tito只是原文的一个示范，相当于张三李四这样，新手可别照着复制找不到，要用自己操作系统中具体的用户名。）





在Android系统中位置如下：



\`\`\`Bash

ANDROID\_APP\_PATH&gt;/.kivy/config.ini

\`\`\`











假如你的Kivy应用的包名称为“org.kivy.launcher”,那么该Kivy应用的配置文件位于:



\`\`\`Bash

/data/data/org.kivy.launcher/files/.kivy/config.ini

\`\`\`











在iOS上Kivy的默认配置文件位于：



\`\`\`Bash

HOME\_DIRECTORY&gt;/Documents/.kivy/config.ini

\`\`\`



















\#\# 本地配置



有时候用户或者开发者可能需要针对特定的应用来修改配置，或者对Kivy的某个组件进行测试，比如输入模块之类的。这时候就可以用如下命令创建一份新的配置文件：







\`\`\`Python

from kivy.config import Config

Config.read\(file&gt;\)

\# set config

Config.write\(\)

\`\`\`











有时候本地配置只有一个.ini文件还不够用，比如说可能你要单独使用某个garden、Kivy日志或者其他什么模块，这时候就要把KIVY\_HOME这个环境变量进行修改了，指定到目标位置就行：





\`\`\`Python

import os

os.environ\['KIVY\_HOME'\] = folder&gt;

\`\`\`





还有一种思路，就是在运行Kivy应用之前，在终端中手动修改一下这个环境变量：







1. Windows:

	\`set KIVY\_HOME=folder&gt;\`



2. Linux & OSX:

	\`export KIVY\_HOME=folder&gt;\`







在设置了KIVY\_HOME之后，所指定的这个文件夹就会被当做默认的.kivy文件夹来用。











\#\# 详细理解配置项



在\[kivy.config\]\(http://kivy.org/docs/api-kivy.config.html\#module-kivy.config "kivy.config"\) 模块中可以看到全部的配置项的解释。





