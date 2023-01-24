.. _quickstart:

编写 pyglet 应用程序
===================

开始使用新的库或框架可能会令人生畏，尤其是在提供大量参考资料需要阅读时。
本章对pyglet进行了非常快速的介绍，而无需深入太多细节。

Hello, World
------------

我们将从必要的 "Hello, World" 介绍开始。
该程序将打开一个包含一些文本的窗口，然后等待关闭。
您可以在 `examples/programming_guide/hello_world.py` 文件中找到整个程序。

首先导入 :mod:`pyglet` 包::

    import pyglet

通过调用其默认构造函数来创建一个 :class:`pyglet.window.Window`。
该窗口将在创建后立即可见，并且其所有参数都具有合理的默认值::

    window = pyglet.window.Window()

为了显示文本，我们将创建一个 :class:`~pyglet.text.Label` 。
关键字参数用于设置标签的字体、位置和锚定::

    label = pyglet.text.Label('Hello, world',
                              font_name='Times New Roman',
                              font_size=36,
                              x=window.width//2, y=window.height//2,
                              anchor_x='center', anchor_y='center')

窗口在准备重绘其内容时调度一个 :meth:`~pyglet.window.Window.on_draw` 事件。
pyglet 提供了几种将事件处理程序附加到对象的方法，一个简单的方法是使用装饰器::

    @window.event
    def on_draw():
        window.clear()
        label.draw()

在上面的 :meth:`~pyglet.window.Window.on_draw` 处理程序中，窗口被清除为默认背景色（黑色），并绘制标签。

最后，调用::

    pyglet.app.run()

这将进入 pyglet 的默认事件循环，并让 pyglet 响应应用程序事件，例如鼠标和键盘。
现在将根据需要调用事件处理程序，并且 :func:`~pyglet.app.run` 方法仅在所有应用程序窗口关闭后返回。

如果您来自另一个库，您可能习惯于编写自己的事件循环，这也可以用pyglet来做。
有关详细信息，请参见 :ref:`programming-guide-eventloop` 。

图像查看器
---------

大多数游戏和应用程序都需要在屏幕上加载和显示图像。
在此示例中，我们将从应用程序的目录中加载图像并将其显示在窗口中::

    import pyglet

    window = pyglet.window.Window()
    image = pyglet.resource.image('kitten.jpg')

    @window.event
    def on_draw():
        window.clear()
        image.blit(0, 0)

    pyglet.app.run()

我们使用 :mod:`pyglet.resource` 的 :func:`~pyglet.resource.image` 函数来加载图像。
它会自动相对于源文件（而不是工作目录）定位文件。 
要加载未与应用程序捆绑在一起的图像（例如，在命令行上指定），请使用 :func:`pyglet.image.load` 。

:meth:`~pyglet.image.AbstractImage.blit` 方法绘制图像。 参数 ``(0, 0)`` 告诉 pyglet 在窗口（左下角）的像素坐标 (0, 0) 处绘制图像。

此示例的完整代码位于
`examples/programming_guide/image_viewer.py`.

处理鼠标和键盘事件
----------------------------------

到目前为止，唯一使用的事件是 :meth:`~pyglet.window.Window.on_draw` 事件。 
若要对键盘和鼠标事件做出反应，还需要为这些事件编写和附加事件处理程序::

    import pyglet

    window = pyglet.window.Window()

    @window.event
    def on_key_press(symbol, modifiers):
        print('A key was pressed')

    @window.event
    def on_draw():
        window.clear()

    pyglet.app.run()

键盘事件有两个参数：按下的虚拟键 `symbol` ，以及存在的任何 `modifiers` 的按位组合（例如， ``CTRL`` 和 ``SHIFT`` 键）。

关键符号定义在 :mod:`pyglet.window.key`::

    from pyglet.window import key

    @window.event
    def on_key_press(symbol, modifiers):
        if symbol == key.A:
            print('The "A" key was pressed.')
        elif symbol == key.LEFT:
            print('The left arrow key was pressed.')
        elif symbol == key.ENTER:
            print('The enter key was pressed.')

请参阅 :mod:`pyglet.window.key` 文档，获取关键符号的完整列表。

鼠标事件的处理方式类似::

    from pyglet.window import mouse

    @window.event
    def on_mouse_press(x, y, button, modifiers):
        if button == mouse.LEFT:
            print('The left mouse button was pressed.')

``x`` 和 ``y`` 参数给出按钮被按下时鼠标相对于窗口左下角的位置。

您可以在一个窗口上处理 20 多种事件类型。
查找所需事件名称和参数的一种简单方法是将以下行添加到程序中::

    event_logger = pyglet.window.event.WindowEventLogger()
    window.push_handlers(event_logger)

这将导致窗口上收到的所有事件都打印到控制台。

使用键盘和鼠标事件的示例程序位于
`examples/programming_guide/events.py`

播放声音和音乐
-------------

Pyglet 可以轻松播放和混合多种声音。
以下示例播放 MP3 文件 [#mp3]_::

    import pyglet

    music = pyglet.resource.media('music.mp3')
    music.play()

    pyglet.app.run()

与前面介绍的图像加载示例一样， :func:`~pyglet.resource.media` 将声音文件定位在应用程序的目录（而不是工作目录）中。 
如果您知道实际的文件系统路径（相对路径或绝对路径），请使用 :func:`pyglet.media.load` 。

默认情况下，播放时会流式传输音频。这适用于较长的音乐曲目。
短声音（例如游戏中使用的枪声）应在使用之前在内存中完全解码。
这使他们能够更立即地播放，并减少 CPU 性能损失。
它还允许重复播放相同的声音而无需重新加载。
在这种情况下指定 ``streaming=False`` ::

    sound = pyglet.resource.media('shot.wav', streaming=False)
    sound.play()

`examples/media_player.py`  示例演示了使用 pyglet 播放流音频和视频。  
`examples/noisy/noisy.py` 示例演示了同时播放许多简短的音频样本，就像在游戏中一样。

.. [#mp3] MP3 和其他压缩音频格式需要安装 FFmpeg。未压缩的 WAV 文件可以在没有 FFmpeg 的情况下播放。

下一步去哪里？
------------

上面的示例向您展示了如何在屏幕上显示某些内容，以及如何执行一些基本任务。 
您可能会对这些示例有很多疑问，但不要担心。本编程指南的其余部分将详细介绍 pyglet 的许多功能。 
如果您是一位经验丰富的开发人员，则可以直接进入您感兴趣的部分。

对于新用户来说，一次通读所有内容可能会令人生畏。
如果您感到不知所措，我们建议您浏览每章的开头，然后查看更深入的示例项目。
您可以在 :ref:`programming-guide-game` 部分找到2D游戏的示例。

要编写高级 3D 应用程序或在 2D 应用程序中实现最佳性能，您还可以直接使用 OpenGL。 
如果你只想使用 OpenGL 原语，但想要一些稍微高级的东西，请查看 :ref:`guide_graphics` 模块。

在文档和源代码发行版的 ``examples/`` 目录中有许多pyglet应用程序的例子。 
如果您遇到困难或有任何疑问，请加入我们的 `mailing list`_ 或 `Discord`_ ！

.. _mailing list: http://groups.google.com/group/pyglet-users
.. _Discord: https://discord.gg/QXyegWe
