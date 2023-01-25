使用其他输入设备
================================

pyglet 的:py:mod:`~pyglet.input` 模块允许您接受来自任何 USB 人机接口设备 （HID） 的输入。
提供了用于使用游戏控制器、操纵杆和 Apple 遥控器的高级抽象。
游戏控制器抽象适用于现代游戏手柄，例如在家用视频游戏机上找到的游戏手柄。
操纵杆抽象更加通用，适用于具有任意数量的按钮、轴和帽子的设备。
这包括方向盘、用于飞行模拟器的操纵杆等设备，以及几乎任何其他设备。
对于大多数类型的游戏，控制器抽象通常是更好的选择。对于高级用例，也可以直接访问低级设备。

:py:mod:`~pyglet.input` 模块提供了几种查询设备的方法，以及一个 ControllerManager 类来支持控制器的热插拔0::

    # 获取所有低级输入设备的列表:
    devices = pyglet.input.get_devices()

    # 获取所有控制器的列表:
    controllers = pyglet.input.get_controllers()

    # 获取所有操纵杆的列表:
    joysticks = pyglet.input.get_controllers()

    # 获取触摸屏列表:
    tablets = pyglet.input.get_tablets()

    # 获取苹果遥控器（如果有）:
    remote = pyglet.input.get_apple_remote()

    # 创建控制器管理器实例:
    controller_manager = pyglet.input.ControllerManager()


使用控制器
---------

控制器具有一组严格定义的输入，模仿流行的视频游戏机控制器的布局。
这包括两个模拟摇杆、一个方向键 （dpad）、面部和肩部按钮，以及开始/返回/指南和摇杆按下按钮。
许多控制器还包括播放隆隆声效果（振动）的能力。
以下平台接口用于控制器支持：

    .. list-table::
        :header-rows: 1

        * - platform
          - interface
          - notes

        * - Linux
          - evdev
          -

        * - Windows
          - DirectInput & Xinput
          - rumble not implemented on DirectInput

        * - MacOSX
          - IOKit
          - rumble not yet implemented

在使用控制器之前，必须找到它并将其打开。
pyglet提供了一个控制器管理器类以允许控制器的热插拔，但也可以手动打开它们。
要手动获取当前连接到计算机的所有控制器的列表，请调用 pyglet.input.get_controllers()::

    controllers = pyglet.input.get_controllers()

然后从列表中选择一个控制器并调用 `Controller.open()` 将其打开::

    if controllers:
        controller = controllers[0]

    controller.open()

打开后，您可以开始从输入接收数据。
定义了各种模拟和数字 :py:class:`~pyglet.input.Control` 类型，这些类型会自动规范化为一致的范围。提供以下模拟控件：

    .. list-table::
        :header-rows: 1

        * - name
          - type
          - range

        * - leftx
          - float
          - -1,1

        * - lefty
          - float
          - -1,1

        * - rightx
          - float
          - -1,1

        * - righty
          - float
          - -1,1

        * - lefttrigger
          - float
          - 0,1

        * - lefttrigger
          - float
          - 0,1

提供以下数字控件:

    .. list-table::
        :header-rows: 1

        * - Name
          - notes
        * - a
          - the "south" face button
        * - b
          - the "east" face button
        * - x
          - the "west" face button
        * - y
          - the "north" face button
        * - leftshoulder
          -
        * - rightshoulder
          -
        * - start
          - called "options" on some controllers
        * - back
          - called "select" or "share" on some controllers
        * - guide
          - usually in the center, with a company logo
        * - leftstick
          - pressing in the left analog stick
        * - rightstick
          - pressing in the right analog stick
        * - dpleft
          -
        * - dpright
          -
        * - dpup
          -
        * - dpdown
          -

可以通过两种方式读取这些值。上面列出的所有控件都是控制器实例上的属性，因此可以在游戏循环中手动查询它们::

    controller_instance.a       # boolean
    controller_instance.leftx   # float


或者，由于控制器是 :py:class:`~pyglet.event.EventDispatcher` 的子类，因此当任何值更改时将调度事件。
这通常是处理输入的推荐方法，因为它减少了由于轮询缓慢而导致“错过”按钮按下的机会。不同的控件分为以下事件类型:

    .. list-table::
        :header-rows: 1

        * - Event
          - Arguments
          - types

        * - on_button_press
          - controller, button_name
          - :py:class:`~pyglet.input.Controller`, `str`

        * - on_button_release
          - controller, button_name
          - :py:class:`~pyglet.input.Controller`, `str`

        * - on_stick_motion
          - controller, stick_name, x_value, y_value
          - :py:class:`~pyglet.input.Controller`, `str`, `float`, `float`

        * - on_dpad_motion
          - controller, left, right, up, down
          - :py:class:`~pyglet.input.Controller`, `bool`, `bool`, `bool`, `bool`

        * - on_trigger_motion
          - controller, trigger_name, value
          - :py:class:`~pyglet.input.Controller`, `str`, `float`


以下是处理模拟事件的方法::

    @controller.event
    def on_stick_motion(controller, name, x_value, y_value):
        # Optionally enforce a "deadzone"

        if name == "leftstick":
            # Do something with the x/y_values
        elif name == "rightstick":
            # Do something with the x/y_values

    @controller.event
    def on_trigger_motion(controller, name, value):

        if name == "lefttrigger":
            # Do something with the value
        elif name == "righttrigger":
            # Do something with the value

以下是如何处理数字事件::

    @controller.event
    def on_button_press(controller, button_name):
        if button_name == 'a':
            # start firing
        elif button_name == 'b':
            # do something else


    @controller.event
    def on_button_release(controller, button_name):
        if button_name == 'a':
            # stop firing
        elif button_name == 'b':
            # do something else

最后，方向键事件可以这样处理::

    @controller.event
    def on_dpad_motion(controller, dpleft, dpright, dpup, dpdown):
        if dpup:
            # move up
        if dpdown:
            # move down
        if dpleft:
            # move left
        if dpright:
            # move right

震动
^^^^

许多控制器还支持播放隆隆声（振动）效果。有强效和弱效，可以独立播放::

    controller.rumble_play_weak(strength, duration=0.5)
    controller.rumble_play_strong(strength, duration=0.5)

`strength`参数应为 0-1 的范围。超出此范围的值将被限制。可选的 `duration` 参数以秒为单位。
最长持续时间可能因平台而异，但通常至少为 5 秒。
如果在现有效果仍在播放时再次调用 play，它将替换当前效果。您也可以随时停止播放隆隆声效果::

    controller.rumble_stop_weak()
    controller.rumble_stop_strong()


控制器管理器
^^^^^^^^^^^

为了简化控制器的热插拔，可以使用 :py:class:`~pyglet.input.ControllerManager` 类。
此类有一个 `get_controllers()` 方法代替 `pyglet.input.get_controllers()` 。
还有 `on_connect`和 `on_disconnect` 事件，每当连接或断开连接控制器实例时，它们都会调度控制器实例。
如果重新连接了以前连接的控制器，则在可能的情况下将返回相同的实例。

要使用控制器管理器，请先创建一个实例::

    manager = pyglet.input.ControllerManager()

然后，您可以查询当前连接的控制器，类似于直接执行此操作::

    controllers = manager.get_controllers()

像往常一样，从列表中选择一个控制器，然后调用 `Controller.open()`将其打开::

    if controllers:
        controller = controllers[0]

    controller.open()

若要处理控制器连接，请将处理程序附加到以下方法::

    @manager.event
    def on_connect(controller):
        print(f"Connected:  {controller}")

    @manager.event
    def on_disconnect(controller):
        print(f"Disconnected:  {controller}")


.. note:: 如果您使用的是控制器管理器，则不应直接使用 `pyglet.input.get_controllers()` 。
          结果未定义。请改用 `ControllerManager.get_controllers()` 。


使用操纵杆
---------------

在使用操纵杆之前，您必须找到它并打开它。 
要获取当前连接到计算机的所有操纵杆设备的列表，请调用 :py:func:`pyglet.input.get_joysticks` ::

    joysticks = pyglet.input.get_joysticks()

然后从列表中选择一个操纵杆并调用 `Joystick.open` 以打开设备::

    if joysticks:
        joystick = joysticks[0]
    joystick.open()

操纵杆的当前位置记录在其 `x` 和 `y` 属性中，这两个属性都归一化为 -1 到 1 范围内的值。 
对于 x 轴， `x`  = -1 表示操纵杆一直向左推， `x`  = 1 表示向右推操纵杆。
对于 y 轴，值 `y` = -1 表示操纵杆被向上推，值 `y` = 1 表示操纵杆被向下推。

如果您的操纵杆有两个模拟控制器，则第二个控制器的位置通常由 `z` 和 `rz` 给出，其中 `z` 是水平轴位置， `rz` 是垂直轴位置。

操纵杆按钮的状态作为布尔值列表包含在 `buttons` 属性中。 
如果值为 True，则表示正在按下相应的按钮。 
虽然按钮在物理操纵杆上可能标记为 A、B、X 或 Y，但在访问 `buttons` 列表时，它们只是由其索引引用。
如果不测试特定的游戏杆，就无法轻松知道哪个按钮索引对应于设备上的哪个物理按钮，因此最好让用户更改按钮分配。

每个打开的操纵杆在游戏杆更改状态时调度事件。
对于按钮，每当按下操纵杆的任何按钮时发送的事件 :py:meth:`~pyglet.input.Joystick.on_joybutton_press`::

    def on_joybutton_press(joystick, button):
        pass

以及每当释放操纵杆的任何按钮时都会发送事件 :py:meth:`~pyglet.input.Joystick.on_joybutton_release`::

    def on_joybutton_release(joystick, button):
        pass

:py:class:`~pyglet.input.Joystick` 参数是 :py:class:`~pyglet.input.Joystick` 实例，其按钮状态已更改（如果连接了多个操纵杆，则很有用）。
`button` 参数表示哪个按钮发生了变化，只是一个整数值，即 `button` 列表中相应按钮的索引。

对于大多数游戏，最好使用 `x` 和 `y` 属性直接检查操纵杆的当前位置。 
但是，如果您希望在这些值更改时收到通知，则应处理 :py:meth:`~pyglet.input.Joystick.on_joyaxis_motion`事件::

    def on_joyaxis_motion(joystick, axis, value):
        pass

:py:class:`~pyglet.input.Joystick` 参数再次告诉您哪个操纵杆设备发生了变化。 
`axis` 参数是字符串，例如 `x` 、 `y` 或 `rx` ，告诉您哪个轴更改了值。 
`value`给出了轴的当前归一化值，范围在 -1 到 1 之间。

如果操纵杆有帽子开关，您可以通过查看 `hat_x` 和 `hat_y` 属性来检查其当前值。 
对于两者，值为 -1、0 或 1。 请注意， `hat_y` 将在向上位置输出 1，在向下位置输出 -1，这与 y 轴控件相反。

要在帽子开关更改值时收到通知，请处理 :py:meth:`~pyglet.input.Joystick.on_joyhat_motion` 事件::

    def on_joyhat_motion(joystick, hat_x, hat_y):
        pass

`hat_x` 和 `hat_y` 参数提供的值与操纵杆的 `hat_x` 和 `hat_y` 属性相同。

使用操纵杆事件处理程序的一个好方法是在控制器类中定义它们，然后调用::

    joystick.push_handlers(my_controller)

请注意，您需要一个正在运行的应用程序事件循环，以便正确更新操纵杆按钮和轴值。
请参阅 :ref:`programming-guide-eventloop` 部分，了解有关如何启动事件循环的更多详细信息。


Using the Apple Remote
----------------------

The Apple Remote is a small infrared remote originally distributed
with the iMac.  The remote has six buttons, which are accessed with
the names `left`, `right`, `up`, `down`, `menu`, and `select`.
Additionally when certain buttons are held down, they act as virtual
buttons.  These are named `left_hold`, `right_hold`, `menu_hold`, and
`select_hold`.

To use the remote, first call :py:func:`~pyglet.input.get_apple_remote`::

    remote = pyglet.input.get_apple_remote()

Then open it::

    if remote:
        remote.open(window, exclusive=True)

The remote is opened in exclusive mode so that while we are using the
remote in our program, pressing the buttons does not activate Front
Row, or change the volume, etc. on the computer.

The following event handlers tell you when a button on the remote has
been either pressed or released::

    def on_button_press(button):
        pass

    def on_button_release(button):
        pass

The `button` parameter indicates which button changed and is a string
equal to one of the ten button names defined above: "up", "down",
"left", "left_hold", "right",  "right_hold", "select", "select_hold",
"menu", or "menu_hold".

To use the remote, you may define code for the event handlers in
some controller class and then call::

    remote.push_handlers(my_controller)


Low-level Devices
-----------------

It's usually easier to use the high-level interfaces but, for specialized
hardware, the low-level device can be accessed directly. You can query the
list of all devices, and check the `name` attribute to find the correct
device::

    for device in pyglet.input.get_devices():
        print(device.name)

After identifying the Device you wish to use, you must first open it::

    device.open()

Devices contain a list of :py:class:`~pyglet.input.Control` objects.
There are three types of controls: :py:class:`~pyglet.input.Button`,
:py:class:`~pyglet.input.AbsoluteAxis`, and :py:class:`~pyglet.input.RelativeAxis`.
For helping identify individual controls, each control has at least a
`name`, and optionally a `raw_name` attribute. Control values can by
queried at any time by checking the `Control.value` property. In addition,
every control is also a subclass of :py:class:`~pyglet.event.EventDispatcher`,
so you can add handlers to receive changes as well. All Controls dispatch the
`on_change` event. Buttons also dispatch `on_press` and `on_release` events.::

    # All controls:

    @control.event
    def on_change(value):
        print("value:", value)

    # Buttons:

    @control.event
    def on_press():
        print("button pressed")

    @control.event
    def on_release():
        print("button release")
