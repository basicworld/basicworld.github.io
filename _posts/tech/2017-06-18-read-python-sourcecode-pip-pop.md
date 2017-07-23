---
layout: single
title: 源码阅读笔记-pip-pop
date: 2017-06-18
category: tech
tags: tech

description: 技术进阶
---
想让代码更加pythonic，最好的方法就是阅读源码，边阅读边实践。推荐知乎[Python之美](https://zhuanlan.zhihu.com/p/22275595?refer=python-cn)。这里先读一读pip-pop项目源码

这是一个很简单的单文件项目 [我是源码pip-pop](https://github.com/kennethreitz/pip-pop)，用来对比、处理requirements.txt文件--python依赖包文件

整个源码介绍如下，包括看项目的过程，先读文件，再读文件夹

## .gitignore

```python
/.tox  # 不懂
/*.egg-info  # 部署该工具的文件
*.pyc
```

自建的ignore文件，没有使用github的自带python-ignore模板

## .travis.yml

```python
sudo: false  # 是否需要sudo权限
language: python  # bin文件的执行语言
python:  # 支持的python版本
  - "2.7"
  - "3.4"
  - "3.5"
  - "pypy"
cache: pip  # 缓存，不太懂
install:  # 安装改工具的方法
  - pip install -r requirements.txt
script:  # 不懂
  - tox  # 这是啥？
```

目前还不知道用处，好像是用来配置pip-pop工具用的

## LICENSE

mit

## README.rst

同 README.md

## requirements.txt

依赖

```python
docopt==0.6.2
tox==2.3.1
```

## setup.py

配置


```python
"""
pip-pop manages your requirements files.
"""
import sys
from setuptools import setup


setup(
    name='pip-pop',  # 库名
    version='0.1.0',  # 版本
    url='https://github.com/kennethreitz/pip-pop',  # 源码地址
    license='MIT',  # license
    author='Kenneth Reitz',  # 作者
    author_email='me@kennethreitz.org',  # 邮箱
    description=__doc__.strip('\n'),  # 库描述文档，疑问：为什么去掉\n?
    #packages=[],  # 不懂
    scripts=['bin/pip-diff', 'bin/pip-grep'],  # 不懂
    #include_package_data=True,  # 不懂
    zip_safe=False,  # 不懂
    platforms='any',  # 可运行平台
    install_requires=['docopt', 'pip>=1.5.0'],  # 依赖包
    tests_require=['tox'],  # 测试依赖
    classifiers=[  # 参考：元信息
        # As from https://pypi.python.org/pypi?%3Aaction=list_classifiers
        #'Development Status :: 1 - Planning',  # 计划中
        #'Development Status :: 2 - Pre-Alpha',
        #'Development Status :: 3 - Alpha',  # 内测试
        'Development Status :: 4 - Beta',  # 对外测试版本
        #'Development Status :: 5 - Production/Stable',  # 稳定版本
        #'Development Status :: 6 - Mature', # 干啥用？
        #'Development Status :: 7 - Inactive',  # 干啥用？
        'Programming Language :: Python',  # 依赖环境
        'Programming Language :: Python :: 2',
        'Programming Language :: Python :: 2.7',
        'Programming Language :: Python :: 3',
        'Programming Language :: Python :: 3.4',
        'Programming Language :: Python :: 3.5',
        'Programming Language :: Python :: Implementation :: CPython',
        'Programming Language :: Python :: Implementation :: PyPy',
        'Intended Audience :: Developers',
        'Intended Audience :: System Administrators',
        'License :: OSI Approved :: BSD License',
        'Operating System :: OS Independent',
        'Topic :: System :: Systems Administration',
    ]
)
```

## bin/pip-diff

主要源码

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""Usage:  # docopt命令行
  pip-diff (--fresh | --stale) <reqfile1> <reqfile2> [--exclude <package>...]
  pip-diff (-h | --help)

Options:
  -h --help     Show this screen.
  --fresh       List newly added packages.  # 新增加的包
  --stale       List removed packages.  # 移除的包
"""
import os
from docopt import docopt
from pip.req import parse_requirements
from pip.index import PackageFinder
from pip._vendor.requests import session

requests = session()


class Requirements(object):
    def __init__(self, reqfile=None):
        super(Requirements, self).__init__()
        self.path = reqfile
        self.requirements = []

        if reqfile:
            self.load(reqfile)

    def __repr__(self):
        return '<Requirements \'{}\'>'.format(self.path)

    def load(self, reqfile):  # 同pip-grep文件中的这个函数， 将指定文件的依赖包解析并加载到self
        if not os.path.exists(reqfile):
            raise ValueError('The given requirements file does not exist.')

        finder = PackageFinder([], [], session=requests)
        for requirement in parse_requirements(reqfile, finder=finder, session=requests):
            if requirement.req:  # req是啥？
                if not getattr(requirement.req, 'name', None):
                    # Prior to pip 8.1.2 the attribute `name` did not exist.
                    requirement.req.name = requirement.req.project_name
                self.requirements.append(requirement.req)


    def diff(self, requirements, ignore_versions=False, excludes=None):  # 为什么不用set来实现？
        r1 = self  # 对象1，包含多个依赖包
        r2 = requirements  # 对象2，包含多个依赖包
        results = {'fresh': [], 'stale': []}  # 记录对比结果

        # Generate fresh packages.
        other_reqs = (  # 如果忽略版本，则只保存名字，否则保存整个对象
            [r.name for r in r1.requirements]
            if ignore_versions else r1.requirements
        )

        for req in r2.requirements: # 对于新的reqirement
            r = req.name if ignore_versions else req  #处理成与other_reqs相同类型的变量

            if r not in other_reqs and r not in excludes:  # 新增加的包，并且不在白名单中，则加入fresh
                results['fresh'].append(req)

        # Generate stale packages.
        other_reqs = (  # 既然fresh和stale是分开写的，那么这个变量命名方式就不好，不应该跟上面相同，不容易理解
            [r.name for r in r2.requirements]
            if ignore_versions else r2.requirements
        )

        for req in r1.requirements: # 对于旧的reqirement
            r = req.name if ignore_versions else req

            if r not in other_reqs and r not in excludes:  # 如果不是在新列表里，并且不在白名单里，则加入stale
                results['stale'].append(req)

        return results


def diff(r1, r2, include_fresh=False, include_stale=False, excludes=None):
    include_versions = True if include_stale else False  # 版本号只在有stale包时才用
    excludes = excludes if len(excludes) else []  # 规范为列表

    try:
        r1 = Requirements(r1)
        r2 = Requirements(r2)
    except ValueError:
        print('There was a problem loading the given requirements files.')
        exit(os.EX_NOINPUT)

    results = r1.diff(r2, ignore_versions=True, excludes=excludes)

    if include_fresh:
        for line in results['fresh']:
            print(line.name if include_versions else line)  # 只打印名字 耦合性太强了

    if include_stale:
        for line in results['stale']:
            print(line.name if include_versions else line)  # 只打印名字


def main():
    args = docopt(__doc__, version='pip-diff')

    kwargs = {
        'r1': args['<reqfile1>'],
        'r2': args['<reqfile2>'],
        'include_fresh': args['--fresh'],
        'include_stale': args['--stale'],
        'excludes': args['<package>']
    }

    diff(**kwargs)


if __name__ == '__main__':
    main()
```

## bin/pip-grep

辅助源码

```python
#!/usr/bin/env python  # 最标准的py文件头
# -*- coding: utf-8 -*-

"""Usage:  # docopt命令行
  pip-grep [-s] <reqfile> <package>...

Options:
  -h --help     Show this screen.
"""
import os
from docopt import docopt
from pip.req import parse_requirements  # 解析reqirements文件
from pip.index import PackageFinder  # 查找库
from pip._vendor.requests import session  # 不懂

requests = session()  # 参考：生成会话，管理上下文


class Requirements(object):
    def __init__(self, reqfile=None):
        super(Requirements, self).__init__()  # 标准的父类init方式，一般我对object类的子类不加这个，可能加上更好
        self.path = reqfile  # reqirement文件路径
        self.requirements = []

        if reqfile:  # 如果有reqfile，则加载reqfile文件
            self.load(reqfile)

    def __repr__(self):  # 打印
        return '<Requirements \'{}\'>'.format(self.path)  # 更pythonic的方法是用单引号嵌套双引号

    def load(self, reqfile):
        if not os.path.exists(reqfile):  # 文件存在性检查
            raise ValueError('The given requirements file does not exist.')

        finder = PackageFinder([], [], session=requests)  # 包查找器，requests管理上下文
        for requirement in parse_requirements(reqfile, finder=finder, session=requests):  # pip解析的依赖包对象
            if requirement.req:
                if not getattr(requirement.req, 'name', None):  # 处理pip版本低的问题，标准化
                    # Prior to pip 8.1.2 the attribute `name` did not exist.
                    requirement.req.name = requirement.req.project_name
                self.requirements.append(requirement.req)  # 将req属性写入requirements


def grep(reqfile, packages, silent=False):  # 查找函数 反馈是否满足依赖
    try:
        r = Requirements(reqfile)  # 实例化
    except ValueError:  # 处理初始化错误
        if not silent:
            print('There was a problem loading the given requirement file.')
        exit(os.EX_NOINPUT)

    for req in r.requirements:  # 处理每个依赖包
        if req.name in packages:
            if not silent:  # 命令行打印
                print('Package {} found!'.format(req.name))
            exit(0)  # 满足依赖则返回0 疑问：为什么结束循环？是不是没判断版本？

    if not silent:  #
        print('Not found.')

    exit(1)  # 不满足以来则返回1


def main():
    args = docopt(__doc__, version='pip-grep')

    kwargs = {'reqfile': args['<reqfile>'], 'packages': args['<package>'], 'silent': args['-s']}

    grep(**kwargs)  # 安全的参数使用方法，先定义有哪些参数，然后用**传递


if __name__ == '__main__':
    main()  # 命令行使用
```

## note
[他人的阅读笔记](http://blog.csdn.net/erosxd/article/details/53054895)
