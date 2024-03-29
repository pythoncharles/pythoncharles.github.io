---
layout: post
category: web
title:  "Django核心配置项"
tags: [阅读,网络]
date: 2018-08-13 13:05:25
---

&emsp;&emsp;Django的默认配置文件中，包含上百条配置项目，其中很多是我们‘一辈子’都不碰到或者不需要单独配置的，这些项目在需要的时候再去查手册。
强调：配置的默认值不是在settings.py文件中！不要以为settings.py中的配置值就是默认值，参考前文。
settings.py是使用django-admin startproject xxx命令时，额外给我们创建的。
<!-- more -->
下面介绍的是61个相对比较常用和重要的配置项，按字母顺序排序，但是最后部分是cache、auth、message、session、static等的配置。    
ADMINS    
ALLOWED_HOSTS    
APPEND_SLASH   
DATABASES   
DATE_FORMAT     
DATE_INPUT_FORMATS     
DATETIME_FORMAT     
DATETIME_INPUT_FORMATS     
DEBUG     
DEFAULT_CHARSET     
DEFAULT_CONTENT_TYPE     
DEFAULT_FROM_EMAIL          
DISALLOWED_USER_AGENTS     
EMAIL_BACKEND          
EMAIL_FILE_PATH     
EMAIL_HOST     
EMAIL_HOST_PASSWORD     
EMAIL_HOST_USER     
EMAIL_PORT     
EMAIL_SUBJECT_PREFIX     
EMAIL_USE_TLS     
EMAIL_USE_SSL     
EMAIL_SSL_CERTFILE     
EMAIL_SSL_KEYFILE     
EMAIL_TIMEOUT     
FILE_CHARSET     
INSTALLED_APPS     
LANGUAGE_CODE     
LANGUAGES     
LOCALE_PATHS     
LOGGING     
LOGGING_CONFIG     
MEDIA_ROOT     
MEDIA_URL     
MIDDLEWARE     
ROOT_URLCONF     
SECRET_KEY     
TEMPLATES     
TIME_ZONE     
USE_I18N     
USE_L10N     
USE_TZ     
WSGI_APPLICATION     
CACHES     
AUTHENTICATION_BACKENDS     
AUTH_USER_MODEL     
LOGIN_REDIRECT_URL     
LOGIN_URL     
LOGOUT_REDIRECT_URL     
PASSWORD_RESET_TIMEOUT_DAYS     
PASSWORD_HASHERS     
MESSAGE_LEVEL     
MESSAGE_STORAGE     
SESSION_COOKIE_AGE     
SESSION_COOKIE_NAME     
SESSION_ENGINE     
SESSION_EXPIRE_AT_BROWSER_CLOSE     
SITE_ID     
STATIC_ROOT     
STATIC_URL     
STATICFILES_DIRS     
1. ADMINS     
默认值：[]（空列表）     
所有获得代码错误通知的人的邮件地址列表。当DEBUG=False，并且一个视图引发了异常时,Django将会给这个列表里的人发一封含有完整异常信息的电子邮件。列表中的每个项目都应该是（全名，电子邮件地址）的元组。例如：
[('John', 'john@example.com'), ('Mary', 'mary@example.com')]

2. ALLOWED_HOSTS     
默认值：[]（空列表）     
这是新手比较困惑的一个配置项。该配置项列表中包含的是Django站点可以为之提供服务的主机/域名。 也就是哪些主机或IP能够访问Django服务器！列表里的所有元素是共同存在的关系，不纯在冲突、优先级和排斥的关系。
列表中的值可以是localhost、www.example.com或者.example.com形式的域名。
也可以是IP地址，比如：137.2.4.1、192.168.1.1、0.0.0.0、127.0.0.1
还可以是通配符'*'，表示所有外部主机都可以访问Django。但这种情况具有安全风险，在线上环境不要使用。
对于0.0.0.0，表示局域网内的主机都可以访问Django。
当DEBUG为True和ALLOWED_HOSTS为空时，默认相当于配置：['localhost'， '127.0.0.1'， '[:: 1]']。
3. APPEND_SLASH     
默认值：True     
当设定为True时，如果请求的URL没有匹配到URLconf里面的任何一条URL路由设置，并且没有以/（斜杠）结束，该请求将重定向到以请求URL加/的URL地址。需要注意的是重定向有可能导致POST提交的数据丢失。
通俗的解释就是，如果你在写url时忘记了在最后添加一个斜杠，Django会默认帮你加上！请尽量保持默认值！
APPEND_SLASH设置只有在安装了CommonMiddleware中间件时才会启用。
4. DATABASES     
默认值: {} (空的字典)     
该配置项包含Django项目使用的所有数据库的设置。这是一个嵌套字典。
DATABASES设置必须配置一个default数据库，以及指定任意数量的其它数据库（可以没有）。
最简单的配置方式是使用SQLite数据库，Python和Django内置对SQLite数据库的支持，无需任何额外的安装和插件，如下所示：
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'mydatabase',
        # 或者'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```     
由于SQLite数据库就在Django项目本地，通常还是在Django项目根目录下，以一个文件的形式存在，没有用户名、密码、IP、port的问题，所以配置比较简单。
当连接其他数据库后端，比如MySQL、Oracle 或PostgreSQL，必须提供更多的连接参数。下面的例子用于PostgreSQL：
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```
下面是DATABASES的内部配置项：     
ATOMIC_REQUESTS     
默认值：False     
原子性事务请求。     
AUTOCOMMIT     
默认值：True     
自动提交。     
ENGINE     
默认值：''（空字符串）     
最重要的配置项！指定使用的数据库后端。 内建的数据库后端名称有：
```
'django.db.backends.postgresql'
'django.db.backends.mysql'
'django.db.backends.sqlite3'
'django.db.backends.oracle'
```
HOST
默认值：''（空字符串）
数据库所在的主机。设置的值可以是主机名、IP地址、和socket路径。空字符串表示localhost。SQLite不需要配置这个选项。
如果其值以斜杠（'/'）开头并且你使用的是MySQL，MySQL将通过Unix socket连接。 像这样：
"HOST": '/var/run/mysql'

NAME     
默认值：''（空字符串）     
使用的数据库名称。 对于SQLite，它是数据库文件的完整路径。指定路径时，请始终使用前向的斜杠，即使在Windows 上（例如C:/homes/user/mysite/sqlite3.db）。
但是对于MySQL等数据库，NAME指的是数据库系统中的具体某个database，Django没有能力自动在MySQL中创建数据库，它只能通过模型创建数据表。所以需要你通过各种数据客户端，在MySQL中提前创建好Django项目需要的数据库，使用命令CREATE DATABASE mysite CHARACTER SET utf8;，mysite是你的数据库名，务必同时指定字符编码方式为utf8！
CONN_MAX_AGE     
默认：0     
数据库连接的存活时间，以秒为单位。0表示在每个请求结束时关闭数据库连接，None表示无限的持久连接。
OPTIONS     
Default: {} (默认为空的字典)     
连接数据库时使用的额外参数。可用的参数与你的数据库后端有关。
PASSWORD          
默认值：''（空字符串）          
连接数据库时使用的密码。SQLite不需要这个选项。
PORT     
默认值：''（空字符串）     
连接数据库时使用的端口。空字符串表示默认的端口。SQLite不需要这个选项。 MySQL一般是3306。
TIME_ZONE     
默认值：None     
数据库中使用的时区。     
USER     
默认值：''（空字符串）     
连接数据库时使用的用户名。SQLite不需要这个选项。     
TEST     
Default: {} (默认为空的字典)     
测试数据库用的配置。     
以下是测试数据库配置的示例：     
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'USER': 'mydatabaseuser',
        'NAME': 'mydatabase',
        'TEST': {
            'NAME': 'mytestdatabase',
        },
    },
}
```
5. DATE_FORMAT     
默认值：'N j, Y' (例如：Feb. 4, 2003)
系统中日期字段的默认显示格式。     
6. DATE_INPUT_FORMATS     
默认值：     
```
[
    '%Y-%m-%d', '%m/%d/%Y', '%m/%d/%y', # '2006-10-25', '10/25/2006', '10/25/06'
    '%b %d %Y', '%b %d, %Y',            # 'Oct 25 2006', 'Oct 25, 2006'
    '%d %b %Y', '%d %b, %Y',            # '25 Oct 2006', '25 Oct, 2006'
    '%B %d %Y', '%B %d, %Y',            # 'October 25 2006', 'October 25, 2006'
    '%d %B %Y', '%d %B, %Y',            # '25 October 2006', '25 October, 2006'
]
```
日期字段中输入数据时将能够被接受的格式列表。格式将按顺序尝试，使用第一个有效的格式。
7. DATETIME_FORMAT     
默认值：'N j, Y, P' (例如Feb. 4, 2003, 4 p.m.)
系统中显示datetime字段的默认格式。     
8. DATETIME_INPUT_FORMATS     
默认值：     
```
[
    '%Y-%m-%d %H:%M:%S',     # '2006-10-25 14:30:59'
    '%Y-%m-%d %H:%M:%S.%f',  # '2006-10-25 14:30:59.000200'
    '%Y-%m-%d %H:%M',        # '2006-10-25 14:30'
    '%Y-%m-%d',              # '2006-10-25'
    '%m/%d/%Y %H:%M:%S',     # '10/25/2006 14:30:59'
    '%m/%d/%Y %H:%M:%S.%f',  # '10/25/2006 14:30:59.000200'
    '%m/%d/%Y %H:%M',        # '10/25/2006 14:30'
    '%m/%d/%Y',              # '10/25/2006'
    '%m/%d/%y %H:%M:%S',     # '10/25/06 14:30:59'
    '%m/%d/%y %H:%M:%S.%f',  # '10/25/06 14:30:59.000200'
    '%m/%d/%y %H:%M',        # '10/25/06 14:30'
    '%m/%d/%y',              # '10/25/06'
]
```
在datetime字段中输入数据时将被接受的格式列表。格式将按顺序尝试，使用第一个有效的格式。
9.DEBUG          
默认值：False          
打开/关闭调试模式。最重要的设置之一！默认值是False，你没有看错！只是在settings.py中又帮我们设置为True了，打开了调试模式，方便开发者和测试者的！线上部署网站的时候务必设置为False。
调试模式下可以显示错误页面的细节。若你的应用产生了一个异常，Django会显示追溯细节，包括许多环境变量的元数据, 比如所有当前定义的Django设置。
作为安全考虑，调试信息中不会列出包含下列关键字的配置项的内容，例如SECRET_KEY：
'API'
'KEY'
'PASS'
'SECRET'
'SIGNATURE'
'TOKEN'
注意，这里使用的是包含匹配，也就是说只要出现这些子字符串的配置项将匹配上。例如'PASS'能够匹配 PASSWORD, 'TOKEN'也将匹配TOKENIZED等等.
最后再次强调，如果DEBUG为False，你还需要正确设置ALLOWED_HOSTS以及静态文件。错误设置将导致对所有的请求返回“Bad Request (400)”。     
10.DEFAULT_CHARSET          
默认值：'utf-8'          
HttpResponse响应对象的默认字符集。     
11.DEFAULT_CONTENT_TYPE     
默认值：'text/html'     
HttpResponse对象的默认内容类型。     
12.DEFAULT_FROM_EMAIL     
默认值：'webmaster@localhost'     
默认的电子邮件发送地址，即发送方。     
13.DISALLOWED_USER_AGENTS     
默认值：[]（空列表）     
这是一个编译好了的正则表达式对象的列表。代表哪些不允许访问任何页面的User-Agent字符串。也就是说如果一个请求的User-Agent属性，
被这个配置项中的任何一个正则表达式匹配到了，那么这个请求将被阻止。常用于对付机器人和网络蜘蛛。需要CommonMiddleware中间件支持。     
14.EMAIL_BACKEND          
默认值：' django.core.mail.backends.smtp.EmailBackend '     
用于发送邮件的后端。     
15.EMAIL_FILE_PATH     
默认：未指定     
邮件后端保存输出文件时使用的目录。     
16.EMAIL_HOST     
默认：'localhost'     
发送邮件使用的主机。     
17.EMAIL_HOST_PASSWORD          
默认值：''（空字符串）     
EMAIL_HOST的SMTP服务器使用的密码。     
18.EMAIL_HOST_USER     
默认值：''（空字符串）     
EMAIL_HOST的SMTP服务器使用的用户名。     
19.EMAIL_PORT     
默认：25     
EMAIL_HOST的SMTP服务器使用的端口。     
20.MAIL_SUBJECT_PREFIX     
默认值：'[Django] '     
使用django.core.mail.mail_admins或django.core.mail.mail_managers发送的电子邮件的主题行前缀。     
21.EMAIL_USE_TLS     
默认值：False     
是否使用TLS(安全)与SMTP服务器连接。用于显式TLS连接,通常在端口587上。     
22.EMAIL_USE_SSL     
默认值：False     
在与SMTP服务器通信时是否使用隐式TLS（安全）连接。在大多数电子邮件文档中，此类型的TLS连接称为SSL。 它通常在端口465上使用。     
注意：腾讯家的qq邮箱服务，需要使用ssl安全链接在465端口上！     
请注意，EMAIL_USE_TLS与EMAIL_USE_SSL是互斥的，因此只能将其中一个设置设置为True。     
23.EMAIL_SSL_CERTFILE     
默认值：None     
如果EMAIL_USE_SSL或EMAIL_USE_TLS为True，则可以选择指定要用于SSL连接的PEM格式的证书链文件的路径。     
24.EMAIL_SSL_KEYFILE     
默认值：None     
如果EMAIL_USE_SSL或EMAIL_USE_TLS为True，可以选择指定要用于SSL连接的PEM格式的私钥文件的路径。     
25.EMAIL_TIMEOUT     
默认值：None     
邮件发送超时时间。     
26.FILE_CHARSET     
默认值：'utf-8'     
从磁盘读取文件时使用的字符编码。包括模板文件和初始SQL数据文件。     
没有特别需求，请不要修改它。     
27.INSTALLED_APPS     
默认值：[]（空列表）     
Django核心配置项！     
当前Django项目中启用的app列表。 每个元素应该是一个Python的点分路径，字符串格式：
项目内每个启用的app，包括Django内置的contrib都必须在这个列表里注册，否则创建数据表、调用功能等等都无法进行。
一个典型的配置如下：
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app1',
    'app2',
]
```
建议在最后一个元素后面添加个逗号。     
28.LANGUAGE_CODE     
默认值：'en-us'     
当前项目所使用的语言。默认为英语。汉语是zh-hans，千万不要写成别的，比如‘chinese’之类！
USE_I18N必须设置为True才能使LANGUAGE_CODE生效。     
29.LANGUAGES     
默认值：所有可用语言的列表。     
该配置项表示可用的语言种类，（语言代码，语言名称）两元组列表，例如：（'ja'， 'Japanese'）。
一般来说，默认值足够了。如果自定义LANGUAGES设置，可以使用ugettext_lazy()函数将语言名称标记为翻译字符串。
下面是一个示例设置文件：
```
from django.utils.translation import ugettext_lazy as _

LANGUAGES = [
    ('de', _('German')),
    ('en', _('English')),
]
```
30.LOCALE_PATHS
默认值：[]（空列表）
Django查找翻译文件的目录列表,例如：
```
LOCALE_PATHS = [
    '/home/www/project/common_files/locale',
    '/var/local/translations/locale',
]
```
Django将在这些路径中查找包含实际翻译文件的目录。          
31.LOGGING     
默认值：日志配置字典。     
日志配置信息。          
32.LOGGING_CONFIG          
默认值：'logging.config.dictConfig'          
用于在Django项目中配置日志记录的可调用项的路径。     
33.MEDIA_ROOT     
默认值：''（空字符串）     
用户上传的文件，所在目录的，文件系统绝对路径。也就是指示上传文件放到哪里。     
例如： "/var/www/example.com/media/"
警告：MEDIA_ROOT和STATIC_ROOT必须设置为不同的值。     
34.MEDIA_URL     
默认值：''（空字符串）     
MEDIA_URL指向MEDIA_ROOT所指定的media文件，用来管理保存的文件。该URL设置为非空值时，必须以斜杠“/”结束。
若你打算在模版中使用{{ MEDIA_URL }}，必须在TEMPLATES的context_processors设置中添加django.template.context_processors.media。
警告:MEDIA_URL和STATIC_URL必须设置为不同的值。     
35.MIDDLEWARE     
默认值：None     
Django1.10中的新功能。要使用的中间件列表。Django-admin命令创建的新项目中，
settings.py文件里默认会为MIDDLEWARE配置项添加一系列Django内置的中间件，我们保持它不变就好了。     
36.ROOT_URLCONF     
默认：未指定     
一个字符串，表示根URLconf的完整Python导入路径。例如："mydjangoapps.urls"。     
每个请求都可以覆盖它，通过设置HTTP请求HttpRequest对象的urlconf属性。但不是闲的，请不要特别设置，使用默认值就好。     
37.SECRET_KEY     
默认值：''（空字符串）
当前Django项目实例的密钥。用于提供cryptographic签名，是一个唯一的并且不可预测的值。比如：
```
SECRET_KEY = '+$*n1_pkko%zaz3!lm8lg208@uj^qy3mcsuy+*ov%ikpvd5$rf'
```
通过django-admin startproject xxx命令创建的项目，会在settings.py中添加随机生成的SECRET_KEY。
如果未设置SECRET_KEY，Django将无法启动。
警告：不要透露该配置项的真实值！     
38.TEMPLATES     
默认值：[]（空列表）
Django模板系统相关的配置。列表中每一项都是一个字典类型数据（类似DATABASE配置），可以配置模板不同的功能。
下面是一个例子：
```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'APP_DIRS': True,
    },
]
```
以下选项适用于所有后端。
BACKEND：
默认：未指定
要使用的模板后端。 内置模板后端有：
```
'django.template.backends.django.DjangoTemplates'
'django.template.backends.jinja2.Jinja2'
```     
通过将BACKEND设置为完全限定路径（即'mypackage.whatever.Backend'），你可以使用第三方提供的模板后端。     
NAME：     
默认值：见下面     
此特定模板引擎的别名，一个标识符，用于在某些情况下指定选择引擎进行渲染。别名在所有已配置的模板引擎中必须是唯一的。     
当没有提供时，如果后端是'mypackage.whatever.Backend'，则其默认名称为'whatever'。     
DIRS：
默认值：[]（空列表）
搜索模版的路径列表。搜索引擎会按照列表的排列顺序查找template资源文件。短路算法，找到即退出，不再往下找。     
APP_DIRS     
默认值：False     
Templates搜索引擎是否应该在已安装的app中查找Template源文件。建议保持打开，即设置为True！
由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'APP_DIRS'已经设置为True了。     
OPTIONS：     
默认值：{}     
传递给模板后端的额外参数。 可用参数因模板后端而异。     
39.TIME_ZONE
默认：'America/Chicago'
时区设置。
注意，这个配置项的值不一定要和服务器的时区一致。例如，一个服务器可上可能有多个Django站点，每个站点都有一个单独的时区设置。
如果要设为中国时间，也就是北京时间，请赋值：TIME_ZONE = 'Asia/Shanghai'。注意是上海，不是北京，囧！
当USE_TZ为False时，它将成为Django存储所有日期和时间数据时，使用的时区。 当USE_TZ为True 时，它是Django显示模板中的时间，
解释表单中的日期，使用的时区。所以，通常我们都将USE_TZ同时设置为False！
注：在Windows 环境中，Django不能可靠地交替其它时区。如果你在Windows上运行Django，TIME_ZONE必须设置为与系统时区一致。     
40.USE_I18N     
默认值：True     
这是一个布尔值，指定Django的翻译系统是否开启。如果设置为False，Django会做一些优化，不去加载翻译机制。     
由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'USE_I18N'已经设置为True了。     
41.USE_L10N     
默认值：False     
一个布尔值，用于决定是否开启数据本地化。如果此设置为True，例如Django将使用当前语言环境的格式显示数字和日期。
由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'USE_L10N'已经设置为True了。     
42.USE_TZ     
默认值：False     
一个布尔值,用来指定是否使用指定的时区(TIME_ZONE)的时间。若为True, 则Django会使用内建的时区的时间；否则, Django将会使用本地的时间。
由django-admin startproject xxx命令创建的Django项目，其settings.py文件中'USE_TZ'已经设置为True了。
如果我们将TIME_ZONE设置成了Asia/Shanghai， 那么务必同时将USE_TZ改成False！     
43.WSGI_APPLICATION     
默认值：None     
Django的内置服务器（例如runserver）将使用的WSGI应用程序对象的完整Python路径。
Django使用WSGI协议与外部进行通信。
由django-admin startproject xxx命令创建的Django项目，将自动创建一个简单的wsgi.py模块，里面有一个可调用的application变量，WSGI_APPLICATION配置项的值就指向这个application变量。
下面是wsgi.py的源码：
```
"""
WSGI config for mysite project.
It exposes the WSGI callable as a module-level variable named ``application``.
For more information on this file, see https://docs.djangoproject.com/en/1.11/howto/deployment/wsgi/
"""

import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")

application = get_wsgi_application()
```
--------------------------------------------------------------------------------
以下是一些子系统或工具框架的相关配置
--------------------------------------------------------------------------------     
44.CACHES     
默认值：
```
{
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    }
}
```
一个嵌套字典，包含所有缓存系统要使用的设置。
CACHES设置必须配置一个default缓存，还可以同时指定任何数量的附加缓存（也可以没有）。
以下是重要的内部配置项目：
BACKEND：
默认值：''（空字符串）
要使用的缓存后端。 内置高速缓存后端有：
```
'django.core.cache.backends.db.DatabaseCache'
'django.core.cache.backends.dummy.DummyCache'
'django.core.cache.backends.filebased.FileBasedCache'
'django.core.cache.backends.locmem.LocMemCache'
'django.core.cache.backends.memcached.MemcachedCache'
'django.core.cache.backends.memcached.PyLibMCCache'
```
通过将BACKEND设置为缓存后端类的完全限定路径（例如mypackage.backends.whatever.WhateverCache），可以使用第三方的缓存后端。）。
LOCATION：
默认值：''（空字符串）
要使用的缓存的位置，可能是文件系统缓存的目录，内存缓存服务器的主机和端口，或者只是本地内存缓存的标识名称。 例如：
```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',
    }
}
```
OPTIONS:     
默认值：None     
传递到缓存后端的额外参数。可用参数因缓存后端而异。     
TIMEOUT:     
默认值：300     
高速缓存的有效时间。 如果此设置的值为None，则缓存将永远不会过期。     
VERSION：     
默认值：1     
Django服务器生成的缓存键的默认版本号。     
45.AUTHENTICATION_BACKENDS     
默认值：['django.contrib.auth.backends.ModelBackend']     
在尝试验证用户时使用的认证后端的列表。默认使用Django自带的Auth框架。     
46.AUTH_USER_MODEL     
默认值：'auth.User'     
默认使用的User模型。          
47.LOGIN_REDIRECT_URL     
默认：'/accounts/profile/'     
登录之后，如果contrib.auth.login视图找不到next参数，请求将被重定向到该URL。     
48.LOGIN_URL     
默认：'/accounts/login/'     
登录页面的URL。     
49.LOGOUT_REDIRECT_URL     
默认值：None     
使用LogoutView视图退出登录后，请求被重定向的URL。如果设置为None，则不执行重定向。          
50.PASSWORD_RESET_TIMEOUT_DAYS     
默认：3     
重置密码的链接，的有效期，的天数。（逗号分开，是不是更好理解一点？） 用于django.contrib.auth的密码重置功能。     
51. PASSWORD_HASHERS     
密码哈希使用的算法。     
默认：
```
[
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
    'django.contrib.auth.hashers.BCryptPasswordHasher',
]

在Django1.10时，以下hashers从默认值中删除：
'django.contrib.auth.hashers.SHA1PasswordHasher'
'django.contrib.auth.hashers.MD5PasswordHasher'
'django.contrib.auth.hashers.UnsaltedSHA1PasswordHasher'
'django.contrib.auth.hashers.UnsaltedMD5PasswordHasher'
'django.contrib.auth.hashers.CryptPasswordHasher'
```
52.MESSAGE_LEVEL     
默认值：messages.INFO     
设置Django内置的消息框架-message，将记录的最低消息级别。     
53.MESSAGE_STORAGE     
默认值：'django.contrib.messages.storage.fallback.FallbackStorage'     
控制Django在哪里存储消息数据。 有效值为：     
```
'django.contrib.messages.storage.fallback.FallbackStorage'
'django.contrib.messages.storage.session.SessionStorage'
'django.contrib.messages.storage.cookie.CookieStorage'
```     
54.SESSION_COOKIE_AGE     
默认：1209600（2个星期）     
会话Cookie的过期时间，以秒为单位。     
55.SESSION_COOKIE_NAME     
默认值：'sessionid'     
要用于会话的Cookie的名称。 名字随意，只要与应用程序中的其他cookie名称不同。          
56.SESSION_ENGINE     
默认值：'django.contrib.sessions.backends.db'     
会话使用的后端，也就是会话数据的保存未知。 内置支持的引擎有：     
```
'django.contrib.sessions.backends.db'
'django.contrib.sessions.backends.file'
'django.contrib.sessions.backends.cache'
'django.contrib.sessions.backends.cached_db'
'django.contrib.sessions.backends.signed_cookies'
```     
57.SESSION_EXPIRE_AT_BROWSER_CLOSE     
默认值：False     
是否在用户关闭浏览器时过期会话。     
58.SITE_ID     
默认：未指定     
当前站点在django_site数据库表中的ID，一个整数，从1开始计数。     
很多人都不知道Django是支持多站点同时运行的。
通常我们都只有一个站点，所以不关心这个选项。如果你同时运行了多个站点，那么每个app就得知道自己是为那个站点或哪些站点服务的，这就需要SITE_ID参数了。     
59.STATIC_ROOT     
默认值：None
在DEBUG设置为False时，也就是线上环境时，Django项目里的静态文件（js\css\plugins）会无法使用。这是，需要运行python manage.py collectstatic，
将静态文件统一收集到一个目录下。STATIC_ROOT配置的就是该目录的绝对路径。
示例："/var/www/example.com/static/"
这个目录，刚开始应该是一个空目录。     
60.STATIC_URL     
默认值：None     
引用位于STATIC_ROOT中的静态文件时使用的网址。    
示例："/static/"或"http://static.example.com/"    
该URL设置为非空值时，必须以斜杠“/”结束。              
61.STATICFILES_DIRS     
默认值：[]（空列表）     
定义额外的静态文件搜索地址。定义额外的静态文件搜索地址     。

例如：
```
STATICFILES_DIRS = [
    "/home/special.polls.com/polls/static",
    "/home/polls.com/polls/static",
    "/opt/webfiles/common",
]
请注意，即使在Windows上（例如"C:/Users/user/mysite/extra_static_content"），这些路径也要使用Unix样式的正斜杠。
--------------------------------------------------------------------------------
如果你实在分不清楚MEDIA_ROOT、MEDIA_URL、STATIC_ROOT、STATIC_URL和STATICFILES_DIRS的区别，下面是一个参考版的设置:
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/1.11/howto/static-files/

STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "static"),
]
STATIC_ROOT = os.path.join(BASE_DIR, "all_static_files")


MEDIA_ROOT = os.path.join(BASE_DIR, 'media').replace("\\", "/")
MEDIA_URL = '/media/'
```