---
layout: post
title:  "Django实现迷你淘宝（三）--- 基于django的用户验证系统实现"
date:   2020-04-29
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

# Django实现迷你淘宝(三) --- 基于django的用户验证系统实现

用户验证基本是一个网站的必备功能，至少包含几个功能：
* 用户注册与登录
* 用户验证（有些网页只有登录用户才能访问）
* 用户基本信息修改

Django本身就自带了用户验证系统，但针对不同类型的网站我们可能需要不同的信息，如迷你淘宝，需要知道该用户是否为卖家等，所以自带的验证系统无法完全满足我们的需求，但是我们可以以此为基础，进行开发，更安全也更方便。

本文[代码](https://github.com/xkw168/blog-code/tree/15a01b5295a876e775066823d2b99ac706627b46)

## 1. 数据对象
因为django内置了一个ORM层，我们不需要直接设计数据库的table，我们只需要创建一个用户对象进行操作即可。在[第二篇文章](https://blog.csdn.net/kewei168/article/details/105446178)里面，我们已经创建了一个Profile类，具体代码如下：
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
这里我们通过`from django.contrib.auth.models import User`导入了django自带的User对象，该对象已经包含了用户名，密码，邮箱等常用字段，并且包含了密码相关的所有内容（如，加密储存，安全性验证等），在此基础上，针对我们自己的应用增加了一个`is_seller`字段。

这就是我们需要的所有信息了，下面我们来看看如何把这个类和前端页面结合起来。
## 2. 用户注册
### 2.1 django表单
django对于每一种类型的变量（Boolean，Integer等等）都内置了有一个对应的html样式，想要使用内置的样式，就需要创建一个表单继承django的内置表单。在users文件夹内创建一个`forms.py`文件，写入如下内容：
```python
from django import forms
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User


class UserRegisterForm(UserCreationForm):
    email = forms.EmailField()

    class Meta:
        model = User
        fields = ['username', 'email', 'password1', 'password2']
```
这里我们创建了一个`UserRegisterForm`继承自django自带的`UserCreationForm`，`class Meta`里面，model用于指定该表单对应的“模型”是什么（每个表单都应该有一个对应的“模型”），fields用于指定我们需要该模型内的哪些字段。
### 2.2 视图函数
在`users/views.py`里面增加注册函数
```python
def register(request):
    if request.method == 'POST':
        form = UserRegisterForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect(reverse("home"))
    else:
        form = UserRegisterForm()
    return render(request, 'users/register.html', {'form': form})
```
这里首先判断request是GET还是POST，如果是GET我们就创建一个空白的表单传给html模板，假如是POST我们就从request中读取表单信息，并调用内置的`is_valid()`函数对表单数据进行验证，验证成功的话就保存数据。由于我们在UserRegisterForm里面声明了该表单关联的对象是User，所以`form.save()`会创建并保存一个User对象。注册成功之后，我们将用户重定向回主页面。

同时别忘了去`users/url.py`里面注册该视图函数：
```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.home, name="home"),
    path('register/', views.register, name='register'),
]
```

### 2.3 HTML模板文件
#### 2.3.1 基础模板文件
首先在`users`文件夹下面创建一个`templates`文件夹并在里面创建`users`文件夹，这个文件夹将会用来存放我们所有的HTML模板文件。这里为了以后能更方便的在每个页面使用Bootstrap（下一篇文章会详细讲解），我们首先新建一个`base.html`，写入如下内容：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    {% load static %}
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <title>迷你淘宝</title>
</head>
<body>

<main role="main">
    {% block content %}
    {% endblock %}
</main>

{% block script %}
{% endblock %}
</body>
</html>
```
这个文件会作为其他所有html文件的“容器“，后期我们可以通过这个“容器”，快速将Bootstrap应用到所有界面。
#### 2.3.2 注册页面模板
创建一个`register.html`用于用户注册
```html
{% extends "users/base.html" %}
{% block content %}
    <form method="POST">
        {% csrf_token %}
        <fieldset>
            <legend>Join Today</legend>
            {{ form.as_p }}
        </fieldset>
        <button class="btn btn-outline-info" type="submit">Sign Up</button>
    </form>
{% endblock content %}
```
模板文件里面通过`{{ form }}`即可取得传入的form对象，想要将其渲染出来，只需要用`{{ form.as_p }}`即可完成（还有`{{ form.as_table }}`等其他样式，详见[这里](https://docs.djangoproject.com/en/3.0/topics/forms/#form-rendering-options)），十分的方便。

此时文件目录如下
```shell
.
├── __init__.py
├── admin.py
├── apps.py
├── forms.py
├── migrations
│   ├── 0001_initial.py
│   ├── __init__.py
├── models.py
├── templates
│   └── users
│       ├── base.html
│       └── register.html
├── tests.py
├── urls.py
└── views.py

```
运行程序，输入网址`http://127.0.0.1:8000/register/`，你应该能看到如下页面
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200425083544500.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
我们不需要写一行具体的代码，就可以生成整个表单。不过现在这个表单还很丑，别急，下一篇文章会教你如何使用Bootstrap美化表单。
### 2.4 利用signals创建Profile
细心的读者可能已经发现了一点问题，就是我们通过表单创建的是一个`User`对象，但是我们实际需要的是一个`Profile`对象（包含了我们自己的field），为了解决这个问题，我们又引入一个新的概念，信号（signals），django提供各种各样的信号，你可以通过捕捉这些信号，干一些有趣的事情。
在users文件夹下面新建`signals.py`，并添加如下代码：
```python
from django.db.models.signals import post_save
from django.contrib.auth.models import User
from django.dispatch import receiver
from .models import Profile


@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)


@receiver(post_save, sender=User)
def save_profile(sender, instance, **kwargs):
    instance.profile.save()
```
这里我们捕获了`post_save`信号（使用annotation），这个信号会在`save()`的最后被调用，所以每一次创建一个新的`User`对象，就会调用这个函数并创建一个对应的`Profile`对象。
为了方便管理所有信号，我们单独新建了一个文件，所以我们需要去“注册”一下这些信号，修改`users/apps.py`文件，增加一个ready函数（会在该app运行起来后被调用），并在函数内导入signals文件：
```python
class UsersConfig(AppConfig):
    name = 'users'

    def ready(self):
        # import all signals
        import users.signals
```
常见问题：
1. 如果现在还是无法捕获信号，尝试在`users/__init__.py`里面添加一行`default_app_config = 'users.apps.UsersConfig'`

### 2.5 结果验证
为了验证我们是否注册成功，我们在主页面加一些内容，把所有已注册用户的名字显示出来。
#### 2.5.1 修改`views.py`的`home`函数
```python
def home(request):
    profiles = Profile.objects.all()
    context = {"profiles": profiles}
    return render(request, 'users/home.html', context)
```
这里我们首先通过`Profile.objects.all()`获得所有的已注册用户信息，再将其传入`home.html`
#### 2.5.2 新建`home.html`函数
在`users/templates/users`目录下新建`home.html`文件，并写入如下内容
```html
{% extends "users/base.html" %}
{% block content %}
    <h1>Hello world</h1>
    {% if profiles %}
        <ul>
            <h2>User list</h2>
            {% for p in profiles %}
                <li>{{ p.user.username }}</li>
            {% endfor %}
        </ul>
    {% else %}
        <h2>No user yet.</h2>
    {% endif %}
    <a href="{% url "register" %}">Register</a>
{% endblock %}
```
这里用`{% for p in profiles %}`循环取出所有的已注册用户

成功运行之后，大家访问`127.0.0.1`就可以看到类似的界面了（我这里已经有两个用户）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200429071608710.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
#### 2.5.3 其他验证方法
有的小伙伴可能会说，我只是想验证一下数据是否成功插入，你让我写一个页面，太麻烦了吧。不要急，也有更简单的方法：
* 首先`python manage.py shell`进入django的shell
* 然后`from users.models import Profile`导入我们的类
* 最后`Profile.objects.all()`

结果如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200429071836631.png)
## 3. 用户登录
用户注册后面紧接着肯定是用户登录功能了
### 3.1 登录页面模板
新建`login.html`，并写入
```html
{% extends "users/base.html" %}
{% block content %}
    <div class="content-section">
        <form method="POST">
            {% csrf_token %}
            <fieldset class="form-group">
                <legend class="border-bottom mb-4">Log In</legend>
                {{ form.as_p }}
            </fieldset>

            <div class="form-group">
                <button class="btn btn-outline-info" type="submit">Login</button>
            </div>
        </form>
        <div class="border-top pt-3">
            <small class="text-muted">
                Need An Account? <a class="ml-2" href="{% url 'register' %}">Sign Up Now</a>
            </small>
        </div>
    </div>
{% endblock content %}
```
同样的，这里还是用到django的表单来简化代码，核心代码就是`{{ form.as_p }}`
这个页面没有太多内容，就是一个表单，用于给用户输入登录信息，然后登录按钮。
### 3.2 登录页面视图函数
由于登录页面的处理逻辑十分简单，而且也相对固定（基本逻辑就是获得用户数据，验证，返回结果），所以django连这个都帮我们写好了，在`django.contrib.auth`包下面，我们打开`urls.py`添加一行
```python
path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
```
同时别忘了导入包
```python
from django.contrib.auth import views as auth_views
```
这时候的`urls.py`应该长这样
```python
from django.urls import path
from django.contrib.auth import views as auth_views

from . import views

urlpatterns = [
    path('', views.home, name="home"),
    path('register/', views.register, name='register'),
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout'),
]
```
细心的小伙伴可能发现了，我们没有指定登陆成功后跳转到哪里，假如你试一下你会发现，django默认会跳转到`127.0.0.1;8000/account/profile`页面，但是我们可能想让他跳转到我们的首页，此时你需要修改一下`setting.py`文件，在文件最后面增加一行
```python
LOGIN_REDIRECT_URL = 'home'
```
### 3.3 登出功能
登出和登录十分类似，django也给我们内置好了，直接写一个html并调用内置函数即可，这里就不详细讲解了。
## 4. 用户验证
前面说了这么多，核心的目的还是为了区分“合法用户”和“非法用户”，因为有些页面只有已登录的用户才能访问（如：个人信息，购物车，历史订单等）。用户验证主要有两方面的需求
1. 某个页面只有已登录用户才能访问，未登录的无法访问
2. 某个页面大家都可以访问，但是已登录用户和未登录用户的界面不一样

### 4.1 拒绝未登录用户
这里我们增加一个profile页面，模仿任意只能被已登录用户访问的页面，细节就不再赘述，大致过程：新建一个html ---> 新建一个视图函数 ---> 注册视图函数 ---> 在主页面增加一个链接，跳转到新页面。
此时首页应该长这样：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200429074601886.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
profile页面的html如下:
```python
{% extends "users/base.html" %}
{% block content %}
    <h2>{{ user.username }}</h2>
{% endblock content %}
```
这个页面，我们只是简单的显示一下用户的用户名。
注意，这里我们不需要传入user对象也可以访问，因为django默认的在每个request里面都会有一个user对象，这也是使用django自带验证系统的一大好处。

为了拒绝未登录用户，我们只需要在profile的视图函数里面加一个annotation就好
```python
from django.contrib.auth.decorators import login_required

@login_required
def profile(request):
    return render(request, 'users/profile.html')
```
未登录用户访问这个页面的“正确”操作应该是把他导向登录页面，所以我们在`setting.py`文件里面增加一行，告诉django我们的登录页面URL
```python
LOGIN_URL = 'login'
```
此时，运行程序，直接访问该页面会被重定向到登录界面，登录后再次访问，就会看到你的用户名。

### 4.2 根据用户身份动态加载内容
有时候我们会需要在html里面判断当前用户是否已经登录，并根据结果展示不同内容。这里以首页为例，未登录用户只展示“登录”和“注册”，已登录用户展示“登出”和“Profile”。
修改`home.html`
```python
{% if user.is_authenticated %}
    <a href="{% url "logout" %}">Logout</a>
    <br>
    <a href="{% url "profile" %}">Profile</a>
{% else %}
    <a href="{% url "register" %}">Register</a>
    <br>
    <a href="{% url "login" %}">Login</a>
{% endif %}
```
我们可以直接通过调用`user.is_authenticated`来判断当前用户是否为已登录（已验证）。

本文[代码](https://github.com/xkw168/blog-code/tree/15a01b5295a876e775066823d2b99ac706627b46)
