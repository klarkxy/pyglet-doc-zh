创建 OpenGL 上下文
==========================

本节介绍如何配置 OpenGL 上下文。对于大多数应用程序这里描述的信息太低级，无法提供任何关注。
但是更高级的应用程序可以利用完整的控制 Pyglet 提供。

显示、屏幕、配置和上下文
---------------------------------------

.. figure:: img/context_flow.png

    构建流程，从抽象的画布到新的创建了带有上下文的窗口。

上下文和配置
^^^^^^^^^^^

当您在 pyglet 中的窗口上绘制时，您正在绘制到 OpenGL 上下文。
每个窗口都有自己的上下文，该上下文是在创建窗口时创建的。
您可以通过其访问窗口的上下文
:attr:`~pyglet.window.Window.context` 属性.

上下文是从 OpenGL 配置（或“配置”）创建的，该配置（或“配置”）已描述上下文的各种属性，例如要使用的颜色格式，
有多少个缓冲区可用，依此类推。 您可以访问配置用于通过上下文的
:attr:`~pyglet.gl.Context.config` 属性.

例如，这里我们使用默认配置创建一个窗口并检查一些其属性::

    >>> import pyglet
    >>> window = pyglet.window.Window()
    >>> context = window.context
    >>> config = context.config
    >>> config.double_buffer
    c_int(1)
    >>> config.stereo
    c_int(0)
    >>> config.sample_buffers
    c_int(0)

请注意，配置属性的值都是 ctypes 实例。
这是因为 pyglet 未指定配置。 相反，它一直是由 Pyglet 从系统支持的配置列表中选择。 
您可以不保证给定的配置在系统上有效，除非它是由系统提供给您。

Pyglet 通过以下方式简化了选择系统配置之一的过程允许您创建一个“模板”配置，
该配置仅指定您的值感兴趣。 
看 :ref:`guide_simple-context-configuration` 了解详情.


.. _guide_displays:

Display
^^^^^^^

系统实际上可能支持几组不同的配置，具体取决于正在使用哪个显示设备。 
例如，具有两个视频的计算机卡可能不支持每个卡上的相同配置。 
另一个例子是使用X11 远程：显示设备将支持与本地驱动程序。 
甚至本地计算机上的单个视频卡也可能支持插入的两个显示器的不同配置。

在 pyglet, 一个 :class:`~pyglet.canvas.Display` 是“screen”的集合连接到单个显示设备。 
在 Linux 上，显示设备对应于到正在使用的 X11 显示器。 在Windows和Mac OS X上，只有一个
显示 (由于这些操作系统将多个视频卡显示为单个虚拟设备).

:mod:`pyglet.canvas` 模块提供对显示器的访问。使用
:func:`~pyglet.canvas.get_display` 获取默认显示的函数::

    >>> display = pyglet.canvas.get_display()

.. note::

    在 X11 上，您可以使用 :class:`~pyglet.canvas.Display` 类直接到
    指定要使用的显示字符串，例如使用远程连接的显示。 
    名称字符串的格式与 ``DISPLAY``环境变量::

        >>> display = pyglet.canvas.Display(name=':1')

    如果您有多个物理屏幕并且正在使用 Xinerama，请参阅
    :ref:`guide_screens` 以选择所需的屏幕，就像在 Windows 中一样
    和 Mac OS X。否则，您可以通过 ``x_screen`` 参数::

        >>> display = pyglet.canvas.Display(name=':1', x_screen=1)

.. _guide_screens:

Screens
^^^^^^^

获得显示后，可以枚举连接的屏幕。 
屏幕是连接到显示设备的物理显示介质，例如计算机显示器、电视或投影仪。 
大多数计算机将具有单个屏幕，但是双头工作站和连接到投影仪的笔记本电脑是存在多个屏幕的常见情况。

在以下示例中，列出了双头工作站的屏幕::

    >>> for screen in display.get_screens():
    ...     print(screen)
    ...
    XlibScreen(screen=0, x=1280, y=0, width=1280, height=1024, xinerama=1)
    XlibScreen(screen=0, x=0, y=0, width=1280, height=1024, xinerama=1)

由于此工作站运行的是 Linux，因此返回的屏幕是``XlibScreen``，
一个 :class:`~pyglet.canvas.Screen` 的子类。 
``screen`` 和 ``xinerama`` 属性特定于 Linux，但
:attr:`~pyglet.canvas.Screen.x`, :attr:`~pyglet.canvas.Screen.y`,
:attr:`~pyglet.canvas.Screen.width` 和
:attr:`~pyglet.canvas.Screen.height` 属性存在于所有屏幕上，并描述屏幕的几何形状，如下所示。

.. figure:: img/screens.png

    屏幕及其报告的几何形状的排列示例。 请注意，主显示器（标记为“1”）根据此特定用户的偏好位于右侧。

始终有一个“默认”屏幕，这是返回的第一个屏幕 :meth:`~pyglet.canvas.Display.get_screens`.  
根据操作系统的不同，默认屏幕通常是包含任务栏（在Windows上）或菜单栏（在OS X上）的屏幕。
您可以使用以下方法直接访问此屏幕 :meth:`~pyglet.canvas.Display.get_default_screen`.


.. _guide_glconfig:

OpenGL 配置选项
----------------------------

当配置或选择一个 :class:`~pyglet.gl.Config` 时, 您可以根据该配置的属性执行此操作。 
PYGLET 支持 AGL、GLX、WGL 及其扩展提供的固定选项子集。 特别是，这些约束被放置在所有OpenGL配置上:

* 缓冲区始终是组件（RGB 或 RGBA）颜色，从不为调色板编制索引。
* 缓冲区的“级别”始终为 0（无论如何，现代 OpenGL 驱动程序在很大程度上不支持此参数）。
* 无法设置缓冲区的透明颜色（同样，此特定于 GLX 的选项没有得到很好的支持）。
* 不支持 pbuffers（使用帧缓冲区对象可以更简单、更高效地实现等效功能）。

缓冲区的可见部分（有时称为颜色缓冲区）配置了以下属性:

    ``buffer_size``
        每个样本的位数。 常用值为 24 和 32，每个颜色分量专用 8 位。 
        缓冲区大小也可以为 16，通常分别对应于 5、6 和 5 位红色、绿色和蓝色。

        通常不需要设置此属性，因为默认情况下，设备驱动程序将选择与当前显示模式兼容的缓冲区大小。

    ``red_size``, ``blue_size``, ``green_size``, ``alpha_size``
        这些都给出了专用于其各自颜色分量的位数。 
        应避免设置任何红色、绿色或蓝色大小，因为这些大小由驱动程序根据 ``buffer_size`` 属性确定。

        如果在颜色缓冲区中需要 Alpha 通道（例如，如果要在多个通道中合成），则应指定 ``alpha_size=8`` 以确保创建此通道。

    ``sample_buffers`` 和 ``samples``
        为多重采样 （MSAA） 配置缓冲区，其中使用多个颜色样本来确定每个像素的颜色，从而获得更高质量的抗锯齿图像。

        通过设置 ``sample_buffers=1`` 启用多重采样 （MSAA），然后给出要在 ``samples`` 中使用的每个像素的样本数。
        例如， ``samples=2`` 是速度最快、质量最低的多重采样配置。
        ``samples=4`` 仍然得到广泛支持，即使在Intel HD和AMD Vega上也相当高性能。
        大多数现代 GPU 支持 2x、4x、8x 和 16x MSAA 样本，性能相当高。

    ``stereo``
        创建单独的左右缓冲区，用于立体声硬件。只有专用视频硬件（如立体眼镜）才会支持此选项。 
        使用时，您需要手动渲染到每个缓冲区， `glDrawBuffers`。

    ``double_buffer``
        创建单独的前端和后端缓冲区。 
        如果没有双缓冲，绘图命令会立即在屏幕上可见，并且当图像在他们面前重新绘制时，用户会注意到可见的闪烁。

        建议设置 ``double_buffer=True`` ，这将创建一个单独的隐藏缓冲区，用于执行绘图。 
        调用 `Window.flip` 时，缓冲区将被交换，使新绘图几乎立即可见。

除了颜色缓冲区之外，还可以根据这些属性的值选择性地创建其他几个缓冲区：

    ``depth_size``
        3D 渲染通常需要深度缓冲区。 典型的深度大小为 24 位。 如果不需要深度缓冲区，请指定 ``0`` 。

    ``stencil_size``
        模板缓冲区是屏蔽其他缓冲区和实现某些体积阴影算法所必需的。 典型的模板大小为 8 位;或者，如果不需要，请指定 ``0``。

    ``accum_red_size``, ``accum_blue_size``, ``accum_green_size``, ``accum_alpha_size``
        累积缓冲区可用于简单的抗锯齿、景深、运动模糊和其他合成操作。 
        如今，它的使用正在被浮点纹理的使用所取代，但它仍然是在旧硬件上实现这些效果的实用解决方案。

        如果需要累积缓冲区，请为每个属性指定 ``8`` （当然，alpha 组件是可选的）。
        
    ``aux_buffers``
        每个辅助缓冲区的配置与颜色缓冲区相同。       
        通常最多可以创建四个辅助缓冲区。 
        如果不需要任何辅助缓冲区，请指定 ``0`` 。

        与累积缓冲区一样，辅助缓冲区现在很少使用，因为可以使用更有效的技术，例如渲染到纹理。 
        但是，它们几乎在较旧的硬件上普遍可用，而较新的技术是不可能的。

如果您希望直接使用 OpenGL，您可以请求更高级别的上下文。
如果您希望使用现代 OpenGL 可编程管道，这是必需的。
但请注意，pyglet 目前在其许多内部模块（例如文本、图形和精灵模块）中使用了传统的 OpenGL 功能。
请求更高版本的上下文当前将阻止使用这些模块。

    ``major_version``
        对于 OpenGL 3.x 或 4.x 上下文，这将是 3 或 4。

    ``minor_version``
        请求的上下文次要版本。在某些情况下，OpenGL 驱动程序可能会返回比请求更高的版本。

    ``forward_compatible``
        将其设置为 `True` 将要求驱动程序从上下文中排除旧版 OpenGL 功能。Khronos 不建议使用此选项。

.. note::
    要在 Mac OSX 上请求更高版本的 OpenGL 上下文，必须禁用 pyglet 阴影上下文。
    为此，请将 pyglet 选项 ``pyglet.options['shadow_window']`` 设置为  ``False`` ，在创建窗口之前，或导入 ``pyglet.window`` 。

The default configuration
^^^^^^^^^^^^^^^^^^^^^^^^^

If you create a :class:`~pyglet.window.Window` without specifying the context
or config, pyglet will use a template config with the following properties:

    .. list-table::
        :header-rows: 1

        * - Attribute
          - Value
        * - double_buffer
          - True
        * - depth_size
          - 24

.. _guide_simple-context-configuration:

Simple context configuration
----------------------------

A context can only be created from a config that was provided by the system.
Enumerating and comparing the attributes of all the possible configs is
a complicated process, so pyglet provides a simpler interface based on
"template" configs.

To get the config with the attributes you need, construct a
:class:`~pyglet.gl.Config` and set only the attributes you are interested in.
You can then supply this config to the :class:`~pyglet.window.Window`
constructor to create the context.

For example, to create a window with an alpha channel::

    config = pyglet.gl.Config(alpha_size=8)
    window = pyglet.window.Window(config=config)

It is sometimes necessary to create the context yourself, rather than letting
the :class:`~pyglet.window.Window` constructor do this for you.  In this case
use :meth:`~pyglet.canvas.Screen.get_best_config` to obtain a "complete"
config, which you can then use to create the context::

    display = pyglet.canvas.get_display()
    screen = display.get_default_screen()

    template = pyglet.gl.Config(alpha_size=8)
    config = screen.get_best_config(template)
    context = config.create_context(None)
    window = pyglet.window.Window(context=context)

Note that you cannot create a context directly from a template (any
:class:`~pyglet.gl.Config` you constructed yourself).  The
:class:`~pyglet.window.Window` constructor performs a similar process to the
above to create the context if a template config is given.

Not all configs will be possible on all machines.  The call to
:meth:`~pyglet.canvas.Screen.get_best_config` will raise
:class:`~pyglet.window.NoSuchConfigException` if the hardware does not
support the requested attributes.  It will never return a config that does not
meet or exceed the attributes you specify in the template.

You can use this to support newer hardware features where available, but also
accept a lesser config if necessary.  For example, the following code creates
a window with multisampling if possible, otherwise leaves multisampling off::

    template = pyglet.gl.Config(sample_buffers=1, samples=4)
    try:
        config = screen.get_best_config(template)
    except pyglet.window.NoSuchConfigException:
        template = gl.Config()
        config = screen.get_best_config(template)
    window = pyglet.window.Window(config=config)

Selecting the best configuration
--------------------------------

Allowing pyglet to select the best configuration based on a template is
sufficient for most applications, however some complex programs may want to
specify their own algorithm for selecting a set of OpenGL attributes.

You can enumerate a screen's configs using the
:meth:`~pyglet.canvas.Screen.get_matching_configs` method. You must supply a
template as a minimum specification, but you can supply an "empty" template
(one with no attributes set) to get a list of all configurations supported by
the screen.

In the following example, all configurations with either an auxiliary buffer
or an accumulation buffer are printed::

    display = pyglet.canvas.get_display()
    screen = display.get_default_screen()

    for config in screen.get_matching_configs(gl.Config()):
        if config.aux_buffers or config.accum_red_size:
            print(config)

As well as supporting more complex configuration selection algorithms,
enumeration allows you to efficiently find the maximum value of an attribute
(for example, the maximum samples per pixel), or present a list of possible
configurations to the user.

Sharing objects between contexts
--------------------------------

Every window in pyglet has its own OpenGL context.  Each context has its own
OpenGL state, including the matrix stacks and current flags.  However,
contexts can optionally share their objects with one or more other contexts.
Shareable objects include:

* Textures
* Display lists
* Shader programs
* Vertex and pixel buffer objects
* Framebuffer objects

There are two reasons for sharing objects.  The first is to allow objects to
be stored on the video card only once, even if used by more than one window.
For example, you could have one window showing the actual game, with other
"debug" windows showing the various objects as they are manipulated.  Or, a
set of widget textures required for a GUI could be shared between all the
windows in an application.

The second reason is to avoid having to recreate the objects when a context
needs to be recreated.  For example, if the user wishes to turn on
multisampling, it is necessary to recreate the context.  Rather than destroy
the old one and lose all the objects already created, you can

1. Create the new context, sharing object space with the old context, then
2. Destroy the old context.  The new context retains all the old objects.

pyglet defines an :class:`~pyglet.gl.ObjectSpace`: a representation of a
collection of objects used by one or more contexts.  Each context has a single
object space, accessible via its
:py:attr:`~pyglet.gl.base.Context.object_space` attribute.

By default, all contexts share the same object space as long as at least one
context using it is "alive".  If all the contexts sharing an object space are
lost or destroyed, the object space will be destroyed also.  This is why it is
necessary to follow the steps outlined above for retaining objects when a
context is recreated.

pyglet creates a hidden "shadow" context as soon as :mod:`pyglet.gl` is
imported. By default, all windows will share object space with this shadow
context, so the above steps are generally not needed. The shadow context also
allows objects such as textures to be loaded before a window is created (see
``shadow_window`` in :data:`pyglet.options` for further details).

When you create a :class:`~pyglet.gl.Context`, you tell pyglet which other
context it will obtain an object space from.  By default (when using the
:class:`~pyglet.window.Window` constructor
to create the context) the most recently created context will be used.  You
can specify another context, or specify no context (to create a new object
space) in the :class:`~pyglet.gl.Context` constructor.

It can be useful to keep track of which object space an object was created in.
For example, when you load a font, pyglet caches the textures used and reuses
them; but only if the font is being loaded on the same object space.  The
easiest way to do this is to set your own attributes on the
:py:class:`~pyglet.gl.ObjectSpace` object.

In the following example, an attribute is set on the object space indicating
that game objects have been loaded.  This way, if the context is recreated,
you can check for this attribute to determine if you need to load them again::

    context = pyglet.gl.current_context
    object_space = context.object_space
    object_space.my_game_objects_loaded = True

Avoid using attribute names on :class:`~pyglet.gl.ObjectSpace` that begin with
``"pyglet"``, as they may conflict with an internal module.
