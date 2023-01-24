.. _guide_working-with-the-keyboard:

使用键盘
=======

pyglet 支持适用于游戏的低级键盘输入以及独立于区域设置和设备的 Unicode 文本输入。

键盘输入需要一个具有焦点的窗口。 操作系统通常决定哪个应用程序窗口具有键盘焦点。 
通常，此窗口显示在所有其他窗口之上，并且可能以不同的方式装饰，尽管这是特定于平台的（例如，某些 Linux 窗口管理器将键盘焦点与鼠标指针耦合）。

您可以使用 :py:meth:`~pyglet.window.Window.activate` 方法为窗口请求键盘焦点，但您不应该依赖它——它可能只是为用户提供一个视觉提示，指示窗口需要用户输入，而没有实际获得焦点。


使用 :py:attr:`~pyglet.window.Window.WINDOW_STYLE_BORDERLESS` 或 :py:attr:`~pyglet.window.Window.WINDOW_STYLE_TOOL` 样式创建的 Windows 无法接收键盘焦点。

如果没有窗口，则无法使用 pyglet 的键盘或文本事件，考虑改用 Python 内置函数，例如 ``input`` 。

键盘事件
-------

:py:meth:`~pyglet.window.Window.on_key_press` 和 :py:meth:`~pyglet.window.Window.on_key_release` 事件分别在按下或松开键盘上的任何键时触发。 
这些事件不受“按键重复”的影响 - 一旦按下一个键，该键在释放之前不会再有事件。

两个事件都由相同的参数参数化::

    def on_key_press(symbol, modifiers):
        pass

    def on_key_release(symbol, modifiers):
        pass

定义的虚拟符号
^^^^^^^^^^^^^

`symbol` 参数是一个整数，表示“虚拟”键代码。
它*不*对应于任何特定的编号方案，特别是该符号*不是*ASCII字符代码。

Pyglet 具有独立于硬件和平台的键符号，适用于多种类型的键盘。 
这些在 :py:mod:`pyglet.window.key` 中定义为常量。 例如，拉丁语-1字母表只是字母本身::

    key.A
    key.B
    key.C
    ...

数字键具有下划线，以使其成为有效的标识符::

    key._1
    key._2
    key._3
    ...

通过名称标识各种控制和方向键::

    key.ENTER or key.RETURN
    key.SPACE
    key.BACKSPACE
    key.DELETE
    key.MINUS
    key.EQUAL
    key.BACKSLASH

    key.LEFT
    key.RIGHT
    key.UP
    key.DOWN
    key.HOME
    key.END
    key.PAGEUP
    key.PAGEDOWN

    key.F1
    key.F2
    ...

数字键盘上的键具有单独的符号::

    key.NUM_1
    key.NUM_2
    ...
    key.NUM_EQUAL
    key.NUM_DIVIDE
    key.NUM_MULTIPLY
    key.NUM_SUBTRACT
    key.NUM_ADD
    key.NUM_DECIMAL
    key.NUM_ENTER

某些修饰键的左侧和右侧都有单独的符号（但是它们不能在所有平台上区分，包括Mac OSX）::

    key.LCTRL
    key.RCTRL
    key.LSHIFT
    key.RSHIFT
    ...

关键符号与任何处于活动状态的修饰符无关。 例如，小写和大写字母都会生成“A”符号。 数字键盘也是如此。

修饰符
^^^^^^^^^

生成事件时处于活动状态的修饰符以按位方式组合，并在 ``modifiers`` 参数中提供。 在 :py:mod:`pyglet.window.key` 中定义的修饰符常量是::

    MOD_SHIFT
    MOD_CTRL
    MOD_ALT         在 Mac OS X 上不可用
    MOD_WINDOWS     仅在 Windows 上可用
    MOD_COMMAND     仅在 Mac OS X 上可用
    MOD_OPTION      仅在 Mac OS X 上可用
    MOD_CAPSLOCK
    MOD_NUMLOCK
    MOD_SCROLLLOCK
    MOD_ACCEL       相当于 Mac OS X 上的 MOD_CTRL 或MOD_COMMAND。

例如，测试是否按住 shift 键::

    if modifiers & MOD_SHIFT:
        pass

与相应的按键不同，无法确定是按住左修饰符还是右修饰符（尽管您可以通过自己跟踪键状态来模拟此行为）。

用户定义按键
^^^^^^^^^^^^^^^

Pyglet 并没有为每个键盘定义键符号。 
例如，非拉丁语言将有许多 pyglet 无法识别的键（但是，它们的 Unicode 表示仍然有效，请参阅 :ref:`guide_text-and-motion-events` ）。
即使是英文键盘也经常有制造商添加的额外所谓“OEM”键，例如，这些键可能被标记为“媒体”、“音量”或“购物”。

在这些情况下，pyglet 将根据密钥的硬件扫描码在运行时创建一个密钥符号。 这保证对于该型号的键盘是唯一的，但在具有相同标记键的其他键盘之间可能不一致。

使用这些键的最佳方法是记录用户在提示后按下的内容，然后检查相同的键符号。 许多商业游戏在允许玩家设置自己的键绑定方面具有类似的功能。

记住按键状态
^^^^^^^^^^^

pyglet 提供了方便类 :py:class:`~pyglet.window.key.KeyStateHandler` 来存储当前的键盘状态。 
这可以推送到任何窗口的事件处理程序堆栈上，然后作为字典进行查询::

    from pyglet.window import key

    window = pyglet.window.Window()
    keys = key.KeyStateHandler()
    window.push_handlers(keys)

    # Check if the spacebar is currently pressed:
    if keys[key.SPACE]:
        pass

.. _guide_text-and-motion-events:

文本和运动事件
-------------

pyglet 将用户按下的键与输入的 Unicode 文本分离。 这样做有几个好处：

* 将修饰符和键符号映射到 Unicode 字符的复杂任务会自动正确处理。
* 按键重复将应用于根据用户的操作系统首选项按住的按键。
* 死键和撰写键会自动解释以生成变音符号或组合字符。
* 键盘输入可以通过输入调色板路由，例如输入来自亚洲语言的字符。
* 文本输入可以来自其他用户定义的源，例如手写或语音识别。

实际的输入源（即，按下了哪些键，或者使用了什么输入法）应该在应用程序范围之外考虑——操作系统提供必要的服务。

将文本输入到窗口中时，将触发 :py:meth:`~pyglet.window.Window.on_text` 事件::

    def on_text(text):
        pass

提供的唯一参数是 Unicode 字符串。
对于键盘输入，这通常是一个字符长，但是更复杂的输入法（如输入调色板）可能会一次提供整个单词或短语。

当您需要从一系列击键中确定字符串时，应始终使用 :py:meth:`~pyglet.window.Window.on_text` 事件。
相反，当您需要按下按键（例如，控制游戏中玩家的移动）时，切勿使用 :py:meth:`~pyglet.window.Window.on_text`。

运动事件
^^^^^^^

除了输入文本外，用户还可以按键盘上的键，根据根深蒂固的约定在文本小部件中导航。 例如，按向左箭头键可将光标向左移动一个字符。

虽然您可能想使用 :py:meth:`~pyglet.window.Window.on_key_press` 事件来捕获这些事件，但存在一些问题：

* 不会为 :py:meth:`~pyglet.window.Window.on_key_press` 生成键重复事件，但用户期望按住左箭头键最终会将字符移动到行首。
* 不同的操作系统对键的行为有不同的约定。例如，在 Windows 上，Home 键通常将光标移动到行首，而在 Mac OS X 上，相同的键移动到文档的开头。

Pyglet 窗口提供 :py:meth:`~pyglet.window.Window.on_text_motion` 事件，该事件通过抽象出按键并仅为应用程序提供预期的光标运动来解决这些问题
pyglet windows provide the :py:meth:`~pyglet.window.Window.on_text_motion`::

    def on_text_motion(motion):
        pass

`motion` 是一个整数，它是在 :py:mod:`pyglet.window.key` 中定义的常量。下表显示了每个操作系统上定义的文本动作及其键盘映射。

    .. list-table::
        :header-rows: 1

        * - Constant
          - Behaviour
          - Windows/Linux
          - Mac OS X
        * - ``MOTION_UP``
          - Move the cursor up
          - Up
          - Up
        * - ``MOTION_DOWN``
          - Move the cursor down
          - Down
          - Down
        * - ``MOTION_LEFT``
          - Move the cursor left
          - Left
          - Left
        * - ``MOTION_RIGHT``
          - Move the cursor right
          - Right
          - Right
        * - ``MOTION_PREVIOUS_WORD``
          - Move the cursor to the previous word
          - Ctrl + Left
          - Option + Left
        * - ``MOTION_NEXT_WORD``
          - Move the cursor to the next word
          - Ctrl + Right
          - Option + Right
        * - ``MOTION_BEGINNING_OF_LINE``
          - Move the cursor to the beginning of the current line
          - Home
          - Command + Left
        * - ``MOTION_END_OF_LINE``
          - Move the cursor to the end of the current line
          - End
          - Command + Right
        * - ``MOTION_PREVIOUS_PAGE``
          - Move to the previous page
          - Page Up
          - Page Up
        * - ``MOTION_NEXT_PAGE``
          - Move to the next page
          - Page Down
          - Page Down
        * - ``MOTION_BEGINNING_OF_FILE``
          - Move to the beginning of the document
          - Ctrl + Home
          - Home
        * - ``MOTION_END_OF_FILE``
          - Move to the end of the document
          - Ctrl + End
          - End
        * - ``MOTION_BACKSPACE``
          - Delete the previous character
          - Backspace
          - Backspace
        * - ``MOTION_DELETE``
          - Delete the next character, or the current character
          - Delete
          - Delete

键盘独占性
---------

某些按键或组合键通常会绕过应用程序，并由操作系统处理。 
一些示例是用于切换应用程序的 Alt+Tab（Mac OS X 上的 Command+Tab）和映射到 Mac OS X 上的“公开”的键。

您可以禁用这些热键，并让它们像应用程序的普通击键一样运行。 
如果您正在开发不应关闭的展台应用程序，或者用户可能会意外按下这些键之一的游戏，这将非常有用。

要启用此模式，请调用 :py:meth:`~pyglet.window.Window.set_exclusive_keyboard` 以获取应应用此模式的窗口。 
在 Mac OS X 上，当专用键盘被激活时，扩展坞和菜单栏将滑出视图。

以下限制适用于 Windows：

* 大多数键未禁用：用户仍然可以使用 Ctrl+Escape、Alt+Escape、Windows 键或 Ctrl+Alt+Delete 切换离开应用程序。 仅禁用 Alt+Tab 组合。

以下限制适用于 Mac OS X：

* 电源键未禁用。

不建议对常规发布应用程序或游戏使用此函数，因为它违反了用户界面约定。
