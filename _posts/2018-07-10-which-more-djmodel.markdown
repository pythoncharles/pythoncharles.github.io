---
layout: post
category: web
title:  "Django 模型 models详解"
tags: [阅读,网络]
date: 2018-07-10 13:05:25
---

Django通过model不可以创建数据库，但可以创建数据库表，一下是创建表的字段以及表字段的参数。
+ 字段
+ 字段参数
+ 附录表之间的关系
<!-- more -->
#### 一、字段
1、models.AutoField　　自增列 = int(11)   
    如果没有的话，默认会生成一个名称为 id 的列，如果要显示的自定义一个自增列，必须将给列设置为主键 primary_key=True。        
2、models.CharField　　字符串字段
　　必须 max_length 参数       
3、models.BooleanField　　布尔类型=tinyint(1)
　　不能为空，Blank=True     
4、models.ComaSeparatedIntegerField　　
    用逗号分割的数字=varchar继承CharField，所以必须 max_lenght 参数    
5、models.DateField　　日期类型 date
　　对于参数，auto_now = True 则每次更新都会更新这个时间；
    auto_now_add 则只是第一次创建添加，之后的更新不再改变。   
6、models.DateTimeField　　日期类型 datetime
　　同DateField的参数   
7、models.Decimal　　十进制小数类型 = decimal
　　必须指定整数位max_digits和小数位decimal_places    
8、models.EmailField　　字符串类型（正则表达式邮箱） =varchar
　　对字符串进行正则表达式     
9、models.FloatField　　浮点类型 = double   
10、models.IntegerField　　整形   
11、models.BigIntegerField　　长整形
```angular2html
integer_field_ranges = {
　　　　'SmallIntegerField': (-32768, 32767),
　　　　'IntegerField': (-2147483648, 2147483647),
　　　　'BigIntegerField': (-9223372036854775808, 9223372036854775807),
　　　　'PositiveSmallIntegerField': (0, 32767),
　　　　'PositiveIntegerField': (0, 2147483647),
　　}
```   
12、models.IPAddressField　　字符串类型（ip4正则表达式）    
13、models.GenericIPAddressField　　字符串类型（ip4和ip6是可选的）    
　　参数protocol可以是：both、ipv4、ipv6
　　验证时，会根据设置报错     
14、models.NullBooleanField　　允许为空的布尔类型    
15、models.PositiveIntegerFiel　　正Integer    
16、models.PositiveSmallIntegerField　　
        正smallInteger     
17、models.SlugField　　减号、下划线、字母、数字    
18、models.SmallIntegerField　　数字     
　　数据库中的字段有：tinyint、smallint、int、bigint    
19、models.TextField　　字符串=longtext       
20、models.TimeField　　时间 HH:MM[:ss[.uuuuuu]]    
21、models.URLField　　字符串，地址正则表达式    
22、models.BinaryField　　二进制<br>
23、models.ImageField   图片<br>
24、models.FilePathField 文件
#### 二、字段参数
1、null=True
　　数据库中字段是否可以为空    
2、blank=True
　　django的 Admin 中添加数据时是否可允许空值    
3、primary_key = False
　　主键，对AutoField设置主键后，就会代替原来的自增 id 列   
4、auto_now 和 auto_now_add
　　auto_now   自动创建---无论添加或修改，都是当前操作的时间
　　auto_now_add  自动创建---永远是创建时的时间    
5、choices
```angular2html
GENDER_CHOICE = (
        (u'M', u'Male'),
        (u'F', u'Female'),
    )
gender = models.CharField(max_length=2,choices = GENDER_CHOICE)
```
6、max_length   
7、default　　默认值  
8、verbose_name　　Admin中字段的显示名称   
9、name|db_column　　数据库中的字段名称    
10、unique=True　　不允许重复    
11、db_index = True　　数据库索引    
12、editable=True　　在Admin里是否可编辑    
13、error_messages=None　　错误提示     
14、auto_created=False　　自动创建     
15、help_text　　在Admin中提示帮助信息    
16、validators=[]    
17、upload-to    
#### 附录表之间的关系
1、一对多，models.ForeignKey(ColorDic)
     应用场景：在某表中创建一行数据时，有一个单选的下拉框（下拉框中的内容被用过一次就消失了）。   
2、一对一，models.OneToOneField(OneModel)
    应用场景：当一张表中创建一行数据时，有一个单选的下拉框（可以被重复选择）。    
3、authors = models.ManyToManyField(Author)
     应用场景：在某表中创建一行数据是，有一个可以多选的下拉框。