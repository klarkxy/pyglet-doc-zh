# 构建此文档

## 基础

安装文档依赖：

    `pip install -r doc/requirements.txt`

The simpleast way build html docs:

最直接的构建html文档方式：

    `python setup.py build_sphinx`

生成结果将显示在项目根目录的“_build”目录中。

## 进阶

构建文档的更高级方法是使用 `make`
（windows使用 `make.bat` ）。

```bash
cd doc/
    make html
```

HTML 文档将在 `doc/_build`子目录中生成。

请运行 `make help`以获取完整的目标列表，但请注意，有些
其中可能有额外的要求。

```bash
$ make help
    Please use `make`<target>` where `<target>` is one of
    html       to make standalone HTML files
    dirhtml    to make HTML files named index.html in directories
    singlehtml to make a single large HTML file
    pickle     to make pickle files
    json       to make JSON files
    htmlhelp   to make HTML files and a HTML help project
    qthelp     to make HTML files and a qthelp project
    devhelp    to make HTML files and a Devhelp project
    epub       to make an epub
    latex      to make LaTeX files, you can set PAPER=a4 or PAPER=letter
    latexpdf   to make LaTeX files and run them through pdflatex
    text       to make text files
    man        to make manual pages
    texinfo    to make Texinfo files
    info       to make Texinfo files and run them through makeinfo
    gettext    to make PO message catalogs
    changes    to make an overview of all changed/added/deprecated items
    linkcheck  to check all external links for integrity
    doctest    to run all doctests embedded in the documentation (if enabled)
```
