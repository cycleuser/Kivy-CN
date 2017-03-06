Title: Kivy Input management
Date: 2017-02-12
Category: Kivy
Tags: Python,Kivy


# Kivy中文编程指南：输入管理

[英文原文](kivy.org/docs/guide/inputs.html)

##译者前言
这一章节比上一章节翻译的还差，最近睡眠不太好，术后恢复比较差，大家凑合看看，看不下去给指出来一下比较不好理解和绕的地方，以及错误的地方，我一定即时修改。



## 输入体系

Kivy能处理绝大多数的输入类型：鼠标，触摸屏，加速器，陀螺仪等等。并且针对以下平台能够处理多点触控的原生协议：Tuio, WM_Touch, MacMultitouchSupport, MT Protocol A/B 以及 Android。（译者注：第一个TUIO应该是通用多点触控，第二个怀疑是WindowsMobile的，第三个是苹果的多点触控，第四个不知道是啥，最后一个是Android的。）

整体上输入体系的结构概括起来如下所示：


```HTML
Input providers -> Motion event -> Post processing -> Dispatch to Window

输入源 -> 动作事件 -> 事后处理 -> 分派到窗口
```







所有输入事件的类是[`MotionEvent`](https://kivy.org/docs/api-kivy.input.motionevent.html#kivy.input.motionevent.MotionEvent "kivy.input.motionevent.MotionEvent")。这个类生成两种事件：


* Touch触控事件：包含位置信息，至少X和Y坐标位置的一种Motion动作事件。所有这种Touch事件都通过控件树进行分派。

* Non-Touch非触控事件：其余的各种事件。例如加速度传感器就是一个持续的事件，不具有坐标位置。这一事件没有起止，一直在发生。这类的事件都不通过控件树来分派。


Motion动作事件是由[`InputProvider`](https://kivy.org/docs/api-kivy.input.providers.html#module-kivy.input.providers "kivy.input.providers")生成的。 
InputProvider这个类就是负责读取输入事件，这些输入事件的来源可以是操作系统，网络或者其他的应用程序。如下这几个都是已有的输入源：

* [`TuioMotionEventProvider`](https://kivy.org/docs/api-kivy.input.providers.tuio.html#kivy.input.providers.tuio.TuioMotionEventProvider "kivy.input.providers.tuio.TuioMotionEventProvider")：创建一个UDP服务端，侦听TUIO/OSC信息。
* `WM_MotionEventProvider`：使用Windows API来读取多点触控信息并发送给Kivy。
* `ProbeSysfsHardwareProbe`：在Linux中，遍历连接到计算机的所有硬件，并为找到的每个多点触摸设备附加一个多点触摸输入提供程序。
* 还有很多很多啦！

当你写一个应用程序的时候，就不用再去重造一个输入源了。Kivy会自动检测可用的硬件。然而，如果你想要支持某些特殊定制的专门硬件，就可能得对Kivy的配置进行一下调整才行。

在新建的Motion动作事件被传递给用户之前，Kivy会先对输入进行处理。Kivy会对每一个动作事件进行分析来检查和纠正错误输入，也是保证能提供有意义的解释，比如：

* 根据姿势和持续时间来检测双击或三次点击；
* 在硬件设备精度不佳的情况下提高事件精确度；
* 原生触摸硬件若在近似相同位置发送事件则降低生成事件数量。



经过上面这些步骤之后，这个Motion动作事件就会被分派给对应的窗口。正如之前解释过的，并非所有事件都分派给整个控件树，程序窗口要对事件进行过滤筛选。对于一个给定的事件：


* 如果仅仅是一个Motion动作事件，那它就会被分派给[`on_motion()`](https://kivy.org/docs/api-kivy.core.window.html#kivy.core.window.WindowBase.on_motion "kivy.core.window.WindowBase.on_motion")；

* 如果是一个Touch事件，这个触摸控件的坐标位置（x,y)（范围在0-1）会被调整到与窗口尺寸（宽高）相适应，然后对应发给下面这些方法：
    * [`on_touch_down()`](https://kivy.org/docs/api-kivy.uix.widget.html#kivy.uix.widget.Widget.on_touch_down "kivy.uix.widget.Widget.on_touch_down")
    * [`on_touch_move()`](https://kivy.org/docs/api-kivy.uix.widget.html#kivy.uix.widget.Widget.on_touch_move "kivy.uix.widget.Widget.on_touch_move")
    * [`on_touch_up()`](https://kivy.org/docs/api-kivy.uix.widget.html#kivy.uix.widget.Widget.on_touch_up "kivy.uix.widget.Widget.on_touch_up")







## Motion动作事件的属性


你用的硬件和输入源可能允许你能获取到更多信息。比如一个Touch触摸输入不仅有坐标位置（x,y），还可能有压力强度信息，触摸范围大小，加速度矢量等等。

在Motion动作事件中，有一个字符串作为profile属性，用于说明该事件内都有那些可用的效果。假如咱们有下面这样的一个`on_touch_move`方法：



```Python
def on_touch_move(self, touch):
    print(touch.profile)
    return super(..., self).on_touch_move(touch)
```




在控制台的打印输出可能是：



```Bash
['pos', 'angle']
```




#### 特别注意


很多人可能会把这里Motion事件的Profile属性的名字与对应的Property属性弄混。一定要注意，可用Profile属性中存在`angle`，并不意味着Touch事件对象也必须有一个`angle`的Property属性。


对应profile属性`'pos'`，property属性中有位置信息`pos`，`x`,`y`。profile属性`angle`，property属性对应的是有角度`a`。刚刚我们就说了，对touchTouch事件来说，profile属性中按照惯例是必须有位置属性`pos`的，但不一定有角度属性`angle`。对角度属性`angle`是否存在，可以用下面的方法来检测一下：



```Python
def on_touch_move(self, touch):
    print('The touch is at position', touch.pos)
    if 'angle' in touch.profile:
        print('The touch angle is', touch.a)
```



在[`motionevent`](https://kivy.org/docs/api-kivy.input.motionevent.html#module-kivy.input.motionevent "kivy.input.motionevent")文档中，可以找到所有可用profile属性的列表。


##Touch事件


有一种特殊的[`MotionEvent`动作事件](https://kivy.org/docs/api-kivy.input.motionevent.html#kivy.input.motionevent.MotionEvent "kivy.input.motionevent.MotionEvent") ，这种事件的[`is_touch`](https://kivy.org/docs/api-kivy.input.motionevent.html#kivy.input.motionevent.MotionEvent.is_touch "kivy.input.motionevent.MotionEvent.is_touch") 方法返回的是True，这就是Touch事件。


所有的Touch事件，都默认就有X和Y的坐标信息，与窗口的宽度和高度相匹配。换句话说就是所有的Touch事件都有`pos`这一profile属性。

### 基本简介


默认情况下，Touch事件会被分派给所有当前显示的控件。也就是说无论这个Touch是否发生在控件的物理范围内，控件都会收到它。

如果你接触过其他的GUI框架，可能觉得这特点挺违背直觉的。一般的GUI框架里面，都是把屏幕分割成多个几何区域，然后只在发生区域内的控件才会被分派到触摸或者鼠标事件。

这个设定对触摸输入的情景来说就过于严格了。因为用手指划，之间点戳，还有长时间按，都可能会有偏移导致落到 用户希望进行交互的控件外的情景。

为了提供最大的灵活性，Kivy会把事件分派给所有控件，然后让控件来自行决定如何应对这些事件。如果你只希望在某个控件内对Touch事件作出反应，只需要按照如下方法进行一下检测：



```Python
def on_touch_down(self, touch):
    if self.collide_point(*touch.pos):
        # The touch has occurred inside the widgets area. Do stuff!
        pass
```









### 坐标位置


一旦你使用一个带有矩阵变换的控件，就一定要处理好Touch事件中的矩阵变换。例如[`Scatter`](https://kivy.org/docs/api-kivy.uix.scatter.html#kivy.uix.scatter.Scatter "kivy.uix.scatter.Scatter")这样的某些控件，自身会有矩阵变换，这就意味着Touch事件也必须用Scatter矩阵进行处理，这样才能正确地把Touch事件的位置分派给Scatter的子控件。



* 从上层空间到本地空间获取坐标： [`to_local()`](https://kivy.org/docs/api-kivy.uix.widget.html#kivy.uix.widget.Widget.to_local "kivy.uix.widget.Widget.to_local")
* 从本地空间到上层空间获取坐标：  [`to_parent()`](https://kivy.org/docs/api-kivy.uix.widget.html#kivy.uix.widget.Widget.to_parent "kivy.uix.widget.Widget.to_parent")
* 从本地空间到窗口空间获取坐标：  [`to_window()`](https://kivy.org/docs/api-kivy.uix.widget.html#kivy.uix.widget.Widget.to_window "kivy.uix.widget.Widget.to_window")
* 从窗口空间到本地空间获取坐标： [`to_widget()`](https://kivy.org/docs/api-kivy.uix.widget.html#kivy.uix.widget.Widget.to_widget "kivy.uix.widget.Widget.to_widget")


一定要使用上面方法当中的某一种来确保内容坐标系适配正确。然后下面这段代码里是Scatter的实现：



```Python
def on_touch_down(self, touch):
    # push the current coordinate, to be able to restore it later
	# 这里用push先把当前的坐标位置存留起来，以后就还可以恢复到这个坐标
    touch.push()

    # transform the touch coordinate to local space
	# 接下来就是把Touch的坐标转换成本地空间的坐标
    touch.apply_transform_2d(self.to_local)

    # dispatch the touch as usual to children
    # the coordinate in the touch is now in local space
	# 转换之后把这个Touch事件按照惯例分派给子控件
	# Touch事件的坐标位置现在就是本地空间的了
    ret = super(..., self).on_touch_down(touch)

    # whatever the result, don't forget to pop your transformation
    # after the call, so the coordinate will be back in parent space
	#无论结果如何，一定记得把这个转换用pop弹出
	# 之后，坐标就又恢复成上层空间的了
    touch.pop()

    # return the result (depending what you want.)
	# 最后就是返回结果了
    return ret
```









### Touch事件的形状

If the touch has a shape, it will be reflected in the ‘shape’ property. Right now, only a [`ShapeRect`](https://kivy.org/docs/api-kivy.input.shape.html#kivy.input.shape.ShapeRect "kivy.input.shape.ShapeRect") can be exposed:

如果你的Touch事件有某个形状，这个信息会反映在`shape`这一property属性中。目前能用的就是一个 [`ShapeRect`](https://kivy.org/docs/api-kivy.input.shape.html#kivy.input.shape.ShapeRect "kivy.input.shape.ShapeRect")：


```Python
from kivy.input.shape import ShapeRect
def on_touch_move(self, touch):
    if isinstance(touch.shape, ShapeRect):
        print('My touch have a rectangle shape of size',
            (touch.shape.width, touch.shape.height))
    # ...
```









### 双击

A double tap is the action of tapping twice within a time and a distance. It’s calculated by the doubletap post-processing module. You can test if the current touch is one of a double tap or not:

双击是一种特定动作，在一小段时间和很短的一小段特定距离内敲击两下。双击的计算识别是通过一个双击后处理模块来实现的。可以用如下代码来检测当前的Touch是否是双击动作中的一下：


```Python
def on_touch_down(self, touch):
    if touch.is_double_tap:
        print('Touch is a double tap !')
        print(' - interval is', touch.double_tap_time)
        print(' - distance between previous is', touch.double_tap_distance)
    # ...
```









### 三次点击

A triple tap is the action of tapping thrice within a time and a distance. It’s calculated by the tripletap post-processing module. You can test if the current touch is one of a triple tap or not:

三次点击和双击的概念类似，只不过是变成了点击三次。这个是通过一个三次点击后处理模块来计算识别的。可以用如下代码来检测当前的Touch是否是三次点击动作中的一下：



```Python
def on_touch_down(self, touch):
    if touch.is_triple_tap:
        print('Touch is a triple tap !')
        print(' - interval is', touch.triple_tap_time)
        print(' - distance between previous is', touch.triple_tap_distance)
    # ...
```











### 拖放事件

父控件可能会从`on_touch_down`中分派Touch事件到子控件，而不从`on_touch_move`或`on_touch_up`分派。这可能发生在某些特定情况知悉啊，比如一个Touch处于父控件的边界之外，这样父控件就会决定不对子控件通知这个Touch。


But you might want to do something in `on_touch_up`. Say you started something in the `on_touch_down` event, like playing a sound, and you’d like to finish things on the `on_touch_up` event. Grabbing is what you need.

不过有可能你还是得处理一下`on_touch_up`。比方说，你开始是`on_touch_down`事件，假设是按下播放语音之类的，然后你希望当手指抬起的时候`on_touch_up`事件发生的时候就结束任务。这时候就需要有Grab拖放事件了。


When you grab a touch, you will always receive the move and up event. But there are some limitations to grabbing:

拖放一个Touch的时候，总会收到移动和抬起事件。但对拖放有如下的限制：

* 至少会两次收到这个事件：一次是从父控件正常收到的事件，还有一次是从窗口获取的Grab拖放事件。

* 有可能你没有进行拖放，但还是会收到一个拖放Touch事件：这可能是因为在子控件处于拖放状态时，父控件发来了一个Touch事件。

* 在拖放状态下，Touch事件的坐标不会转换成控件空间的坐标，因为这个Touch事件是直接来自窗口的。所以要手动将坐标转换到本地空间。


下面这段代码展示了对拖放的使用：


```Python
def on_touch_down(self, touch):
    if self.collide_point(*touch.pos):

        # if the touch collides with our widget, let's grab it
        touch.grab(self)

        # and accept the touch.
        return True

def on_touch_up(self, touch):
    # here, you don't check if the touch collides or things like that.
    # you just need to check if it's a grabbed touch event
    if touch.grab_current is self:

        # ok, the current touch is dispatched for us.
        # do something interesting here
        print('Hello world!')

        # don't forget to ungrab ourself, or you might have side effects
        touch.ungrab(self)

        # and accept the last up
        return True
```









### Touch事件管理


想要了解更多Touch事件如何控制以及如何在控件之间传递，可以阅读一下[Widget touch event bubbling](https://kivy.org/docs/api-kivy.uix.widget.html#widget-event-bubbling)这部分内容。



