---
title: "Python的caller对callee模块内部参数的修改生效情况"
date: 2019-07-24T10:16:20+08:00
categories: 2019-07
tags: [python]
author: sgl
---

### 被调用方callee代码
    
    callee.py
    
    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    """
        @Author      : xxx@xxx.net
        @Date        : 2019-06-28
        @Description : 
        @File        : callee.py
    """
    
    NAME = '我是模块内部的初始值'
    print(NAME)
    print('模块会在被引入时(import callee)执行一遍,引入本模块会显示此信息')
    
    
    def fun1():
        print("模块会在被引入时执行一遍，但是不会执行这个函数，除非被调用")
    
    
    class ClassTemplate(object):
        # 此处的name变量除非被初始化时显示参数传入，否则使用的是模块内部的全局变量值
        def __init__(self, name=NAME):
            # __name 是私有变量，只有类本身可以访问，子类也不可以访问
            # _name 是受保护变量，类本身和子类可以访问，from module import *无法导入
            # decorator装饰器/迭代器__iter__、__next__/yield生成器/
            self.__name = name
            print(name)
            # 如果外面模块修改了这个变量，则显示修改后的变量值
            print(NAME)
    
        def run(self):
            print("my name is %s" % self.__name)
    
        @classmethod
        # 此处的变量value除非被初始化时显示参数传入，否则使用的是模块内部的全局变量值
        def class_method(cls, value=NAME):
            print(value)
            # 如果外面模块修改了这个变量，则显示修改后的变量值
            print(NAME)
            # cls.staticmethod('test')
    
        @staticmethod
        def staticmethod(value):
            print("my value is {}".format(value))
    

### 调用方caller代码
    
    test_shutter.py
    
    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    """
        @Author      : xxxx
        @Date        : 2019-06-10
        @Description : 
        @File        : test_shutter.py
    """
    
    from django.test import TestCase    
    import callee
    
    
    class TestShutter(TestCase):
        """doc:I'm a class"""        
    
        # @unittest.skip('跳过')
        def test_0(self):
            """doc: I'm a function"""
            # 从外部修改被调用模块的变量
            callee.NAME = "我是外部修改模块全局变量值"
            a = callee.ClassTemplate(name="我是传入参数")
            print(a)
            a.class_method()
    
            print("##########")
            # 当前模块module的文件路径
            print(__file__)
            # 类名字
            print(TestShutter.__name__)
            # 类信息
            print(TestShutter.__class__)
            # 类名字
            print(TestShutter.__class__.__name__)
            # 当前模块文件的名字
            print(__name__)
            # 当前模块文档
            print(__doc__)
            # 当前模块某个类的文档
            print(TestShutter.__doc__)
            # 当前模块某个类的方法的文档
            print(TestShutter.test_0.__doc__)
            # 当前类
            print(__class__)
            # 被引入模块的名字
            print(callee.__name__)
    
            print("##########")
    
            self.assertEqual(True, True)

### django下测试输出

执行```python manage.py test --patter='test_shutter.py' -v 3```,输出内容：

    我是模块内部的初始值
    模块会在被引入时(import callee)执行一遍,引入本模块会显示此信息
    
    我是传入参数
    我是外部修改模块全局变量值
    <callee.ClassTemplate object at 0x111971320>
    我是模块内部的初始值
    我是外部修改模块全局变量值
    ##########
    /Users/shendepu/gitlab/erebus_app/test_shutter.py
    TestShutter
    <class 'type'>
    type
    test_shutter
    
        @Author      : xxxx
        @Date        : 2019-06-10
        @Description : 
        @File        : test_shutter.py
    
    doc:I'm a class
    doc: I'm a function
    <class 'test_shutter.TestShutter'>
    callee
    ##########
    
### 结论

通过上面模块test_shutter.py对模块callee.py的引用，以及测试结果输出，理解模块callee的变量范围  

* 当文件是被调用时，__name__的值为模块名；此时并不需要一个可执行的入口main了
* 当一个module作为整体被执行时，__name__的值为 "__main__", 类似于C语言中的main()函数的作用，即作为入口