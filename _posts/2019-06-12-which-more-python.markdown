---
layout: post
category: python
title:  "python2与python3的区别齐全"
tags: [阅读,网络]
date: 2019-06-12 18:05:01
---
#### 一、核心类差异
    1. Python3 对 Unicode 字符的原生支持。
        Python2 中使用 ASCII 码作为默认编码方式导致 string 有两种类型 str 和 unicode，Python3 只
        支持 unicode 的 string。Python2 和 Python3 字节和字符对应关系为：
    2. Python3 采用的是绝对路径的方式进行 import
        Python2 中相对路径的 import 会导致标准库导入变得困难（想象一下，同一目录下有 file.py，如
        何同时导入这个文件和标准库 file）。Python3 中这一点将被修改，如果还需要导入同一目录的文件必
        须使用绝对路径，否则只能使用相关导入的方式来进行导入。
    3. Python2 中存在老式类和新式类的区别
        Python3 统一采用新式类。新式类声明要求继承 object，必须用新式类应用多重继承。
    4. Python3 使用更加严格的缩进。
        Python2 的缩进机制中，1 个 tab 和 8 个 space 是等价的，所以在缩进中可以同时允
        许 tab 和 space 在代码中共存。这种等价机制会导致部分 IDE 使用存在问题。
        Python3 中 1 个 tab 只能找另外一个 tab 替代，因此 tab 和 space 共存会导致报错：TabError:
        inconsistent use of tabs and spaces in indentation.
        <!-- more -->
#### 二、废弃类差异
    1. print 语句被 Python3 废弃，统一使用 print 函数
    2. exec 语句被 python3 废弃，统一使用 exec 函数
    3. execfile 语句被 Python3 废弃，推荐使用 exec(open("./filename").read())
    4. 不相等操作符"<>"被 Python3 废弃，统一使用"!="
    5. long 整数类型被 Python3 废弃，统一使用 int
    6. xrange 函数被 Python3 废弃，统一使用 range，Python3 中 range 的机制也进行修改并提高
    了大数据集生成效率
    7. Python3 中这些方法再不再返回 list 对象：dictionary 关联的 keys()、values()、items()，zip()，
        map()，filter()，但是可以通过 list 强行转换：
        1. mydict={"a":1,"b":2,"c":3}
        2. mydict.keys() #<built-in method keys of dict object at 0x000000000040B4C8>
        3. list(mydict.keys()) #['a', 'c', 'b']
    8. 迭代器 iterator 的 next()函数被 Python3 废弃，统一使用 next(iterator)
    9. raw_input 函数被 Python3 废弃，统一使用 input 函数
    10. 字典变量的 has_key 函数被 Python 废弃，统一使用 in 关键词
    11. file 函数被 Python3 废弃，统一使用 open 来处理文件，可以通过 io.IOBase 检查文件类型
    12. apply 函数被 Python3 废弃
    13. 异常 StandardError 被 Python3 废弃，统一使用 Exception

#### 三 、修改类差异
+ 1. 浮点数除法操作符“/”和“//”的区别
    “ / ”：
    Python2：若为两个整形数进行运算，结果为整形，但若两个数中有一个为浮点数，则结果为
    浮点数；
    Python3:为真除法，运算结果不再根据参加运算的数的类型。
    “//”：
    Python2：返回小于除法运算结果的最大整数；从类型上讲，与"/"运算符返回类型逻辑一致。
    Python3：和 Python2 运算结果一样。
+ 2. 异常抛出和捕捉机制区别
    Python2
        1. raise IOError, "file error" #抛出异常
        2. except NameError, err: #捕捉异常
    Python3
        1. raise IOError("file error") #抛出异常
        2. except NameError as err: #捕捉异常
+ 3. for 循环中变量值区别
    Python2，for 循环会修改外部相同名称变量的值
        1. i = 1
        2. print （'comprehension: ', [i for i in range(5)]）
        3. print （'after: i =', i ） #i=4
    Python3，for 循环不会修改外部相同名称变量的值
        1. i = 1
        2. print （'comprehension: ', [i for i in range(5)]）
        3. print （'after: i =', i ） #i=1
+ 4. round 函数返回值区别
    Python2，round 函数返回 float 类型值
    1. isinstance(round(15.5),int) #True
    Python3，round 函数返回 int 类型值
    1. isinstance(round(15.5),float) #True
+ 5. 比较操作符区别
    Python2 中任意两个对象都可以比较
    1. 11 < 'test' #True
    Python3 中只有同一数据类型的对象可以比较
    1. 11 < 'test' # TypeError: unorderable types: int() < str()

#### 四、第三方工具包差异
我们在 pip 官方下载源 pypi 搜索 Python2.7 和 Python3.5 的第三方工具包数可以发现，Python2.7
版本对应的第三方工具类目数量是 28523,Python3.5 版本的数量是 12457，这两个版本在第三方工具
包支持数量差距相当大。
我们从数据分析的应用角度列举了常见实用的第三方工具包（如下表），并分析这些工具包在
Python2.7 和 Python3.5 的支持情况
 
#### 五、工具安装问题
+ windows 环境
Python2 无法安装 mysqlclient。Python3 无法安装 MySQL-python、 flup、functools32、
Gooey、Pywin32、 webencodings。
matplotlib 在 python3 环境中安装报错：The following required packages can not be
built:freetype, png。需要手动下载安装源码包安装解决。
scipy 在 Python3 环境中安装报错，numpy.distutils.system_info.NotFoundError，需要自己手
工下载对应的安装包，依赖 numpy,pandas 必须严格根据 python 版本、操作系统、64 位与否。运行
matplotlib 后发现基础包 numpy+mkl 安装失败，需要自己下载，国内暂无下载源
+ centos 环境下
Python2 无法安装 mysql-python 和 mysqlclient 包，报错：EnvironmentError: mysql_config not
found，解决方案是安装 mysql-devel 包解决。使用 matplotlib 报错：no module named _tkinter，
安装 Tkinter、tk-devel、tc-devel 解决。
pywin32 也无法在 centos 环境下安装。
```
import sys
# https://blog.csdn.net/u011089523/article/details/69681099
PY2 = sys.version_info[0] == 2
if not PY2:
    text_type = str
    string_types = (str,)
    unichr = chr
else:
    text_type = unicode
    string_types = (str, unicode)
    unichr = unichr

if PY2:
    def __str__(self):
        return self.__unicode__().encode('utf-8')

if not PY3:
    def __str__(self):
        return self.__unicode__().encode('utf-8')

if PY2:
    def implements_to_string(cls):
        cls.__unicode__ = cls.__str__
        cls.__str__ = lambda x: x.__unicode__().encode('utf-8')
        return cls
    else:
        implements_to_string = lambda x: x
```
