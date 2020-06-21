---
layout: post
title:  "Django实现迷你淘宝（四）--- 基于Bootstrap的商品页面设计与美化"
date:   2020-05-13
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

# Django实现迷你淘宝(四) --- 基于Bootstrap的商品页面设计与美化
本文[代码](https://github.com/xkw168/blog-code/tree/f96c8e3e9918bf738cb40aef6bb2b1bf239e2760)
@[toc]

## 1. Bootstrap的导入
首先我们需要在我们的项目中引入Bootstrap，这里有两种方式：
1. 下载Bootstrap（[地址](https://getbootstrap.com/docs/4.4/getting-started/download/)）
2. 使用在线CDN导入

这里我们使用在线的方式，还记得我们之前新建的`base.html`文件吗？现在就是它大展身手的时候了。

打开`base.html`，在`head`里面添加
```html
<!-- Bootstrap CSS -->
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css"
          integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
<!-- Bootstrap fontawesome icon -->
<link href="//maxcdn.bootstrapcdn.com/font-awesome/4.1.0/css/font-awesome.min.css" rel="stylesheet">
```
然后在`body`里面，`script`上面添加
```html
<!-- Optional JavaScript -->
<!-- jQuery first, then Popper.js, then Bootstrap JS -->
<script src="https://code.jquery.com/jquery-3.4.1.slim.min.js"
        integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n"
        crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js"
        integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo"
        crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"
        integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6"
        crossorigin="anonymous"></script>
```
此时你的`base.html`应该是这样的

```html
<!DOCTYPE html>
<html lang="en">
<head>
    {% load static %}
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css"
          integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <!-- Bootstrap fontawesome icon -->
    <link href="//maxcdn.bootstrapcdn.com/font-awesome/4.1.0/css/font-awesome.min.css" rel="stylesheet">

    <title>迷你淘宝</title>
</head>
<body>

<main role="main">
    {% block content %}
    {% endblock %}
</main>

<!-- Optional JavaScript -->
<!-- jQuery first, then Popper.js, then Bootstrap JS -->
<script src="https://code.jquery.com/jquery-3.4.1.slim.min.js"
        integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n"
        crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js"
        integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo"
        crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"
        integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6"
        crossorigin="anonymous"></script>

{% block script %}
{% endblock %}
</body>
</html>
```
至此，我们就成功把Bootstrap引入了我们的项目。

## 2. Bootstrap基本介绍
Bootstrap是一个以mobile优先的库，这意味着，你用Bootstrap开发的网页都可以很好地兼容移动端访问。Bootstrap使用“栅格系统”作为基本的排版，它把整个页面横向分为了12列，你可以通过控制每个元素占据几列来控制彼此间的相对位置。为了使用这个默认的“栅格系统”，我们需要使用`container`类来包含整个页面（[container介绍](https://getbootstrap.com/docs/4.4/layout/overview/#containers)）。还记得我们的`base.html`吗？我们只需要增加一行代码，在原来的main上面增加一个class，如下
```html
<main role="main" class="container">
```
## 3. 添加导航栏
导航栏基本上是大部分网站的标配（如你正在看的CSDN），我们自然也不能落下。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200501035022592.png)
因为我们希望给每个页面都添加导航栏，所以我们还是直接在`base.html`上面进行修改。
### 3.1 添加main.css
为了方便统一整个网站的样式，我们先创建一个全局的CSS文件，定义了所有页面共用的一些样式（如header样式）。
新建文件`users/static/users/main.css`并写入如下内容：
```css
body {
    background: #fafafa;
    color: #333333;
    margin-top: 5rem;
}

h1, h2, h3, h4, h5, h6 {
    color: #444444;
}

ul {
    margin: 0;
}

.bg-steel {
    background-color: #5f788a;
}

.site-header .navbar-nav .nav-link {
    color: #cbd5db;
}

.site-header .navbar-nav .nav-link:hover {
    color: #ffffff;
}

.site-header .navbar-nav .nav-link.active {
    font-weight: 500;
}

.content-section {
    background: #ffffff;
    padding: 10px 20px;
    border: 1px solid #dddddd;
    border-radius: 3px;
    margin-bottom: 20px;
}
```
这里大部分的样式都是为了导航栏服务（后面会添加更多样式），有兴趣的同学可以仔细看看。
同时我们需要在代码里导入该css文件，打开`base.html`在`head`里面添加
```html
{% load static %}
<link rel="stylesheet" type="text/css" href="{% static 'users/css/main.css' %}">
```
此时我们已经成功把css文件应用到所有页面，接下来我们一起添加导航栏吧。
### 3.2 添加header
打开`base.html`，在`body`里面，`main`上面 ，添加`<header>`
```html
<header class="site-header">
    <!-- initial a navigation bar -->
    <nav class="navbar navbar-expand-md navbar-dark bg-steel fixed-top">
        <div class="container">
            <a class="navbar-brand mr-4" href="{% url 'home' %}">迷你淘宝</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarToggle"
                    aria-controls="navbarToggle" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <!-- use mr-auto to push all other elements to the right side -->
            <div class="collapse navbar-collapse" id="navbarToggle">
                <!-- use mr-auto to push all other elements to the right side -->
                <ul class="navbar-nav mr-auto">
                    <li>
                        <a class="nav-item nav-link" href="{% url 'home' %}">首页</a>
                    </li>
                </ul>

                <!-- Navbar Right Side -->
                <ul class="navbar-nav">
                    {% if user.is_authenticated %}
                        <li class="nav-item">
                            <a class="nav-item nav-link" href="#">
                                <i class="fa fa-shopping-cart"></i>
                            </a>
                        </li>
                        <li class="nav-item dropdown">
                            <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button"
                               data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                                选项
                            </a>
                            <div class="dropdown-menu" aria-labelledby="navbarDropdown">
                                <a class="dropdown-item" href="#">订单</a>
                                <a class="dropdown-item" href="{% url 'profile' %}">用户详情</a>
                                <div class="dropdown-divider"></div>
                                <a class="dropdown-item" href="{% url 'logout' %}">注销</a>
                            </div>
                        </li>
                    {% else %}
                        <li class="nav-item">
                            <a class="nav-item nav-link" href="{% url 'login' %}">登录</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-item nav-link" href="{% url 'register' %}">注册</a>
                        </li>
                    {% endif %}
                </ul>
            </div>
        </div>
    </nav>
</header>
```
关键的地方我都写了注释，其他的基本都是固定写法，调用Boostrap的内置css样式，直接复制就好。
此时运行程序打开网页，将会看到这样的导航栏：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200501042951214.png)
常见问题：
1. 假如此时打开网站看不到导航栏
多半是因为css文件未加载成功导致（可以通过F12的开发者工具来确认一下），可以去`setting.py`里面添加一行（加在STATIC_URL上面）
```python
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

## 4. 登录注册页面
### 4.1 注册页面
先来看看现在的注册页面（没改过代码）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200501043532451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
可以看到，虽然我们没有修改任何之前的代码，仅仅是导入了Bootstrap并用container包含所有html，这个页面已经好看了不少。
下面我们再稍微美化一下该页面：
首先给页面添加一个白色背景，用`<div cla="content-section"></div>`包含整个表单。我们来看看main.css里面的定义
```css
.content-section {
    background: #ffffff;
    padding: 10px 20px;
    border: 1px solid #dddddd;
    border-radius: 3px;
    margin-bottom: 20px;
}
```
这里我们定义了背景颜色为纯白，然后增加了一个实线边框（border），并定义圆角弧度为3px。

然后增加登录链接：
```html
<div class="border-top pt-3">
    <small class="text-muted">
        Already Have An Account? <a class="ml-2" href="{% url 'login' %}">Login In</a>
    </small>
</div>
```
这里我们通过`border-top`增加一个分隔线，并利用`text-muted`使的字体灰显（更多[字体样式](https://getbootstrap.com/docs/4.4/utilities/colors/#color)）。

此时我们的`register.html`如下：
```html
{% extends "users/base.html" %}
{% block content %}
    <div class="content-section">
        <form method="POST">
            {% csrf_token %}
            <fieldset class="form-group">
                <legend class="border-bottom mb-4">Join Today</legend>
                {{ form.as_p }}
            </fieldset>
            <div class="form-group">
                <button class="btn btn-outline-info" type="submit">Sign Up</button>
            </div>
        </form>
        <div class="border-top pt-3">
            <small class="text-muted">
                Already Have An Account? <a class="ml-2" href="{% url 'login' %}">Log In</a>
            </small>
        </div>
    </div>
{% endblock content %}
```
实际效果如图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200501043657173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
可以看到确实美化了不少（表单由于是django内置生成的，修改起来较为麻烦，这里我们就不详述了）。

### 4.2 登录页面
细心的同学应该发现之前登录页面就已经用到了很多class，没错那都是Bootstrap的内置样式，只不过当时因为没导入Bootstrap所以没有效果而已，现在再次打开登录页面，你会看到如下页面：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200501044804261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
可以看到与注册页面样式很像，你可以模仿注册页面自己实现一下（文章后面也有全部代码）。

## 5. 首页（商品列表+搜索）
首页我们的定位就是展示商品列表，加上搜索功能。
### 5.1 Taobao App + 商品model
前面我们说过，users app是专门用于处理和用户验证相关逻辑的，所以为了引入商品这个概念，我们新建一个App，叫做taobao（具体步骤可见[第二篇](https://blog.csdn.net/kewei168/article/details/105446178)），并把首页的视图函数和url也移植过去。然后在`taobao/models.py`里面新建一个model叫做Item，代表了我们的商品。
```python
class Item(models.Model):
    description = models.CharField(max_length=100, blank=False, null=False)
    price = models.FloatField(default=0.99, blank=False, null=False)
    img = models.CharField(max_length=50, default="/static/img/sample.jpg")
    seller = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)
    on_sell = models.BooleanField(default=True)

    def __str__(self):
        return self.description
```

### 5.2 商品列表
这里我们修改一下首页代码，去掉之前的调试信息，写入如下内容：
```html
<!-- item list -->
{% if items %}
    <div class="row">
        {% for item in items %}
            <div class="col-lg-3 col-md-4 col-sm-6 col-xs-6">
                <div class="listitem">
                    <a href="{% url 'item_detail' item.id %}">
                        <img src="{{ item.img }}" class="img-thumbnail" alt="thumbnail"> 
                        <div class="thumbnail">
                            <div class="caption">
                                 <h4>{{ item.description }}</h4>
                            </div>
                            <div class="caption">
                                <h5>￥ {{ item.price|floatformat:2 }}</h5>
                            </div>
                        </div>
                    </a>
                    <div class="caption">
                        <h6>
                            卖家: {{ item.seller.username }}
                        </h6> 
                    </div>
                </div>
            </div>
        {% endfor %}
    </div>
{% else %}
    <h4 class="m-4">暂无商品</h4>
{% endif %}
```
这里我们遍历了传入的items对象，针对每个商品创建一个`<div></div>`，这里我们用到了一个自己写的样式，listitem，具体代码如下（添加在`main.css`内）：
```css
.listitem {
    float: inside;
    padding: 15px;
    margin: 20px 22px;
    background: #FFFFFF;
    /*opacity: 0.5;*/
    border: 1px solid #647888;
}

.listitem:hover{
    border: 3px solid #7bc27a;
    transition: all 0.5s linear;
}

.listitem a {
    text-decoration: none;
}

.listitem a img {
    width: 180px;
    height: 200px;
}

.listitem a p {
    color: #7bc27a;
    font-size: 14px;
    margin-top: 5px;
    margin-bottom: 5px;
    text-align: center;
}
```
修改首页的视图函数为：
```python
def home(request):
    items = Item.objects.all().filter(on_sell=True).order_by("id")
    if request.method == "POST":
        search = request.POST["search"]
        items = items.filter(description__icontains=search)
    context = {"items": items}
    return render(request, "taobao/home.html", context)
```

接下来我们创建几个商品来测试一下展示效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200501075422766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
大功告成！大家也可以按照自己的想法随意修改这个布局，我这里只是一个展示作用、

### 5.3 搜索框
在html里面添加如下代码
```html
<!-- search bar -->
<form method="POST">
    {% csrf_token %}
    <!-- use offset to implement center in row -->
    <div class="input-group p-2 col-md-8 offset-2 bg-light rounded rounded-pill shadow-sm mt-4 searchbar">
        <input name="search" type="search" placeholder="想找点什么？"
               class="form-control border-0 bg-light" style="outline: none; border: 0;box-shadow: none">
        <div class="input-group-append">
            <button type="submit" class="btn btn-link text-primary">
                <!-- search icon -->
                <i class="fa fa-search"></i>
            </button>
        </div>
    </div>
</form>
```
其实这里就是简单的增加了一个form，然后添加各种Bootstrap样式，并添加一个搜索按钮。

 同时修改home的视图函数为（增加一个POST的处理）:

```python
def home(request):
    context = {}
    items = Item.objects.all().filter(on_sell=True).order_by("id")
    if request.method == "POST":
        search = request.POST["search"]
        items = items.filter(description__icontains=search)
    context["items"] = items
    context["categories"] = Category.objects.all()
    context["category"] = "All"
    return render(request, "amazon/home.html", context)
```
实际效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020050108105164.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)


至此，一个简单的首页就制作完成了！
## 6. 商品详情页面
商品详情页面就比较freestyle了，而且基本原理与前面的也一样，这里就不赘述了，贴一下最后的效果图，有兴趣的同学可以clone最新代码自行修改。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200501082850404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)

本文[代码](https://github.com/xkw168/blog-code/tree/f96c8e3e9918bf738cb40aef6bb2b1bf239e2760)
