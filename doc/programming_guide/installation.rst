安装
============

.. note:: 这些说明适用于 pyglet |version|.

Pyglet 是一个纯 Python 库，因此安装时不需要特殊的步骤或编译。
您可以通过多种方式安装它，或者只是将 `pyglet` 文件夹直接复制到您的项目中。
如果您不确定该怎么做，建议的方法是将其安装到本地“站点包”目录中。
pyglet 在 `on PyPI <https://pypi.python.org/pypi/pyglet>`_ 上可用。
通过 **PIP** 轻松安装：

.. code-block:: sh

    pip install pyglet --user

您还可以使用 **git** 克隆存储库并从源代码安装：

.. code-block:: sh

    git clone https://github.com/pyglet/pyglet.git
    cd pyglet
    git checkout pyglet-1.5-maintenance
    python setup.py install --user


如果要播放视频或多种压缩音频，pyglet 可以选择使用 `FFmpeg <https://www.ffmpeg.org/download.html>`_.


运行示例
-------

源代码存档包括示例。档案是
`available on Github <https://github.com/pyglet/pyglet/releases/>`_:

.. code-block:: sh

    unzip pyglet-x.x.x.zip
    cd pyglet-x.x.x
    python examples/hello_world.py


如上所述，您还可以使用 Git 克隆存储库：

.. code-block:: sh

    git clone https://github.com/pyglet/pyglet.git
    cd pyglet
    git checkout pyglet-1.5-maintenance
    python examples/hello_world.py
