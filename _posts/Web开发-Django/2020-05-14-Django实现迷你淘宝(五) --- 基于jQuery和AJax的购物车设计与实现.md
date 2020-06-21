---
layout: post
title:  "Django实现迷你淘宝（五）--- 基于JQuery和AJax的购物车设计与实现"
date:   2020-05-14
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

# Django实现迷你淘宝(五) --- 基于jQuery和AJax的购物车设计与实现

@[toc]

本文[代码](https://github.com/xkw168/blog-code/tree/17885f0c26711e9361d687dd41ede69527a5ace5)
## 1. 购物车设计
先来看看最终效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514042044681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
### 1.1 修改模型
分析一下购物车对应的数据库模型，首先必要的信息就是商品信息（如：名字，价格）和对应的数量；其次，一个购物车可能会有多个商品。这里简单起见，我只新建了一个Order类，代表了一个商品和其对应的数量。修改`taobao/models.py	`添加以下内容：
```python
class Order(models.Model):
    # user info
    owner = models.ForeignKey(User, on_delete=models.CASCADE, related_name="orders")
    item = models.ForeignKey(Item, on_delete=models.SET_NULL, null=True)
    item_cnt = models.IntegerField(default=1)

    # return the total price for current order
    def total(self):
        return self.item_cnt * self.item.price

    def __str__(self):
        return "<" + str(self.item_id) + ', ' + str(self.item_cnt) + ">"
```
有了该模型，用户每次添加到购物车我们就只需要创建一个新的`Order`，或者修改已有`Order`的数量。
修改`taobao/views.py`里面的`item_detail`函数（关键部分都已经添加了注释）
```python
def item_detail(request, item_id):
    item = Item.objects.get(pk=item_id)
    context = {}
    if request.method == "POST":
        if not request.user.is_authenticated:
            return redirect(reverse("login"))
        cnt = int(request.POST["count"])
        if request.POST["action"] == "buy":
            # do nothing for now
            print("buy")
        else:
            try:
                # try to get an existing order
                exist_order = Order.objects.get(owner=request.user, item=item, package__isnull=True)
                exist_order.item_cnt += cnt
                exist_order.save()
            except Order.DoesNotExist:
                # create a new order
                order = Order(owner=request.user, item=item, item_cnt=cnt)
                order.save()
        return render(request, "taobao/success.html", context)
    else:
        context["item"] = item
        return render(request, "taobao/item_detail.html", context)
```

### 1.2 HTML模板文件
这里就不详细叙述HTML的具体代码了，有兴趣的同学可以照着最终效果尝试实现一下。

```html
{% extends "users/base.html" %}
{% block content %}
    <h1>购物车</h1>
    <!-- order list -->
    {% if orders %}
        <!-- NOTE: nested form is not allowed!!! -->
        <form id="form_orders" name="form_orders" action="{% url 'shop_cart' %}" method="post">
            {% csrf_token %}
            <div class="table-responsive mt-4 table-hover">
                <table id="order_table" class="table">
                    <thead>
                    <tr class="row text-center">
                        <th class="col-1">
                            <input class="form-check-input" type="checkbox" id="check_all">
                            <label class="form-check-label" for="check_all">#</label>
                        </th>
                        <!-- thumbnail + description -->
                        <th class="col-3 text-left">
                            <a href="#" style="color: #080000;">商品
                                <i class="fa fa-sort"></i>
                            </a>
                        </th>
                        <th class="col-2">
                            <a href="#" style="color: #080000;">数量
                                <i class="fa fa-sort"></i>
                            </a>
                        </th>
                        <th class="col-2">
                            <a href="#" style="color: #080000;">价格
                                <i class="fa fa-sort"></i>
                            </a>
                        </th>
                        <th class="col-2">
                            <a href="#" style="color: #080000;">总计
                                <i class="fa fa-sort"></i>
                            </a>
                        </th>
                        <!-- delete button -->
                        <th class="col-2"></th>
                    </tr>
                    </thead>
                    <tbody>
                    {% for order in orders %}
                        <tr class="row text-center border-bottom">
                            <td class="col-1 align-self-center border-top-0">
                                <input class="form-check-input" type="checkbox" value="{{ order.id }}"
                                       name="checked_orders" id="checkbox{{ order.id }}">
                                <label class="form-check-label"
                                       for="checkbox{{ order.id }}"> {{ forloop.counter }}</label>
                            </td>
                            <td class="col-3 text-left border-top-0">
                                <img class="img-thumbnail" style="width: 50px; height: 50px" src="{{ order.item.img }}">
                                {{ order.item.description }}
                            </td>
                            <td class="col-2 align-self-center border-top-0">
                                <button type="button" class="btn" onclick="change_cnt({{ order.id }}, false)">
                                    <i class="fa fa-minus-square-o"></i>
                                </button>
                                <span id="cnt{{ order.id }}">{{ order.item_cnt }}</span>
                                <button type="button" class="btn" onclick="change_cnt({{ order.id }}, true)">
                                    <i class="fa fa-plus-square-o"></i>
                                </button>
                            </td>
                            <td class="col-2 align-self-center border-top-0">
                                &dollar; {{ order.item.price|floatformat:2 }}</td>
                            <td class="col-2 align-self-center border-top-0">
                                &dollar;
                                <span id="total_order{{ order.id }}">{{ order.total|floatformat:2 }}</span>
                            </td>
                            <td class="col-2 align-self-center border-top-0">
                                <button onclick="delete_order({{ order.id }})" class="btn btn-outline-secondary">
                                    删除
                                </button>
                            </td>
                        </tr>
                    {% endfor %}
                    </tbody>
                </table>
            </div>
            <!-- total price -->
            <div class="row">
                <div class="col-5 offset-7 text-right mt-4 mb-4">
                    <b>总计(所有选中商品): </b>
                    <span style="color: red">&dollar;</span>
                    <span id="total_cart" style="color: red">{{ total|floatformat:2 }}</span>
                </div>
            </div>
            <div class="text-right">
                <button onclick="check_out()" class="btn btn-primary"><i class="fa fa-dollar"></i> 结算</button>
            </div>
        </form>
    {% else %}
        <h4 class="m-4">这里空空如也,
            <a href="{% url 'home' %}">去买点什么吧。</a>
        </h4>
    {% endif %}
{% endblock content %}

<!-- javascript -->
{% block script %}
    <script type="text/javascript">
        function delete_order(order_id) {
            add_operation_type(document.form_orders, "delete")
            const id_field = document.createElement("input")
            id_field.type = "hidden"
            id_field.name = "order_id"
            id_field.value = order_id
            document.form_orders.appendChild(id_field)
            document.form_orders.submit();
        }

        function check_out() {
            add_operation_type(document.form_orders, "checkout")
            document.form_orders.submit()
        }

        function add_operation_type(element, type) {
            const operation_field = document.createElement("input");
            operation_field.type = "hidden"
            operation_field.name = "operation"
            operation_field.value = type
            element.appendChild(operation_field)
        }

        function change_cnt(id, isAdd) {
        }

        // calculate the total price
        function cal_total() {
        }
    </script>
{% endblock script %}
```
这里有很多script函数是空的，别急，后面会一一实现。
### 1.3 视图函数
对应的视图函数主要处理几种情况：删除商品，结算，计算总价（所有选中的商品）。
```python
@login_required
def shop_cart(request):
    orders = Order.objects.filter(owner=request.user)
    if request.method == 'POST':
        operation = request.POST["operation"]
        # user delete some order
        if operation == "delete":
            oid = request.POST["order_id"]
            orders.get(pk=oid).delete()
        elif operation == "checkout":
            # get all checked orders
            checked_orders = request.POST.getlist("checked_orders")
            print(checked_orders)
            # will only create a new package when at least one order is chosen
            if len(checked_orders) > 0:
                return render(request, "taobao/success.html")
        # api for calculating the total price
        elif operation == "cal_total" and request.is_ajax():
            checked_orders = request.POST.getlist("checked_orders")
            total = 0.0
            for o in checked_orders:
                total += orders.get(pk=o).total()
            return JsonResponse({"total_cart": ("%.2f" % total)})
    total = 0
    for o in orders:
        total += o.total()
    context = {"orders": orders, "total": total}
    return render(request, "taobao/shopping_cart.html", context)
```
注：别忘了去`urls.py`里面“注册”一下该函数
介绍完基本的购物车样式，下面就来一一实现具体的功能吧~

## 2. 删除商品
删除功能比较好实现，我们只需要在用户点击删除按钮时，向表单里面插入一些隐藏的输入信息，一并提交，然后代码里获取相关数据即可。
* 首先通过`onclick="delete_order({{ order.id }})"`，使得用户每次点击删除按钮时，都会调用该javaScript函数并传入待删除的订单id
* 然后在`delete_order`函数里面，首先插入一个operation说明是想“删除”，然后再插入一个订单id的信息，最后一并提交整个表单
* 视图函数里面，检测到operation是delete的话，就获取对应的订单id，然后删除即可

## 3. 动态修改商品数量
细心的小伙伴可能已经发现，我们数量左右的加减按钮是用`<button>`实现的，所以一个很直接的想法就是，使用和删除商品类似的方案，调用一个JavaScript函数，然后向表单里面插入一些信息，再提交。原理上来说这个方案是完全可行的，但是面临着一个问题，就是“**页面刷新**”。注意，至今为止，我们的视图函数都是直接返回一个新的html（整个页面），所以前端会刷新整个页面，而这个在某些情况下十分的用户不友好，试想你滚动到了页面底端，修改了一个商品数量，结果整个页面刷新并回到顶端，是不是很“诡异”。
仔细分析需求我们会发现，我们需要刷新的内容只有，*该商品总价*，*该商品数量*和*购物车总价*三个变量。所以，这里我们使用AJax来实现页面的**局部刷新**。
### 3.1 修改数量API
首先，我们需要有一个API，能够修改某一个商品的数量（加一或减一）。
在`views.py`里面添加一个新的函数`change_cnt()`
```python
@login_required
def change_cnt(request):
    if request.is_ajax() and request.method == "POST":
        order_id = request.POST["order_id"]
        operation = request.POST["operation"]
        total_cart = float(request.POST["total_cart"])
        order = Order.objects.get(pk=order_id)
        # lower and upper limit --- 1 ~ 99
        if operation == "add" and order.item_cnt < 99:
            order.item_cnt += 1
            order.save()
            total_cart += order.item.price
        elif operation == "minus" and order.item_cnt > 1:
            order.item_cnt -= 1
            order.save()
            total_cart -= order.item.price
        data = {
            # latest count
            "cnt": order.item_cnt,
            # total price for the order
            "total_order": ("%.2f" % order.total()),
            # total price for all
            "total_cart": ("%.2f" % total_cart)
        }
        return JsonResponse(data)
    return JsonResponse({})
```
整体思路也很简单：
1. 判断请求是否为`POST`并且是`AJax`
2. 获取当前数据：订单ID，具体操作（加or减），当前购物车总价
3. 根据订单ID获得具体的对象，并执行对应操作
4. 返回最新数据：最新订单数量，该商品总价，购物车总价

这里比较有趣的一个点是，我们的返回值不再是`render`或者`redirect`，而是一个`JsonResponse`，只包含了我们感兴趣的数据，而不是整个html页面，所以前端可以从这里面获得感兴趣的内容并且更新对应元素。
注：别忘了去`urls.py`里面“注册”一下该函数
### 3.2 引入jQuery
为了使用AJax和jQuery我们首先需要下载最新的jQuery并引入项目。
* 下载最新的jQuery，[地址](https://jquery.com/download/)
* 放到`taobao/static/js`目录下面
* 添加`<script src="{% static "js/jquery-3.5.0.min.js" %}"></script>`到`base.html`里面（放到最下面，紧跟着bootstrap的三个script）
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020051402360342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)
### 3.3 AJax异步请求
有了API之后，我们就可以在前端调用这个API了。需要注意的是，我们最好使用异步的方式，因为我们不知道调用这个API需要花费多少时间（网络状态，服务器荷载等），但是我们不希望前端页面“卡住”，所以我们需要使用异步操作。

添加一下内容到JavaScript的`change_cnt`函数里面
```javascript
function change_cnt(id, isAdd) {
    let opera;
    if (isAdd) {
        opera = "add";
    } else {
        opera = "minus";
    }
    const total = $("#total_cart").text();
    // use ajax to communicate with backend, change the count of specific order
    const config = {
        "url": "{% url "change_cnt" %}",
        "async": true,
        "type": "post",
        "dataType": "json",
        "data": {
            "order_id": id,
            "operation": opera,
            "total_cart": total,
            "csrfmiddlewaretoken": "{{ csrf_token }}"
        },
        "success": function (result) {
            $("#total_order" + id).text(result["total_order"]);
            $("#cnt" + id).text(result["cnt"]);
            // only update the total price if this order is checked
            if ($("#checkbox" + id).is(":checked")) {
                $("#total_cart").text(result["total_cart"]);
            }
        },
        "error": function (xhr, status, error) { }
   };
   $.ajax(config);
}
```
这里我们使用了AJax来进行异步请求，并使用了一些jQuery的语法来获取某些元素的当前值。
* 利用`$("#total_cart").text();`来获取当前的购物车总金额
* 注：jQuery里面使用`$("#<id>");`来通过id定位一个元素（别忘了那个**#**）

```javascript
const config = {
    "url": "{% url "change_cnt" %}",
    "async": true,
    "type": "post",
    "dataType": "json",
    "data": {
        "order_id": id,
        "operation": opera,
        "total_cart": total,
        "csrfmiddlewaretoken": "{{ csrf_token }}"
    },
    "success": function (result) {
        $("#total_order" + id).text(result["total_order"]);
        $("#cnt" + id).text(result["cnt"]);
        // only update the total price if this order is checked
        if ($("#checkbox" + id).is(":checked")) {
            $("#total_cart").text(result["total_cart"]);
        }
    },
    "error": function (xhr, status, error) { }
};
```
* 这里整个`config`变量使用键值 对的形式来配置AJax的内容
    * `url`设置该请求的目标地址
    * `async`设置该请求为异步请求（与之对应的为同步/阻塞方式）
    * `type`设置该请求使用POST方式（与之对应的为GET方式）
    * `dataType`设置我们期待的返回值为JSON格式
    * `data`包含了所有的数据
    * `success`成功的回调函数，假如请求返回成功，会调用该函数（在这个函数里面更新UI）
        * 这里的`result`是一个字典，我们可以通过`result["xxx"]`获得里面的值
        * 同时我们可以通过jQuery获得任意一个元素，并通过`.text()`修改元素的值
    * `error`请求失败的回调函数
* 利用`$.ajax(config);`来提交AJax请求

现在，保存并运行程序，去享受你的成果吧！
## 4. 动态选择商品
我们不可能每次结算都“清空”购物车（虽然这是梦想，但是现实很骨感），所以我们需要有“选中”功能（包括全选，反选等），用户可以选中这次想购买的物品，并结算，其他商品不受影响。
仔细分析一下需求，我们至少需要做到：
1. 全选（全不选）整个购物车
2. 选择任意商品
3. 购物车总金额根据选择的商品动态变化
4. 全选框会根据当前状态动态调整（如，你手动勾选了所有商品，那么此时全选框也应该被自动勾选）
5. 用户每次刷新页面默认全选购物车（也可以默认全不选）

下面我们来一一实现这些功能，注意UI部分（checkbox）我们已经在前面的章节实现了，所以这里我们主要看背后的逻辑函数。

### 4.0 计算总价函数
首先我们先补全之前的`cal_total()`函数，用于计算并更新当前购物车所有选中商品的总价。
```javascript
function cal_total() {
    add_operation_type(document.form_orders, "cal_total")
    const config = {
        "url": "{% url "shop_cart" %}",
        "async": true,
        "type": "post",
        "dataType": "json",
        "data": $("#form_orders").serialize(),
        "success": function (result) {
            $("#total_cart").text(result["total_cart"]);
        },
        "error": function (xhr, status, error) { }
        };
        $.ajax(config);
}
```
这个函数很简单，和修改商品数量基本一样，发送整个表单到后台，后台计算总额之后，返回结果并局部刷新总金额。
比较有趣的一点就是，这里我们使用`$("#form_orders").serialize()`来把整个表单序列化并传回后台，这样后台就可以和处理普通表单提交一样的方式处理。
这个函数就实现了功能3（动态更新总价），我们只需要在每个可能影响总价的操作最后调用一下这个函数即可。

### 4.1 全选（全不选）购物车
这个功能实现起来也比较简单，基本思路就是监听全选框，一旦被勾选（或者取消），就自动勾选（取消）所有的checkbox。具体实现代码如下：
```javascript
$("#check_all").on("click", function () {
    if (this.checked) {
        // use "prop" to set the value of all checkboxes
        $(":checkbox").prop("checked", true);
    } else {
        $(":checkbox").prop("checked", false);
    }
    // refresh total price
    cal_total();
});
```
这里我们用`$("#check_all")`来获得全选框这个元素，然后通过`.on()`函数来监听`click`事件。修改完所有的checkbox状态之后，我们再重新计算总价。
### 4.2 选择任意商品
这里由于我们不仅要实现可选择任意商品（很容易实现，checkbox本身就支持任意选择），我们更需要的是选中（取消）任意一个checkbox之后，检测所有checkbox的状态，并据此更新全选框的状态。
```javascript
const checkboxes = $("input[name='checked_orders']");
checkboxes.on("click", function () {
    if (this.checked) {
        let i = 0;
        for (i = 0; i < checkboxes.length; i++) {
            if (!checkboxes[i].checked) {
                break;
            }
        }
        if (i === checkboxes.length) {
            $("#check_all").prop("checked", true);
        }
    } else {
        $("#check_all").prop("checked", false);
    }
    // refresh total price
    cal_total();
});
```
这里我们首先通过`const c heckboxes = $("input[name='checked_orders']");`获得所有的名字为*checked_orders*的checkbox，并对他们进行监听，一旦监听到任何一个checkbox被选中，则遍历所有的checkbox，假如都被选中了，就把全选框选中，否则保持现状；假如任何一个checkbox被取消选中，则取消全选框的选中状态。同样的，函数最后调用`cal_total();`来更新购物车总价。

### 4.3 默认全选（全不选）
这里基本的实现也很简单，我们只需要等待整个页面加载完成后（确保所有元素都已经渲染完成），把所有checkbox的状态都改为选中（或未选中）。
那么关键问题就在于，如何知道页面什么时候加载完成，别担心，jQuery早就帮我们弄好了。
```javascript
$(function () {
    // do something
})
```
这个函数会在页面加载完成后被自动调用，所以我们只需要在这里面执行我们的操作即可。添加以下两行：
```javascript
$(function () {
    $(":checkbox").prop("checked", true);
    cal_total();
})
```
## 5. 表格排序
有一个也比较常见的功能就是表格排序，用户可以通过点击表头的任意列，从而根据该列的值进行排序。既然这个功能如此常见，没错，jQuery已经有了对应的插件了。下面来一步步看看如何使用
1. 下载[插件tablesorter](https://mottie.github.io/tablesorter/docs/)
2. js文件放到`taobao/static/js`路径下面（与jQuery文件在一起）
3. 购物车的HTML文件里面添加一下代码（script里面）
```javascript
{% load static %}
<script src="{% static "js/jquery.tablesorter.js" %}"></script>
```
4. （可选）：在页面加载完成的回调函数里面配置tablesorter，如：有时候我们只需要某几列可以排序，其他的不可以
```javascript
$(function () {
    $(":checkbox").prop("checked", true);
    cal_total();
    $("#order_table").tablesorter({
        headers: {
            0: {sorter: false}
        }
    });
})
```
这里我们使得第一列（编号）无法排序，其他默认都可以用于排序。

至此，一个基本的购物车就已经完成了，所有的基本功能都已经实现，谢谢每一个耐心看到最后的读者。
本文[代码](https://github.com/xkw168/blog-code/tree/17885f0c26711e9361d687dd41ede69527a5ace5)
