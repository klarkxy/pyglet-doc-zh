绘制形状
==============

.. _guide_shapes:

:py:mod:`~pyglet.shapes` 模块是一个易于使用的选项，用于创建和操作彩色形状，如矩形、圆形和线条。
在适用的情况下，可以调整形状的大小、定位和旋转形状，并且可以更改其颜色和不透明度。
所有形状都是使用 OpenGL 基元实现的，因此可以使用 :ref:`guide_batched-rendering` 有效地绘制它们。
在以下示例中，为简洁起见，将省略 `Batch` ，但通常您始终希望使用批处理渲染来提高性能。

要绘制更复杂的形状，请使用 :ref:`guide_graphics` 模块。

创建形状
----------------

可以构建具有特定位置，大小和颜色的各种形状::

    circle = shapes.Circle(x=100, y=150, radius=100, color=(50, 225, 30))
    square = shapes.Rectangle(x=200, y=200, width=200, height=200, color=(55, 55, 255))

您还可以更改颜色，或在创建后设置不透明度。不透明度可以设置为 0-255 的比例，以实现各种级别的透明度::

    circle.opacity = 120

形状的大小也可以在创建后调整::

    square.width = 200
    circle.radius = 99


锚点
^^^^

与 pyglet 中的图像类似，可以设置形状的“锚点”。
这与 x 轴和 y 轴上的形状中心有关。
对于圆，默认锚点是圆的中心。对于矩形，它是左下角。
根据您需要如何定位形状，可以更改此设置。
对于矩形，如果要旋转矩形，这将特别有用，因为形状将围绕锚点旋转。
在此示例中，将创建一个矩形，然后将锚点设置为中心::

    rectangle = shapes.Rectangle(x=400, y=400, width=100, height=50)
    rectangle.anchor_x = 50
    rectangle.anchor_y = 25
    # or, set at the same time:
    rectangle.anchor_position = 50, 25

    # The rectangle is then rotated around its anchor point:
    rectangle.rotation = 45

如果计划创建大量形状，可以选择设置默认锚点::

    shapes.Rectangle._anchor_x = 100
    shapes.Rectangle._anchor_y = 50
