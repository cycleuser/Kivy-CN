Title: Kivy Graphics
Date: 2017-02-12
Category: Kivy
Tags: Python,Kivy


# Kivy中文编程指南：图形

[英文原文](https://kivy.org/docs/guide/graphics.html)

##译者前言
这一章节比前两章节简单很多，翻译的也比较顺了。



## 简介Canvas

Kivy中控件图形呈现是使用Canvas完成的，可以将其看作一个无限的绘图板，也是一组绘图指令。有很多种绘图指令都可以应用或者添加到你的Canvas伤，不过总体上分为两类：


* [`context instructions`环境指令](https://kivy.org/docs/api-kivy.graphics.context_instructions.html#module-kivy.graphics.context_instructions "kivy.graphics.context_instructions")
* [`vertex instructions`顶点指令](https://kivy.org/docs/api-kivy.graphics.vertex_instructions.html#module-kivy.graphics.vertex_instructions "kivy.graphics.vertex_instructions")


[`context instructions`环境指令](https://kivy.org/docs/api-kivy.graphics.context_instructions.html#module-kivy.graphics.context_instructions "kivy.graphics.context_instructions")不绘制任何图形，但会改变[`vertex instructions`顶点指令](https://kivy.org/docs/api-kivy.graphics.vertex_instructions.html#module-kivy.graphics.vertex_instructions "kivy.graphics.vertex_instructions")的绘制结果。

Canvas都包含两个指令分支。分别是[`canvas.before`](https://kivy.org/docs/api-kivy.graphics.html#kivy.graphics.Canvas.before "kivy.graphics.Canvas.before")和[`canvas.after`](https://kivy.org/docs/api-kivy.graphics.html#kivy.graphics.Canvas.after "kivy.graphics.Canvas.after") 这两种指令群。这两组指令分别在`Canvas`图形绘制前后执行。
绘制前的会被绘制的图形覆盖掉，绘制后的会覆盖在图形上层。这些指令都在用户对它们读取之后才会被创建。


要对一个控件添加Canvas绘图指令，需要使用Canvas环境指令：




```Python
class MyWidget(Widget):
    def __init__(self, **kwargs):
        super(MyWidget, self).__init__(**kwargs)
        with self.canvas:
            # add your instruction for main canvas here
			# 这里是增加一个座位主绘图的指令

        with self.canvas.before:
            # you can use this to add instructions rendered before
			# 这里可以在绘图之前添加指令

        with self.canvas.after:
            # you can use this to add instructions rendered after
			# 这里可以在绘图之后添加指令
```









## 环境指令

环境指令是用于操作opengl环境。 可以旋转，翻译和缩放画布。还可以附加纹理或更改绘图颜色。下面这段代码里面的是最常用到的更改颜色的指令，其他的环境指令也都很有用处：

```Python
with self.canvas.before:
    Color(1, 0, .4, mode='rgb')
```











## 绘图指令


绘图指令可简可繁，最简单的比如画一个多边形，更复杂的比如绘制网格或者贝塞尔曲线都可以：



```Python
with self.canvas:
   # draw a line using the default color
   # 用默认颜色画一条线
   Line(points=(x1, y1, x2, y2, x3, y3))

   # lets draw a semi-transparent red square
   # 接下来画一个半透明的红方块
   Color(1, 0, 0, .5, mode='rgba')
   Rectangle(pos=self.pos, size=self.size)
```










## 操作指令


有时候可能需要把之前添加到Canvas绘图上的指令进行更改或者删除，这可以有很多种办法，要根据具体需求来选择：

可以给指令创建一个引用然后对其进行更新：

```Python
class MyWidget(Widget):
    def __init__(self, **kwargs):
        super(MyWidget, self).__init__(**kwargs)
        with self.canvas:
            self.rect = Rectangle(pos=self.pos, size=self.size)

        self.bind(pos=self.update_rect)
        self.bind(size=self.update_rect)

    def update_rect(self, *args):
        self.rect.pos = self.pos
        self.rect.size = self.size
```


或者也可以清空Canvas画布然后重新画：

```Python
class MyWidget(Widget):
    def __init__(self, **kwargs):
        super(MyWidget, self).__init__(**kwargs)
        self.draw_my_stuff()

        self.bind(pos=self.draw_my_stuff)
        self.bind(size=self.draw_my_stuff)

    def draw_my_stuff(self):
        self.canvas.clear()

        with self.canvas:
            self.rect = Rectangle(pos=self.pos, size=self.size)
```


要注意更新指令的方法是更好的选择，因为这样减少了开销，并且避免了创建新指令。

