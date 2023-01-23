文档
=============

这是 pyglet 文档，生成于 `Sphinx`_.

.. _Sphinx: https://sphinx-doc.org

.. _reStructuredText: http://www.sphinx-doc.org/en/stable/rest.html

.. _autodoc: http://www.sphinx-doc.org/en/stable/ext/autodoc.html

详情:

.. include:: build.rst

.. note::

    对于错误请参阅的 `Sphinx warnings log file <../warnings.txt>`_ .


编写文档
---------------------

熟悉 `Sphinx`_ 和 `reStructuredText`_.

文献
^^^^

主页是 ``pyglet/doc/index.rst``. 此文件创建三个目录树:

* 编程指南
* API接口文档
* 您现在正在阅读的开发指南

源代码
-----------

API 文档由源代码文档字符串生成，通过
`autodoc`_ 和一些自定义扩展。

:Example:

   .. code-block:: python

      class Class1():
      '''Short description.

      Detailed explanation, formatted as reST.
      Can be as detailed as it is needed.

      :Ivariables:
         `arg1`
             description

      .. versionadded:: 1.2

      '''

      attribute1 = None
      '''This is an attribute.

      More details.
      '''

      #: This is another attribute.
      attribute2 = None

      def __init__(self):
          '''Constructor

          :parameters:
             `arg1` : type
                description
          '''

          self.instance_attribute = None
          '''This is an instance attribute.
          '''

      def method(self):
          '''Short description.

          :returns: return description
          :rtype: returned type
          '''

      def _get_property1(self):
          '''Getter Method contains docstrings for a property

          :return: property1 value
          :rtype: property1 type
          '''

      def _set_property1(self, value):
          '''Setter Method docstrings are ignored
          '''

      property1 = property(_get_property1, _set_property1,
                        doc='''Override docstring here if you want''')


Pyglet 在弃用方面具有特殊作用, ``:deprecated:``.

    .. list-table::
        :header-rows: 1

        * - Source
          - Output
        * - ``:deprecated: Do not use``
          - .. warning:: Deprecated. Do not use


构建
--------

完整的文档可以使用 ``sphinx``.
确保按照中所述准备环境 :doc:`virtualenv`.

要构建文档，请执行::

   ./make.py docs --open

.. note ::
   由于 Sphinx 中的错误，文档生成目前仅适用于 Python 3.x.

如果生成成功，则网页位于 ``doc/_build/html``.

（可选）构建文档的独立方法是通过
``setup.py`` 或 ``make``.

.. code:: bash

    # using setup.py (output dir: _build in project root)
    python setup.py build_sphinx

    # make (make.bat for windows)
    cd doc
    make html

HTML主题
----------

.. Note:: 自定义主题在 2019 年被禁用，并替换为
          标准版“阅读文档”主题 ``sphinx_rtd_theme``.

自定义的 sphinx 主题位于 ``ext/theme`` 文件夹中.
