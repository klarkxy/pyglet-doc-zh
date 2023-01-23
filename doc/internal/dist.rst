制作 pyglet 发行
=======================

#. 将 pyglet 克隆到新目录中

#. 确保它是最新的::

    git pull

#. 更新以下文件中的版本字符串并提交:

   * pyglet/__init__.py
   * doc/conf.py

#. 使用版本号标记当前变更列表::

    git tag -a v0.0.0 -m "release message"

#. 将更改推送到中央存储库::

    git push
    git push --tags

#. 构建 wheels 和文档::

    ./make.py clean
    ./make.py dist

#. 上传 wheels 和 zips 到 PyPI::

    twine upload dist/pyglet-x.y.z*

#. 开始构架文档在 https://readthedocs.org/projects/pyglet/builds/ 上

#. 使用相同的版本号在 Github 上起草新版本 https://github.com/pyglet/pyglet/releases

#. 告诉别人!

主要版本增加
----------------------
在准备主要版本时，您可能还需要考虑以后:

* 为主要版本创建维护分支
* 为该维护分支添加 readthedocs 配置
* 将 URL 指向维护分支文档 setup.py

