Title: Kivy Events and Properties
Date: 2017-02-9
Category: Kivy
Tags: Python,Kivy


# Kivy中文编程指南：事件和属性

[英文原文](https://kivy.org/docs/guide/events.html)

##译者前言
这一章节是我有史以来翻译质量的低谷，一来是我自己也是刚学，半懂不懂，二来本身语言基础各方面也薄弱，三来是笔记本坏掉了，搞个ChromeOS折腾中。

大家凑合看看，看不下去给指出来一下比较不好理解和绕的地方，以及错误的地方，我一定即时修改。



##简要介绍

在Kivy开发过程中，事件是最重要的一部分了。如果之前有过GUI图形界面开发的经验的话，你可能对此习以为常了，但对新手来说，这个概念很重要。一旦你理解了事件的应用和搭配，你就会发现在Kivy开发的过程中，事件是无处不在的。有了各种事件的搭配，你就可以用Kivy来搭建你想要的各种功能了。



下面这幅图展示了Kivy框架中事件的处理过程：

![../_images/Events.png](http://kivy.org/docs/_images/Events.png)



## 事件分派器




事件分派器[EventDispatcher](http://kivy.org/docs/api-kivy.event.html#kivy.event.EventDispatcher "kivy.event.EventDispatcher") c是Kivy框架中最重要的基类之一。通过这个类，用户可以注册各种事件，然后分发给对应的部件（一般情况下是其他的事件分派器）。控件类[Widget](http://kivy.org/docs/api-kivy.uix.widget.html#kivy.uix.widget.Widget "kivy.uix.widget.Widget"), 动画类[Animation](http://kivy.org/docs/api-kivy.animation.html#kivy.animation.Animation "kivy.animation.Animation") 以及时间类[Clock](http://kivy.org/docs/api-kivy.clock.html#kivy.clock.Clock "kivy.clock.Clock") 都属于事件分派器。



事件分派器对象要在整个程序的循环流程的基础上来生成和处理各种事件。



## 主流程

上文的简介概括起来是说，Kivy有一个主循环体。这个循环体会在Kivy应用的整个生命周期中一直运行，直到退出应用的时候才结束。

在循环内部，每一步迭代都伴随有各种事件生成，这些事件可以来自用户输入、硬件传感器，或者是其他的各种来源，然后一帧一帧地渲染到屏幕上。

你写的应用程序将要指定好各种由主循环进行调用而产生的**回调**（callback，稍后再详细介绍相关内容）。如果一次**回调**花费很长时间或者根本不退出，主循环就被打破了，你的应用也就不能正常工作了。

在Kivy应用里面，一定要避免用特别长的循环、无限循环，或者休眠。下面这个代码就同时是死循环+休眠，就是一个反例了：


```Python
while True:
    animate_something()
    time.sleep(.10)
```

如果把上面这段代码拿去运行，那程序就会无法退出循环了，就让Kivy卡住了，什么后续步骤都不能进行了。用户就只能看到一个黑色的窗口，什么操作都没有响应。不能死循环也不能休眠，所以就得想其他办法，比如有计划地重复调用对animate_something()这样的函数。（animate_something 的意思是让某个东西动起来，作者是用来指代类似的这种需要时不时重复调用的函数。）




### 计划周期事件


利用schedule_interval()这个函数，你就可以每秒对某个函数或者方法进行指定次数的调用了。下面就是一个例子，在这段代码的第三行，实现了每秒钟调用my_callback(dt)函数三十次：

```Python
def my_callback(dt):
    print 'My callback is called', dt
event = Clock.schedule_interval(my_callback, 1 / 30.)
#译者注：这里的1/30明显就是频率的倒数了，如果是每秒钟50次就应该是1/50，以此类推了，大家可以自己修改试试看。
```

要取消之前的计划事件有多种方法。可以用cancel()，也可以用 unschedule()：

```Python
event.cancel()

#或者用下面这种方法

Clock.unschedule(event)
```

再有一种方法，就是在回调的时候返回False，这样这个事件就会被自动取消计划，不再重复：


``` Python
count = 0
def my_callback(dt):
    global count
    count += 1
    if count == 10:
        print 'Last call of my callback, bye bye !'
        return False
    print 'My callback is called'
Clock.schedule_interval(my_callback, 1 / 30.)
```








### 计划一次性事件

使用schedule_once()函数，可以对一个函数**稍后调用**的效果，可以是在下一帧，也可以是在指定时间X之后：


```Python
def my_callback(dt):
    print 'My callback is called !'
Clock.schedule_once(my_callback, 1)
```
上面这段代码会在一秒钟后再对my_callback(dt)进行调用。schedule_once()函数的第二个变量X就是延迟调用的时间，以秒为单位。具体这个变量的用法有以下三种：

* 若X大于零，则作为时间长度的秒数，延迟X秒之后进行下一次调用
* 若X等于零，则在下一帧进行调用
* 若X为-1，调用则发生在下一帧渲染之前

假如你已经有了一个计划事件，但又想要在下一帧渲染之前计划一次调用，这种情况就适合使用-1这种用法。


这里就有了一种衍生出来的重复调用某个函数的方法，就是在函数体内放一个schedule_once()，然后在第二次调用该函数的时候，函数内的schedule_once()就会继续对本身进行调用了：


```Python
def my_callback(dt):
    print 'My callback is called !'
    Clock.schedule_once(my_callback, 1)
Clock.schedule_once(my_callback, 1)
```

主循环会一直按照代码的要求来保持各种计划调用的实现，但一次计划调用发生的具体时间是具有一些不确定性的。有时候其他的调用或者应用中的其他任务可能会比想象中执行得更久一些，这时候用计时的方法制定计划就不太合适了。

后面介绍的这种用内置schedule_once()来进行重复回调问题的解决方案中，在最后一次迭代结束后，下一次迭代将至少要一秒之后才能被调用。 而使用schedule_interval()这种方法就可以每秒都进行回调。


### 触发事件


有时候可能一个函数只需要计划在下一帧调用一次，而不允许重复调用。这时候就可以用下面这样的思路来实现：

```Python

#首先是用schedule_once()计划调用一次
event = Clock.schedule_once(my_callback, 0)

#然后在另外一个位置，就用unschedule()取消计划调用，这样就能避免重复调用。接下来就是再次用schedule_once()进行计划调用。
Clock.unschedule(event)
event = Clock.schedule_once(my_callback, 0)

#译者注：我这部分理解的也不够深，翻译得很生硬，我的大概理解就是这样可以精确控制调用次数，避免一次计划调用之后发生的调用次数不可控。
```

上面这种方法构建触发器可谓费时费力，因为你得经常用到unschedule，即使一个事件已经结束。此外，每次还都产生新事件。所以可以用下面这个trigger()来作为触发器：


```Python
trigger = Clock.create_trigger(my_callback)
# later
trigger()
```


这样你每次调用trigger()就可以了，这个触发器会对你的my_callback回调进行单次计划调用。如果之前存在计划调用了，则不重新产生计划调用。





##控件事件

控件有两种默认事件：

* 属性事件：比如你的控件改变了位置或者大小，就会触发一个事件。

* 控件定义的事件：比如一个Button按钮控件被按下或者松开，也会触发一个事件。


关于控件的Touch事件的管理和传播，可以参考[API文档中这部分相关内容](http://kivy.org/docs/api-kivy.uix.widget.html#widget-event-bubbling)。



##创建自定义事件


要使用自定义事件创建事件分派器，需要首先在类中注册事件名称，然后创建同名的方法。

例如下面这段代码所示：

```Python
class MyEventDispatcher(EventDispatcher):
    def __init__(self, **kwargs):
        self.register_event_type('on_test')
        super(MyEventDispatcher, self).__init__(**kwargs)

    def do_something(self, value):
        # when do_something is called, the 'on_test' event will be
        # dispatched with the value
        self.dispatch('on_test', value)

    def on_test(self, *args):
        print "I am dispatched", args
```









##附加回调



要利用一个事件，必须要对其绑定回调。当事件被分派的时候，该特定事件相关的参数将被用于调用回调。

回调可以使Python中能进行调用的任意内容，函数或者方法都可以，但一定要确保回调要接收**事件**发出的参数。最安全的常规做法是接收* args参数，将所有参数都存放成一个参数列表。


例如：

```Python
def my_callback(value, *args):
    print "Hello, I got an event!", args

ev = MyEventDispatcher()
ev.bind(on_test=my_callback)
ev.do_something('test')
```





请阅读参考 [kivy.event.EventDispatcher.bind()](http://kivy.org/docs/api-kivy.event.html#kivy.event.EventDispatcher.bind "kivy.event.EventDispatcher.bind") 方法的文档来查看更多附加调用相关的样例。




##简介属性Properties


属性（Properties）是定义和绑定事件的一种很赞的办法。关键就是属性能生成事件，这样当你的某个对象中有一个属性（attribute）发生改变的时候，所有引用该属性（attribute）的属性（Properties）都会被自动更新

译者注：我汉语词汇量匮乏了，很痛苦，这里说不明白了，所以就放了原文的单词作为对比，避免混淆了。



针对你要处理的数据类型，存在很多种不同类型的属性（Properties）：

* [字符串属性StringProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.StringProperty "kivy.properties.StringProperty")
* [数值属性NumericProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.NumericProperty "kivy.properties.NumericProperty")
* [有界数值属性BoundedNumericProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.BoundedNumericProperty "kivy.properties.BoundedNumericProperty")
* [对象属性ObjectProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.ObjectProperty "kivy.properties.ObjectProperty")
* [词典属性DictProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.DictProperty "kivy.properties.DictProperty")
* [列表属性ListProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.ListProperty "kivy.properties.ListProperty")
* [选项属性OptionProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.OptionProperty "kivy.properties.OptionProperty")
* [别名属性AliasProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.AliasProperty "kivy.properties.AliasProperty")
* [布尔属性BooleanProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.BooleanProperty "kivy.properties.BooleanProperty")
* [引用属性ReferenceListProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.ReferenceListProperty "kivy.properties.ReferenceListProperty")





##属性声明

要声明属性（Properties），必须要在类的层次上进行声明。接下来这个类才能在你创建对象的时候对真是的属性（attributes）进行实例化。此属性（Properties）非彼属性（attributes），Properties是根据你的attributes来创建事件的机制，例如：


```Python
class MyWidget(Widget):
    text = StringProperty('')
```

译者注：我这段翻译的很垃圾，一来是自己水平问题，二来是自己的语言能力问题。




当覆盖初始化方法__init__的时候，**一定要**接收**kwargs做参数，并且一定要用super()来调用基类的初始化方法__init__，传递自定义类的实例过去：

```Python
def __init__(self, **kwargs):
    super(MyWidget, self).__init__(**kwargs)
```



##分派属性事件


Kivy的属性Property，默认提供了一个on_事件。在属性被改变的时候，就会调用这个事件了。


####特别注意

如果属性的新值与当前已有的值相等，那么on_事件就不会被调用了。

例如下面这段代码：

```Python
 class CustomBtn(Widget):

     pressed = ListProperty([0, 0])

     def on_touch_down(self, touch):
         if self.collide_point(*touch.pos):
             self.pressed = touch.pos
             return True
         return super(CustomBtn, self).on_touch_down(touch)

     def on_pressed(self, instance, pos):
         print ('pressed at {pos}'.format(pos=pos))
```



上面代码的第三行中：


```Python
pressed = ListProperty([0, 0])
```


这一句中，基于[ListProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.ListProperty "kivy.properties.ListProperty")定义了一个pressed按下的属性，默认值是[0,0]。从这往后，只要这个属性被改变了，on_presses事件就会被调用。



第五行有如下代码：

```Python
def on_touch_down(self, touch):
    if self.collide_point(*touch.pos):
        self.pressed = touch.pos
        return True
    return super(CustomBtn, self).on_touch_down(touch)
```



这部分代码覆盖了控件类的_touch_down()方法。这段代码中，用控件对touch触碰的位置进行了检测。


如果touch的位置在控件范围内，就把pressed的值改变成touch.pos这个值，然后返回True，这表明程序已经处理好了这个touch了，就把这个touch消耗掉了，不用再传播了。



如果touch的位置在控件外部，就通过super(...)了调用原始事件，并返回结果。这就和常规情况一样了，touch事件会被继续传递下去。


在第十一行：

```Python
def on_pressed(self, instance, pos):
    print ('pressed at {pos}'.format(pos=pos))
```


这里定义了一个on_pressed函数，只要属性值发生改变了，这个函数就会被调用。



####特别注意


这个on_事件是在类内定义属性的位置被调用。在定义该属性的类之外，若要监控/观察一个属性的任何变动，就必须丢这个属性进行bind绑定操作。


只能读取到一个控件实例的时候，要怎么去监控属性的变化呢？这时候用**bind**绑定一下属性就行了：


```Python
your_widget_instance.bind(property_name=function_name)
```




例如下面这段代码：


```Python
 class RootWidget(BoxLayout):

     def __init__(self, **kwargs):
         super(RootWidget, self).__init__(**kwargs)
         self.add_widget(Button(text='btn 1'))
         cb = CustomBtn()
         cb.bind(pressed=self.btn_pressed)
         self.add_widget(cb)
         self.add_widget(Button(text='btn 2'))

     def btn_pressed(self, instance, pos):
         print ('pos: printed from root widget: {pos}'.format(pos=.pos))

```


如果运行上面这段代码，会发现有两次print输出语句出现在控制台中。第一个是来自_pressed事件，在CustomBtn类内部调用；另外一次print是来自我们用bind绑定到了属性变化上的btn_pressed函数。

（译者注：在用bind绑定了之后，属性的变化都会通过bind的函数被看到了。）

两个函数都被调用的原因很简单。Bind绑定操作并不意味着覆盖。这两个函数同时保留就冗余了，所以一般情况你只选择一个来对属性变化进行监听/反应就行了。


你还得注意一下传递给on_事件或者绑定到属性的函数的参数。


```Python
def btn_pressed(self, instance, pos):
```




第一个参数是self，这就是该函数所在类本身的一个实例。也可以用内联函数，如下代码所示：


```Python
 cb = CustomBtn()

 def _local_func(instance, pos):
     print ('pos: printed from root widget: {pos}'.format(pos=pos))

 cb.bind(pressed=_local_func)
 self.add_widget(cb)
```


第一个参数是定义属性的类的实例。第二个参数是一个值，这个值是属性的新值。
上面那一段只是代码片段，下面这一段代码是完整的样例了，可以复制粘贴到编辑器里面然后测试一下：

```Python
 from kivy.app import App
 from kivy.uix.widget import Widget
 from kivy.uix.button import Button
 from kivy.uix.boxlayout import BoxLayout
 from kivy.properties import ListProperty

 class RootWidget(BoxLayout):

     def __init__(self, **kwargs):
         super(RootWidget, self).__init__(**kwargs)
         self.add_widget(Button(text='btn 1'))
         cb = CustomBtn()
         cb.bind(pressed=self.btn_pressed)
         self.add_widget(cb)
         self.add_widget(Button(text='btn 2'))

     def btn_pressed(self, instance, pos):
         print ('pos: printed from root widget: {pos}'.format(pos=pos))

 class CustomBtn(Widget):

     pressed = ListProperty([0, 0])

     def on_touch_down(self, touch):
         if self.collide_point(*touch.pos):
             self.pressed = touch.pos
             # we consumed the touch. return False here to propagate
             # the touch further to the children.
             return True
         return super(CustomBtn, self).on_touch_down(touch)

     def on_pressed(self, instance, pos):
         print ('pressed at {pos}'.format(pos=pos))

 class TestApp(App):

     def build(self):
         return RootWidget()

 if __name__ == '__main__':
     TestApp().run()


```



运行上面这段完整的样例代码，会得到如下图所示的输出：

![../_images/property_events_binding.png](http://kivy.org/docs/_images/property_events_binding.png)


咱们这个CustomBtn没有做任何视觉上的调整，所以就是个大黑块。你可以触摸/点击这个黑色的区域，来看看控制台里面的输出。


## 复合属性


定义一个[别名属性AliasProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.AliasProperty "kivy.properties.AliasProperty")的时候，通常要定义一个getter函数和一个setter函数，前者用来读取值，后者用来设定值。这时候，你就得通过bind绑定参数来确定好getter和setter函数的调用时间。


例如下面这段代码：

```Python
 cursor_pos = AliasProperty(_get_cursor_pos, None, bind=(
     'cursor', 'padding', 'pos', 'size', 'focus',
     'scroll_x', 'scroll_y'))
 '''Current position of the cursor, in (x, y).

 :attr:`cursor_pos` is a :class:`~kivy.properties.AliasProperty`, read-only.
 '''
```


这里的cursor_pos（光标位置的意思）就是一个[别名属性AliasProperty](http://kivy.org/docs/api-kivy.properties.html#kivy.properties.AliasProperty "kivy.properties.AliasProperty")，它有一个getter函数，名为_get_cursor_pos()，然后没有设置setter函数，这就说明这个属性是**只读**的。

最末尾那一段的bind参数的意思是，当在bind=这个等号后括号内的属性中有任意的一个发生变化，都会分派on_cursor_pos事件。
