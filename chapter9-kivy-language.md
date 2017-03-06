Title: Kivy language

Date: 2017-02-23

Category: Kivy

Tags: Python,Kivy





\# Kivy中文编程指南：KV 语言





\[英文原文\]\(https://kivy.org/docs/guide/lang.html\)







\#\# 语言背后的概念







随着你的应用程序越写越复杂，就往往会发现控件树的结构/各种绑定的声明等等，都越来越繁琐复杂了，维护起来也很费力气。KV 语言就是为了解决这个问题而设计出来的。



（译者注：这种情况在 GUI 界面的 APP 开发中很常见，比如在 Android 开发的过程中，就用到了 xml 来定义界面元素的关系等等。）



KV 语言（英文缩写也叫 kvlang 或者 kivy 语言），可以让开发者用描述的方式来创建控件树，以及绑定控件对应的属性，以实现一种自然地调用。这一设计可以允许用户能够快速建立应用雏形，然后对界面进行灵活调整。此外，这样的设计还使得运行逻辑与用户界面相互分离不干扰。



\#\# 载入 KV 的方法







通过以下两种方法都可以在你的应用程序中载入 KV 代码：







\* 通过同名文件查找：

	Kivy 会找跟 App 类同名的小写字母的 Kv 扩展名的文件，如果你的应用类尾部有 App 字样，查找的时候会找去掉这个 App 字样的文件，例如：

    MyApp -&gt; my.kv

    如果这个文件定义了一个根控件，这个文件就会被添加到应用的根属性中去，然后用作整个程序的控件树基础。



\* \`Builder\`: 也可以直接指定让 Kivy 去加载某个字符串或者文件。如果这个字符串或者文件定义了一个根控件，就会被下面这个方法返回：

    \`Builder.load\_file\('path/to/file.kv'\)\`或者\`Builder.load\_string\(kv\_string\)\`







\#\# 规则语义







Kv 源文件包含有各种规则，这些规则是用来描述控件的环境设定的，可以有一个根规则，然后其他的各种类的或者模板的规则就都不限制数量来。



根规则是用来描述你的根控件类的，不能有任何缩进，跟着一个英文冒号:，在应用程序的实例当中这就会被设置成根属性：



\`Widget:\`



一类规则，声明方式为将控件类的名字用尖括号括起来的，然后跟着一个英文冒号:，这类规则用来定义这个类的实例图形化呈现的方式：



\`&lt;MyWidget&gt;:\`





Kv 文件中各种规则都用缩进来进行区块划分，就像 Python 里面一样，这些缩进得是四个空格作为一层缩进，就跟 Python 里面推荐的做法是一样的。





以下是三个 Kv 语言的关键词：

\* app: 指向你应用程序的实例。

\* root: 指向当前规则中的基础控件或者基础模板。

\* self: 指向当前的控件。









\#\# 特殊语法





有两种特殊的语法，能定义整个 Kv 环境下的各种值：



读取 Kv 中的 Python 模块和各种类：



\`\`\`Python

\#:import name x.y.z

\#:import isdir os.path.isdir

\#:import np numpy

\`\`\`



等价于 Python 中的：



\`\`\`Python

from x.y import z as name

from os.path import isdir

import numpy as np

\`\`\`



设置各种全局变量：



\`\`\`Python

\#:set name value

\`\`\`



等价于 Python 中的：



\`\`\`Python

name = value

\`\`\`







\#\# 子对象实例化





To declare the widget has a child widget, instance of some class, just declare this child inside the rule:



给一个控件声明子控件，比如某个类的实例，只要在规则内部声明一下这个子对象就可以类：





\`\`\`Python

MyRootWidget:

    BoxLayout:

        Button:

        Button:

\`\`\`









上面的样例代码定义了根控件，是一个 MyRootWidget 的实例，它有一个子控件，是一个 \[\`BoxLayout\`\]\(https://kivy.org/docs/api-kivy.uix.boxlayout.html\) 实例。这个 \[\`BoxLayout\`\]\(https://kivy.org/docs/api-kivy.uix.boxlayout.html\) 还有自己的两个子对向，是两个 \[\`Button\`\]\(https://kivy.org/docs/api-kivy.uix.button.html\) 类的实例。





与上面代码等价的 Python 代码大致如下：







\`\`\`Python

root = MyRootWidget\(\)

box = BoxLayout\(\)

box.add\_widget\(Button\(\)\)

box.add\_widget\(Button\(\)\)

root.add\_widget\(box\)

\`\`\`



你可能会发现直接用 Python 来实现的代码不那么好阅读，写起来也不那么简便。





用 Python 可以在创建控件的时候传递关键词参数过去，来指定这些控件的行为。例如下面的这个代码就是设定一个 \[\`gridlayout\`\]\(https://kivy.org/docs/api-kivy.uix.gridlayout.html\) 中的栏目数：





\`\`\`Python

grid = GridLayout\(cols=3\)

\`\`\`





同样目的也可以用 Kv 来实现，可以直接在规则内指定好子控件的属性：







\`\`\`Python

GridLayout:

    cols: 3

\`\`\`





这个值会作为一个 Python 表达式来进行计算，然后所有在表达式中用到的属性都是可见的，就好比下面的 Python 代码一样（这里假设 self 是一个有 \`ListProperty\` 数据的控件）：





\`\`\`Python

grid = GridLayout\(cols=len\(self.data\)\)

self.bind\(data=grid.setter\('cols'\)\)

\`\`\`



如果想要在数据修改的时候就更新显示，可以用如下方法实现：



\`\`\`Python

GridLayout:

    cols: len\(root.data\)

\`\`\`

\#\#\#\# 特别注意







控件的名字一定要用大写字母打头，而属性的名字一定要用小写的。推荐遵循\[PEP8 命名惯例\]\(https://www.python.org/dev/peps/pep-0008/\#naming-conventions\)。







\#\# 事件绑定





在 Kv 中，使用英文冒号 ":" 就可以来进行事件绑定，也就是将某个回调和一个事件联系起来：



\`\`\`Python

Widget:

    on\_size: my\_callback\(\)

\`\`\`





使用 args 关键词的信号，就能把分派来的值传递过去类：



\`\`\`Python

TextInput:

    on\_text: app.search\(args\[1\]\)

\`\`\`



还可以用更加复杂的表达式，例如：





\`\`\`Python

pos: self.center\_x - self.texture\_size\[0\] / 2., self.center\_y - self.texture\_size\[1\] / 2.

\`\`\`



上面这段表达式中，监听了\`center\_x\`, \`center\_y\`, \`texture\_size\`这三个属性的变化。只要其中有一个发生了变化，表达式就会重新计算来更新 \`pos\` 的区域。



你还看以在 kv 语言中处理  \`on\_\` 事件。例如 TextInput 这个类就有一个 \`focus\` 属性，这个数行自动生成的 \`on\_focus\` 事件可在 kv 语言内进行读取：





\`\`\`Python

TextInput:

    on\_focus: print\(args\)

\`\`\`



\#\# 扩展画布



Kv 语言也已用来定义你控件的画布，如下所示：





\`\`\`Python

MyWidget:

    canvas:

        Color:

            rgba: 1, .3, .8, .5

        Line:

            points: zip\(self.data.x, self.data.y\)

\`\`\`



当属性发生变化的时候，这些画布就会更新。当然也可以用 canvas.before 和 canvas.after。



\#\# 定位控件



在一个控件树当中，经常会需要去读取或者定位其他的控件。 Kv 语言提供了一种快速的方法来实现这一目的，就是使用 id。（译者注：在 Android 的开发中就是这样的。）

可以把这些控件当作是类这以层次的变量，只能在 Kv 语言中使用。例如下面的：





\`\`\`Python

&lt;MyFirstWidget&gt;:

    Button:

        id: f\_but

    TextInput:

        text: f\_but.state



&lt;MySecondWidget&gt;:

    Button:

        id: s\_but

    TextInput:

        text: s\_but.state

\`\`\`



An \`id\` is limited in scope to the rule it is declared in, so in the code above \`s\_but\` can not be accessed outside the \`&lt;MySecondWidget&gt;\` rule.



\`id\` 只能再所处的规则内使用，也就是声明它的位置，所以在上面的代码中，\`s\_but\` 就不能在 \`&lt;MySecondWidget&gt;\` 规则外被读取到。





\#\#\#\# 特别注意





给一个 \`id\` 赋值的时候，一定要记住这个值不能是字符串。所以不能有引号：这是正确的 -&gt; \`id: value\`, 这样就不对 -&gt; \`id: 'value'\`

\`id\` 是对空间的一个 \`weakref\` （弱引用），而不是控件本身。所以，在垃圾回收的时候要保存控件，就不能仅仅保存 \`id\`。



下面的代码中：



\`\`\`Python

&lt;MyWidget&gt;:

    label\_widget: label\_widget

    Button:

        text: 'Add Button'

        on\_press: root.add\_widget\(label\_widget\)

    Button:

        text: 'Remove Button'

        on\_press: root.remove\_widget\(label\_widget\)

    Label:

        id: label\_widget

        text: 'widget'

\`\`\`



虽然 \`MyWidget\` 中已经存储了一个对 \`label\_widget\` 的引用，但是这个只是一个弱引用，其他引用被移除的时候还不足以保证对象依然可用。因此，在移除按钮被点击（这时候也就是移除类所有对这个控件的引用）之后，或者窗口大小被调整了（这会调用垃圾回收器，导致删掉 \`label\_widget\`），这时候如果点击添加按钮来重新把控件增加回来的话，就会有一个引用错误\(\`ReferenceError\`\)被抛出来：因为弱引用的对象已经不存在类。





\`\`\`Python

&lt;MyWidget&gt;:

    label\_widget: label\_widget.\_\_self\_\_

\`\`\`



\#\# Python 代码读取在 Kv 中定义的控件



假如在 my.kv 文件中有如下的代码：



\`\`\`Python

&lt;MyFirstWidget&gt;::

    \# both these variables can be the same name and this doesn't lead to

    \# an issue with uniqueness as the id is only accessible in kv.

    txt\_inpt: txt\_inpt

    Button:

        id: f\_but

    TextInput:

        id: txt\_inpt

        text: f\_but.state

        on\_text: root.check\_status\(f\_but\)

\`\`\`



在 myapp.py 这个文件中：





\`\`\`Python

...

class MyFirstWidget\(BoxLayout\):



    txt\_inpt = ObjectProperty\(None\)



    def check\_status\(self, btn\):

        print\('button state is: {state}'.format\(state=btn.state\)\)

        print\('text input text is: {txt}'.format\(txt=self.txt\_inpt\)\)

...

\`\`\`



txt\_inpt 是一个\[\`ObjectProperty\`\]\(https://kivy.org/docs/api-kivy.properties.html\#kivy.properties.ObjectProperty "kivy.properties.ObjectProperty"\)对象，在类内被初始化为 None。



\`\`\`Python

txt\_inpt = ObjectProperty\(None\)

\`\`\`





目前位置，这个 self.txt\_inpt 还是 None。在 Kv 语言中，这个属性会进行更新，保存 txt\_input 这个 id 所引用的 \`TextInput\` 实例：





\`\`\`Python

txt\_inpt: txt\_inpt

\`\`\`



从这以后，self.txt\_inpt 久保存了一个到控件的引用，通过 txt\_input 这个 id 来识别，可以在类内的各个地方来使用，就跟在 check\_status 函数里一样。当然也可以不这么做，可以把 id 传给需要用到它的函数，例如上面代码中那个 f\_but 这个例子。



通过 id 标签，在 kv 语言中可以查找对象，这是一种更简单的读取对象的方法。比如下面这段代码：





\`\`\`Python

&lt;Marvel&gt;

  Label:

    id: loki

    text: 'loki: I AM YOUR GOD!'

  Button:

    id: hulk

    text: "press to smash loki"

    on\_release: root.hulk\_smash\(\)

\`\`\`





在你的 Python 代码中：

\`\`\`Python

class Marvel\(BoxLayout\):

    def hulk\_smash\(self\):

        self.ids.hulk.text = "hulk: puny god!"

        self.ids\["loki"\].text = "loki: &gt;\_  \# alternative syntax

\`\`\`



当你的 kv 文件被解析的时候，kivy 会选中所有带有标签 id 的控件，然后把它们放到 self.ids 这样一个辞典类型的属性里面去。所以你就可以对这些控件进行遍历，然后像是辞典数据一样来进行读取类：





\`\`\`Python

for key, val in self.ids.items\(\):

    print\("key={0}, val={1}".format\(key, val\)\)

\`\`\`



\#\#\#\# 特别注意



虽然这种 self.ids 方法非常简便，但通常最推荐的还是用对象属性。这样会创建一个直接的引用，能提供更快地读取速度，并且也更加准确可靠。



\#\# 动态类型



参考下面的代码：

\`\`\`Python

&lt;MyWidget&gt;:

    Button:

        text: "Hello world, watch this text wrap inside the button"

        text\_size: self.size

        font\_size: '25sp'

        markup: True

    Button:

        text: "Even absolute is relative to itself"

        text\_size: self.size

        font\_size: '25sp'

        markup: True

    Button:

        text: "Repeating the same thing over and over in a comp = fail"

        text\_size: self.size

        font\_size: '25sp'

        markup: True

    Button:

\`\`\`



如果这里使用一个模板，就不用那么麻烦地去重复对每一个按钮进行设置了，例如下面这样就可以了：



\`\`\`Python

&lt;MyBigButt@Button&gt;:

    text\_size: self.size

    font\_size: '25sp'

    markup: True



&lt;MyWidget&gt;:

    MyBigButt:

        text: "Hello world, watch this text wrap inside the button"

    MyBigButt:

        text: "Even absolute is relative to itself"

    MyBigButt:

        text: "repeating the same thing over and over in a comp = fail"

    MyBigButt:

\`\`\`



上面这个类是在这个规则的声明内建立的，继承了按钮类 Button，然后我们就可以用这个类来对默认值进行修改，并且建立所有实例的链接绑定，而不用在 Python 弄一大堆新代码了。



\#\# 在多个控件中复用样式



参考下面的代码，在 my.kv 文件中：

\`\`\`Python

&lt;MyFirstWidget&gt;:

    Button:

        on\_press: root.text\(txt\_inpt.text\)

    TextInput:

        id: txt\_inpt



&lt;MySecondWidget&gt;:

    Button:

        on\_press: root.text\(txt\_inpt.text\)

    TextInput:

        id: txt\_inpt

\`\`\`





在 myapp.py 这个文件中：



\`\`\`Python

class MyFirstWidget\(BoxLayout\):

    def text\(self, val\):

        print\('text input text is: {txt}'.format\(txt=val\)\)



class MySecondWidget\(BoxLayout\):

    writing = StringProperty\(''\)

    def text\(self, val\):

        self.writing = val

\`\`\`



好多类都要用到同样的 .kv 样式文件，这样就可以通过让所有控件都对样式进行复用，就能简化一下设计。这就可以在 kv文件中来实现。例如下面这个就是 my.kv 文件中的代码：





\`\`\`Python

&lt;MyFirstWidget,MySecondWidget&gt;:

    Button:

        on\_press: self.text\(txt\_inpt.text\)

    TextInput:

        id: txt\_inpt

\`\`\`

只要把各个类的名字用英文冒号:分隔开，声明当中包含的类就都会使用相同的 kv 属性了。



\#\# 使用 Kivy 语言来设计

Kivy 语言的设计目标之一就是希望能够\[做好分工\]\(https://en.wikipedia.org/wiki/Separation\_of\_concerns\)，把界面和内部逻辑相互分离。输出的样式由你的 kv 文件来确定，运行逻辑靠你的 python 代码来执行。



\#\#\# Python 文件中的代码



来一个简单的小例子。首先要有一个名字为 main.py 的 Python 源文件：



\`\`\`Python

import kivy

kivy.require\('1.0.5'\)



from kivy.uix.floatlayout import FloatLayout

from kivy.app import App

from kivy.properties import ObjectProperty, StringProperty



class Controller\(FloatLayout\):

'''

创建一个控制器，从 kv 文件中接收一个定制的控件。

增加一个由 kv 文件进行调用的动作。

'''

    label\_wid = ObjectProperty\(\)

    info = StringProperty\(\)

    def do\_action\(self\):

        self.label\_wid.text = 'My label after button press'

        self.info = 'New info text'



class ControllerApp\(App\):

    def build\(self\):

        return Controller\(info='Hello world'\)



if \_\_name\_\_ == '\_\_main\_\_':

    ControllerApp\(\).run\(\)

\`\`\`



刚刚这个代码样例中，我们创建了一个有两个属性的控制器：



\* \`info\` 该属性用于接收文本

\* \`label\_wid\` 该属性用于接收文本标签控件



此外还创建了一个 \`do\_action\(\)\` 方法，这个方法会使用上面的属性。这个方法会改变 \`info\`里面的文本，以及 \`label\_wid\` 控件中的文本。



\#\#\# controller.kv 文件中的布局样式



没有对应的 kv 文件，应用程序也能运行，只不过是屏幕上不会有任何显示输出。这个是符合情理的，因为毕竟控制器 \`Controller\` 这个类是没有任何控件的，就只是一个\`FloatLayout\`（流动输出？）咱们可以创建一个名字为 controller.kv 的文件，来围绕着这个控制器类 \`Controller\` 搭建 UI （用户界面），这个文件会在运行 \`ControllerApp\` 的时候被加载。这个具体怎么实现的，以及加载类哪些文件，可以参考 \[\`kivy.app.App.load\_kv\(\)\`\]\(https://kivy.org/docs/api-kivy.app.html\#kivy.app.App.load\_kv "kivy.app.App.load\_kv"\) 方法里的描述。





\`\`\`Python

\#:kivy 1.0

&lt;Controller&gt;:

    label\_wid: my\_custom\_label



    BoxLayout:

        orientation: 'vertical'

        padding: 20



        Button:

            text: 'My controller info is: ' + root.info

            on\_press: root.do\_action\(\)



        Label:

            id: my\_custom\_label

            text: 'My label before button press'

\`\`\`







上面的代码中就是一个竖直的箱式布局（BoxLayout）。看着就挺简单的。这个代码主要功能有以下三个：



1 使用来自控制器类 Controller 的数据。只要 controller 中的 info 属性发生了改变，表达式 'My controller info is: ' + root.info 就会自动重新计算，改变按钮（Button\)上面的值。



2 传递数据给控制器类 Controller。my\_custom\_label 这个 id 会赋值给 id 为 my\_custom\_label 的新建文本标签（Label）。此后，使用 label\_wid:my\_custom\_label 中的 my\_custom\_label 就能得到一个控制器中的 Label 控件实例了。



3 在按钮 Button 中使用控制器类 Controller 的 on\_press 方法来创建一个自定义回调。

    root 和 self 都是保留关键词，任何地方都不可见的。root 代表的是规则中的顶层控件，self 表示的是当前控件。

	你可以在当前规则中使用任意的已经声明过的 id， root 和 self 也可以这样来用。比如下面就是在 on\_press\(\) 里面使用 root：







\`\`\`Python

Button:

    on\_press: root.do\_action\(\); my\_custom\_label.font\_size = 18

\`\`\`



就这么多了。现在当我们再次运行 main.py的时候，controller.kv 就会被加载，然后 Button 按钮和 Label 文本标签就会出现，并且根据触摸事件进行响应了。







