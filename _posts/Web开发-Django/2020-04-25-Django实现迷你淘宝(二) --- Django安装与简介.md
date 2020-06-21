---
layout: post
title:  "Django实现迷你淘宝（二）--- Django安装与简介"
date:   2020-04-25
categories: 专业学习
tag: Django
---

* content
{:toc}


---

欢迎查看本系列的其他文章：

1. [postgres安装与入门](https://blog.csdn.net/kewei168/article/details/105446135)
2. [django安装与入门](https://blog.csdn.net/kewei168/article/details/105446178)
3. [基于django的用户验证系统实现](https://blog.csdn.net/kewei168/article/details/105446311)
4. [基于Bootstrap的商品页面设计与美化](https://blog.csdn.net/kewei168/article/details/105852937)
5. [基于jQuery和AJax的购物车设计与实现](https://blog.csdn.net/kewei168/article/details/106089391)

# Django实现迷你淘宝(二) --- Django安装与简介
@[toc]
## 一、安装Django

### 1.1 安装python

[官网](https://www.python.org/downloads/)下载最新版的python（至少3.0+），按照提示安装

### 1.2 配置Emacs

安装yapf，一款Google出品的python文件自动格式化工具

```shell
pip install yapf
```

打开Emacs

1. `M+x list-packages`列出所有安装包
2. `install py-yapf`，py-yapf会在保存python文件是自动调用yapf调整格式
3. `install elpy`，elpy是emacs下一个python开发环境包，提供了语法高亮，自动补全等特性

要是不喜欢用命令行写代码，IDE推荐使用[PyCharm](https://www.jetbrains.com/pycharm/)，学生邮箱还能申请免费使用JetBrain全家桶。

### 1.3 安装Django

在利用pip安装Django之前，为了保持我们电脑开发环境的整洁（和方便），我们先创建一个新的文件夹，并创立一个新的python3虚拟环境

```shell
mkdir testDjango
cd testDjango
python3 -m venv venv
```

激活虚拟环境（取消的话，`deactivate`）

```shell
source venv/bin/activate
```

输入`python --version`查看版本，应该显示的是python3.0+版本

利用pip安装Django

```shell
pip install django
```

安装完成之后，进入python，输入一下指令
```python
import django
print(django.get_version())
```
或者直接命令行输入
```shell
django-admin --version
```

能正常输出版本号，即Django安装成功

## 二、第一个Django项目

这部分会带领你一步步的建立你的第一个Django项目

### 2.1 基础知识

#### 2.1.1 查看版本号

```shell
python -m django --version
```

#### 2.1.2 新建Django项目

```shell
django-admin startproject miniTaobao
```

利用tree指令（没有的话用brew或者apt安装一下）查看目录，会看到如下的结构：

```shell
.
├── manage.py
└── miniTaoBao
    ├── __init__.py
    ├── asgi.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

* 最外层的HelloWorld目录，只是目录名字，Django不会识别，你可以任意修改
* `manage.py`: 一个命令行工具，让你与`Django`进行交互（如启动server或者新建App）
* 里层的HelloWorld目录是该工程实际的python包名，用于导入里面的文件（如`import miniTaobao.urls`）
* `miniTaoBao/__init__.py`: 一个空文件，用于告诉python这个目录应被识别为一个包
* `miniTaoBao/settings.py`: 设置/配置Django项目的地方
* `miniTaoBao/urls.py`: 该Django项目的URL声明，可以简单的理解为一个该工程支持的所有url的表格

#### 2.1.3 运行项目
首先进入`miniTaobao/settings.py`文件修改一下配置，找到`ALLOWED_HOSTS`，添加`0.0.0.0`。
然后运行以下指令
```shell
python manage.py runserver 0.0.0.0:8000
```
或者可以简写为`python manage.py runserver 0:8000`
这时候你可能会看到这样的红色字提醒
> You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

暂时不用理解是什么，单纯的按照他说的，运行一下`python manage.py migrate`，然后重新运行一下。
此时打开浏览器，输入`0.0.0.0:8000`，你应该可以看到django成功运行的标志
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200411015217811.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)

NOTE：使用0.0.0.0使得其他电脑也可以连接到你的服务器（前提你的电脑公网可见的话），127.0.0.1则只接受本地连接

#### 2.1.4 修改数据库
django默认生成的是使用sqlite作为后端数据库的，这里我们改用PostgreSQL作为我们的数据库。因为sqlite只能本地访问，而postgresql是支持通过多台机器通过网络连接的，而且开源免费，功能也更加强大（具体配置可看前一篇[文章](https://blog.csdn.net/kewei168/article/details/105446135)）。
打开`miniTaobao/settings.py`，找到`DATABASES`，修改为如下
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'testDB',
        'USER': 'postgres',
        'PASSWORD': 'postgres',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```

记得安装postgres的相关python包（否则可能会看到类似错误`Error loading psycopg2 module: No module named 'psycopg2'`）
```shell
pip install psycopg2
```
运行程序，成功运行的话，证明切换数据库成功

**常见问题**
1. Library not loaded: libssl.1.1.dylib
尝试安装`psycopg2-binary`，这个包会包含一些常用的C库文件，如libssl
```shell
pip install psycopg2-binary
```

### 2.2 创建一个App

利用以下指令新建一个App（负责一个特定功能）

```shell
python manage.py startapp users
```
这里我们新建一个users类，专门负责用户验证相关功能，如注册，登录

> Project和App的区别
>  App是一个Web应用，它应该支持某个功能（可以干某些事情），如博客，数据库等。
>  Project是针对一个完整网站的一系列配置文件和App的集合。
>  一个Project可以包含很多个App，而一个App也可以被应用在很多个Project里面。模块化思想！！！

此时我们的项目目录应该变为
```
.
├── db.sqlite3
├── manage.py
├── miniTaoBao
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-37.pyc
│   │   ├── settings.cpython-37.pyc
│   │   ├── urls.cpython-37.pyc
│   │   └── wsgi.cpython-37.pyc
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── users
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── migrations
    │   └── __init__.py
    ├── models.py
    ├── tests.py
    └── views.py
```
可以看到，在miniTaobao的同级目录下多了一个users的文件夹，这里简单解释一下app里每个文件的作用：
* \_\_init\_\_.py： 用于声明该文件夹是一个python package
* admin.py：用于注册你的models（模型），对应数据库里面的数据
* apps.py：声明了这个App的一些相关信息（修改setting.py“安装”该app的时候会用得到）
* migrations：存放了数据库的修改历史（每次makemigrations就会生成一个新的文件，记录修改）
* models.py：用于声明你的models
* tests.py：用于编写Unit test
* views.py：编写你的视图函数（每个视图函数会对应一个url）

注意，此时你只是新建了一个App，但是django还不能识别这个新的App，你还需要“注册”一下。
打开`miniTaobao/setting.py`找到`INSTALLED_APPS`，在最开始添加一行`'users.apps.UsersConfig',`，这样django就会识别新建的App了。

#### 2.2.1 编写第一个视图

进入users目录，分别修改`view.py`和`urls.py`两个文件，内容如下

`view.py`

```python
from django.http import HttpResponse
def home(request):
    return HttpResponse("Hello, world. You're at the user page.")
```

`urls.py`（默认没有，自己新建一个）

```python
from django.urls import path
from . import views
urlpatterns = [
    path('', views.home, name="home"),
]
```

NOTE：每一个App里面都有自己的view和url，一般来说两者是一一对应的，新增一个view，一般就对应一条新的URL

还记得前面说的吗？`miniTaobao/urls.py`记录了整个网站所有的url（当然也包含了这个新App！），所以现在我们需要去那里“登记”一下我们的url，打开`miniTaobao/urls.py`文件，修改成以下的样子（增加了`include`）

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('', include('users.urls')),
    path('admin/', admin.site.urls),
]
```

[include()](https://docs.djangoproject.com/en/3.0/ref/urls/#django.urls.include) 函数使得我们可以“引用”其他的url配置。当Django遇到`include()`函数时，它会截断已经匹配到的url并将剩余部分发送到包含进来的url配置里面进行进一步的解析。`include()`函数让URL更加“插件化”，每一个app都可以有自己的URL匹配规则（如`users/urls.py`），然后每一个app可以被放置到不同的地方。
现在再次运行我们的程序，并打开浏览器输入`0.0.0.0:8000`，你就能看到你刚新建的页面了

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200425032824810.png)

#### 2.2.2 创建一个新的model
Django里面内置了一层ORM（Object-relational mapper）（用于把类映射到sql里面），所以我们不需要去写sql语句，只需要创建修改我们需要的类，剩下的事情，就都交给Django。

修改“model”三步走

1. 在`models.py`里面修改model
2. `python manage.py makemigrations`，针对新的更改，生成migrations
3. `python manage.py migrate`，应用上一步生成的migrations

牢记每次修改模型后，都需要运行`makemigrations`和`migrate`两个步骤，这两个就是告诉Django把我们类的更新，映射到SQL数据库里面（生成修改和执行修改）。

打开`users/models.py`，加入以下内容
```python
from django.contrib.auth.models import User
from django.db import models

# Create your models here.
class Profile(models.Model):
    user = models.OneToOneField(User, primary_key=True, on_delete=models.CASCADE)
    is_seller = models.BooleanField(default=False)

    def __str__(self):
        return f'{self.user.username} Profile'
```
这里我们声明了一个profile类，你可能会好奇这个profile类里面并没有姓名密码等常见字段，这是因为django自带就有一个user类（用于验证），user类里面已经帮我们把这些常用字段封装好了，所以我们声明一个`OneToOneField`，表明每个profile对象对应着一个user对象。
* `python manage.py makemigrations`
```shell
Migrations for 'users':
  users/migrations/0001_initial.py
    - Create model Profile
```
* `python manage.py migrate`
```shell
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions, users
Running migrations:
  Applying users.0001_initial... OK
```

注意：如果此时`python manage.py makemigrations`显示No changes detected，很大可能是你忘了去setting.py里面“注册”这个app。

至此，我们就成功创建了一个新的App，并编写了第一个视图函数，创建了第一个类。

好了，django的基本介绍就到这里了，下一篇文章将会介绍如何利用我们创建的Profile类和django自带的用户验证系统，实现基本的登录注册功能。
[使用django内置的验证系统]()

[本文代码](https://github.com/xkw168/blog-code/tree/f663f1d97548e3b62e79eb3929bf0858bcf9d349)
