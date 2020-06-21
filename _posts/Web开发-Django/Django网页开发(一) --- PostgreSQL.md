最近疫情待在家里，天天就是写作业，写写博客换换脑子，顺便总结一下这段时间所学。

本系列打算分为5部分， 分别是：

1. [postgres安装与入门](https://blog.csdn.net/kewei168/article/details/105446135)
2. django安装与入门
3. 使用django内置的验证系统
4. 使用django的表单简化代码
5. 使用Bootstrap美化网页

# Django网页开发(一) --- PostgreSQL

[toc]

## 1. 安装

linux: `sudo apt install postgresql`

mac: 利用[EnterpriseDB](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)进行下载安装，进入官网选择最新版本安装（下载安装包之后就和普通软件一样）

测试安装是否成功：

```
sudo -u postgres psql
```
也可以先通过`sudo su - postgres`切换到postgres用户，然后`psql`进入(若是无法识别`psql`，则用`./bin/psql`)，这里引用管网的一段话描述psql是什么
> Psql is the interactive terminal for working with Postgres. Theres an abundance of flags available for use when working with psql, but lets focus on some of the most important ones, then how to connect:

简单来说，`psql`就是一个可供用户和postgres直接交互的terminal

假如成功安装的话会看到如下的画面
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200411011923333.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70#pic_center)

## 2. 基本操作

成功安装之后，我们先用postgresql自身的API进行一些基本操作熟悉一下sql；
注：以下所有操作均在`psql`环境下完成

### 2.1 新建数据库

新建一个testDB数据库

```
CREATE DATABASE testDB;
```

注意sql语言是不区分大小写的，所以上面的语句会创建一个名叫`testdb`的数据库，如果想要强制大写的话，需要加上双引号`CREATE DATABASE "testDB";`

### 2.2 查看所有的数据库

```
\l
```

利用该指令可以看到所有的数据库，postgres默认会创建一个同名数据库和两个template数据库，可以看到这里有一个testDB数据库，就是我们刚才创建的（ACC_BBALL是我之前创建的）

![table](https://img-blog.csdnimg.cn/20200411012226944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70#pic_center)
### 2.3 查看用户
```
\du
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200411012522326.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tld2VpMTY4,size_16,color_FFFFFF,t_70)

### 2.4 修改用户密码
一般建议都新建一个用户，并且设置一个密码方便以后连接（和安全）。
```
\password userName
```

或者

```
ALTER USER postgres with encrypted password '12345';
```

### 2.5 连接（切换）数据库
```
\c testDB
```
注意，如果要想删除某一个数据库，你不能连接到该数据库

### 2.6 查看数据库内所有table
```
\d
```

### 2.7 查看某一具体表的属性
```
\d <table_name>
```
### 2.8 删除数据库
```
DROP DATABASE testDB;
```

一个很好的常用指令[链接](https://mozillazg.com/2014/06/hello-postgresql.html)
