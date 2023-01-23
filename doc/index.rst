pyglet 文档
====================

.. 注意::
   本文档适用于 pyglet 2.x 系列，该系列不兼容
   与 1.5 及更早版本相比的更改。如果您正在寻找文档
   对于以前的稳定版本，请参阅
   `1.5 maintenance <https://pyglet.readthedocs.io/en/pyglet-1.5-maintenance/>`_.


**pyglet** 是一个跨平台的Python窗口和多媒体库，
用于开发游戏和其他视觉丰富的应用程序。它支持
窗口化、用户界面事件处理、游戏控制器和操纵杆、
OpenGL图形，加载图像和视频，以及播放声音和音乐。
**pyglet** 适用于Windows，OS X和Linux。

pyglet的一些特点是:

* **没有外部依赖关系或安装要求。** 
   对于大多数应用程序和游戏要求，pyglet 除了 Python 之外不需要其他任何东西，
   简化分发和安装。
* **利用多个窗口和多显示器桌面。** 
   Pyglet 允许您根据需要使用任意数量的窗口，并且完全了解
   用于全屏游戏和应用程序的多显示器设置。
* **以几乎任何格式加载图像，声音，音乐和视频。** 
   Pyglet 有内置对常见音频和图像格式的支持，并可以选择使用
   FFMPEG加载几乎任何其他压缩的音频或视频文件。
* **pyglet 在 BSD 开源许可证下提供**
   允许您将其用于商业和其他开源项目，只需很少
   限制。

请加入我们的 `Discord`_ 服务器, 或加入我们的 `mailing list`_!

.. _Discord: https://discord.gg/QXyegWe
.. _mailing list: http://groups.google.com/group/pyglet-users

如果这是您第一次阅读有关pyglet的信息，我们建议您从
:doc:`programming_guide/quickstart`.
如果您要从旧版本的 pyglet 迁移，请通读
:doc:`programming_guide/migration`.

.. toctree::
   :maxdepth: 3
   :caption: Programming Guide

   programming_guide/installation
   programming_guide/quickstart
   programming_guide/windowing
   programming_guide/keyboard
   programming_guide/mouse
   programming_guide/input
   programming_guide/shapes
   programming_guide/image
   programming_guide/media
   programming_guide/text
   programming_guide/resources
   programming_guide/graphics
   programming_guide/events
   programming_guide/time
   programming_guide/context
   programming_guide/gl
   programming_guide/shaders
   programming_guide/math
   programming_guide/eventloop
   programming_guide/examplegame

   programming_guide/options
   programming_guide/debug
   programming_guide/migration

.. toctree::
   :maxdepth: 3
   :caption: API Reference

   modules/pyglet
   modules/app
   modules/canvas
   modules/clock
   modules/event
   modules/font
   modules/gl
   modules/math
   modules/graphics/index
   modules/gui
   modules/image/index
   modules/info
   modules/input
   modules/media
   modules/resource
   modules/sprite
   modules/shapes
   modules/text/index
   modules/window

.. toctree::
   :maxdepth: 3
   :caption: External Resources

   external_resources

.. toctree::
   :maxdepth: 3
   :caption: Development Guide

   internal/contributing
   internal/virtualenv
   internal/testing
   internal/doc
   internal/dist
   internal/gl
   internal/generated
   internal/wraptypes
   internal/media_manual
   internal/media_logging_manual
