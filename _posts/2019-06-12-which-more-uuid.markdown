---
layout: post
category: web
title:  "UUID详解"
tags: [阅读,网络]
date: 2019-06-12 10:05:01
---

&emsp;&emsp;UUID是通用唯一识别码（Universally Unique Identifier）的缩写，是一种软件建构的标准，亦为开放软件基金会组织在分布式计算环境领域的一部分。
其目的，是让分布式系统中的所有元素，都能有唯一的辨识信息，而不需要通过中央控制端来做辨识信息的指定。
如此一来，每个人都可以创建不与其它人冲突的UUID。在这样的情况下，就不需考虑数据库创建时的名称重复问题。<!-- more -->

+ 作用

    UUID 的目的是让分布式系统中的所有元素，都能有唯一的辨识资讯，而不需要透过中央控制端来做辨识资讯的指定。
    如此一来，每个人都可以建立不与其它人冲突的 UUID。在这样的情况下，就不需考虑数据库建立时的名称重复问题。
    目前最广泛应用的 UUID，即是微软的 Microsoft's Globally Unique Identifiers (GUIDs)，而其他重要的应用，
    则有 Linux ext2/ext3 档案系统、LUKS 加密分割区、GNOME、KDE、Mac OS X 等等。

+ 组成

    UUID是指在一台机器上生成的数字，它保证对在同一时空中的所有机器都是唯一的。通常平台会提供生成的API。
    按照开放软件基金会(OSF)制定的标准计算，用到了以太网卡地址、纳秒级时间、芯片ID码和随机数。
    UUID由以下几部分的组合：
    （1）当前日期和时间，UUID的第一个部分与时间有关，如果你在生成一个UUID之后，过几秒又生成一个UUID，则第一个部分不同，其余相同。
    （2）时钟序列。
    （3）全局唯一的IEEE机器识别号，如果有网卡，从网卡MAC地址获得，没有网卡以其他方式获得。
    UUID的唯一缺陷在于生成的结果串会比较长。关于UUID这个标准使用最普遍的是微软的GUID(Globals Unique Identifiers)。
    在ColdFusion中可以用CreateUUID()函数很简单地生成UUID，其格式为：xxxxxxxx-xxxx- xxxx-xxxxxxxxxxxxxxxx(8-4-4-16)，
    其中每个 x 是 0-9 或 a-f 范围内的一个十六进制的数字。而标准的UUID格式为：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (8-4-4-4-12)，
    可以从cflib 下载CreateGUID() UDF进行转换。

+ 定义

    UUID是由一组32位数的16进制数字所构成，是故UUID理论上的总数为16^32=2^128，约等于3.4 x 10^38。也就是说若每纳秒产生1兆个UUID，要花100亿年才会将所有UUID用完。
    UUID的标准型式包含32个16进制数字，以连字号分为五段，形式为8-4-4-4-12的32个字符。示例：
    550e8400-e29b-41d4-a716-446655440000
    UUID亦可刻意重复以表示同类。例如说微软的COM中，所有组件皆必须实现出IUnknown接口，方法是产生一个代表IUnknown的UUID。
    无论是程序试图访问组件中的IUnknown接口，或是实现IUnknown接口的组件，只要IUnknown一被使用，皆会被参考至同一个ID：00000000-0000-0000-C000-000000000046。
```
#coding=utf-8
 
import uuid
 
name = 'test_name'
namespace = 'test_namespace'
 
print uuid.uuid1()
print uuid.uuid3(namespace,name)
print uuid.uuid4()
print uuid.uuid5(namespace,name)
```
Version1变种 – Hibernate
Hibernate的CustomVersionOneStrategy.java，解决了之前version 1的两个问题
- 时间戳(6bytes, 48bit)：毫秒级别的，从1970年算起，能撑8925年….
- 顺序号(2bytes, 16bit, 最大值65535): 没有时间戳过了一秒要归零的事，各搞各的，short溢出到了负数就归0。
- 机器标识(4bytes 32bit): 拿localHost的IP地址，IPV4呢正好4个byte，但如果是IPV6要16个bytes，就只拿前4个byte。
- 进程标识(4bytes 32bit)： 用当前时间戳右移8位再取整数应付，不信两条线程会同时启动。
值得留意就是，机器进程和进程标识组成的64bit Long几乎不变，只变动另一个Long就够了。
 
Version1变种 – MongoDB
MongoDB的ObjectId.java
- 时间戳(4 bytes 32bit): 是秒级别的，从1970年算起，能撑136年。
- 自增序列(3bytes 24bit, 最大值一千六百万)： 是一个从随机数开始（机智）的Int不断加一，也没有时间戳过了一秒要归零的事，各搞各的。因为只有3bytes，所以一个4bytes的Int还要截一下后3bytes。
- 机器标识(3bytes 24bit): 将所有网卡的Mac地址拼在一起做个HashCode，同样一个int还要截一下后3bytes。搞不到网卡就用随机数混过去。
- 进程标识(2bytes 16bits)：从JMX里搞回来到进程号，搞不到就用进程名的hash或者随机数混过去。
可见，MongoDB的每一个字段设计都比Hibernate的更合理一点，比如时间戳是秒级别的。总长度也降到了12 bytes 96bit，但如果果用64bit长的Long来保存有点不上不下的，只能表达成byte数组或16进制字符串。
        