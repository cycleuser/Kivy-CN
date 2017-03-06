Title: Kivy Integration
Date: 2017-02-23
Category: Kivy
Tags: Python,Kivy


# Kivy中文编程指南：整合其他框架

[英文原文](https://kivy.org/docs/guide/other-frameworks.html)



这是在 Kivy 1.0.8 版本以后添加的新功能。



## 在 Kivy 内使用 Twisted


#### 特别注意

可以使用 kivy.support.install_twisted_reactor 这个函数来安装一个 twisted 反应器（reactor），这个反应器会在 Kivy 的事件循环内运行。


传递给此函数（kivy.support.install_twisted_reactor ）的任何参数、关键字参数将在线程选择反应器交叉函数上传递（threadedselect reactors interleave function 这个我不懂是什么，强行翻译了，抱歉）。 这些参数通常是传递给twisted 的反应器启动函数（reactor.startRunning）的。


#### 警告

Kivy 这里面的这个 reactore 和默认的 twisted reactor 反应器不一样，只有当你把 ‘installSignalHandlers’ 关键词参数设置为 1 来才去处理信号。这样做是为了保证 Kivy 能够按照常规情况来处理信号，而当你指定让 twisted reactor 来处理信号的时候再换用 twisted。（比如 SIGINT 智能信号等情境。）


Kivy 的样例代码中有一个例子，是一个简单的 twisted 服务端和客户端。服务端应用开启了一个 twisted 服务器，并对任何信息都进行日志记录。客户端的应用可以向服务端发送信息，然后接收服务端返回的信息并且输出显示。这些样例代码是基于 twisted 官方文档里面的简单的回声样例进行修改而实现的，原版代码可以在下面的地址中找到：


* [简单的服务端代码](http://twistedmatrix.com/documents/current/_downloads/simpleserv.py)
* [简单的客户端代码](http://twistedmatrix.com/documents/current/_downloads/simpleclient.py)



请按照如下步骤来尝试这个样例：先运行 echo_server_app.py， 然后运行 echo_client_app.py。在客户端的文本框中输入随便一些什么内容都可以，然后回车发送给服务端，服务端会把接收到的信息都原样返回，就像是回声一样。


### 服务端应用

```Python
# install_twisted_rector must be called before importing  and using the reactor
# 一定要先要导入反应器，然后才能调用install_twisted_rector



from kivy.support import install_twisted_reactor
install_twisted_reactor()

from twisted.internet import reactor
from twisted.internet import protocol

class EchoProtocol(protocol.Protocol):
    def dataReceived(self, data):
        response = self.factory.app.handle_message(data)
        if response:
            self.transport.write(response)

class EchoFactory(protocol.Factory):
    protocol = EchoProtocol

    def __init__(self, app):
        self.app = app

from kivy.app import App
from kivy.uix.label import Label

class TwistedServerApp(App):
    def build(self):
        self.label = Label(text="server started\n")
        reactor.listenTCP(8000, EchoFactory(self))
        return self.label

    def handle_message(self, msg):
        self.label.text = "received: %s\n" % msg

        if msg == "ping":
            msg = "pong"
        if msg == "plop":
            msg = "kivy rocks"
        self.label.text += "responded: %s\n" % msg
        return msg

if __name__ == '__main__':
    TwistedServerApp().run()
```



### 客户端应用


```Python
# install_twisted_rector must be called before importing the reactor
# 一定要先要导入反应器，然后才能调用install_twisted_rector

from kivy.support import install_twisted_reactor
install_twisted_reactor()

# A simple Client that send messages to the echo server
# 这个简单的客户端是用来给回声服务器发送信息的

from twisted.internet import reactor, protocol

class EchoClient(protocol.Protocol):
    def connectionMade(self):
        self.factory.app.on_connection(self.transport)

    def dataReceived(self, data):
        self.factory.app.print_message(data)

class EchoFactory(protocol.ClientFactory):
    protocol = EchoClient

    def __init__(self, app):
        self.app = app

    def clientConnectionLost(self, conn, reason):
        self.app.print_message("connection lost")

    def clientConnectionFailed(self, conn, reason):
        self.app.print_message("connection failed")

from kivy.app import App
from kivy.uix.label import Label
from kivy.uix.textinput import TextInput
from kivy.uix.boxlayout import BoxLayout

# A simple kivy App, with a textbox to enter messages, and
# a large label to display all the messages received from
# the server
# 这里的样例是一个很简单的 Kivy 应用，有一个字符输入框 textbox 来输入消息
# 还有一个大的文本标签 label 来显示从服务器接收到的信息。



class TwistedClientApp(App):
    connection = None

    def build(self):
        root = self.setup_gui()
        self.connect_to_server()
        return root

    def setup_gui(self):
        self.textbox = TextInput(size_hint_y=.1, multiline=False)
        self.textbox.bind(on_text_validate=self.send_message)
        self.label = Label(text='connecting...\n')
        self.layout = BoxLayout(orientation='vertical')
        self.layout.add_widget(self.label)
        self.layout.add_widget(self.textbox)
        return self.layout

    def connect_to_server(self):
        reactor.connectTCP('localhost', 8000, EchoFactory(self))

    def on_connection(self, connection):
        self.print_message("connected successfully!")
        self.connection = connection

    def send_message(self, *args):
        msg = self.textbox.text
        if msg and self.connection:
            self.connection.write(str(self.textbox.text))
            self.textbox.text = ""

    def print_message(self, msg):
        self.label.text += msg + "\n"

if __name__ == '__main__':
    TwistedClientApp().run()
```
