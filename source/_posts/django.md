---
title: django
date: 2019-10-29 17:05:41
tags: django

---

### 安装python3
```shell
wget https://www.python.org/ftp/python/3.6.9/Python-3.6.9.tgz
tar -xzvf Python-3.6.9.tgz
cd Python-3.6.9
./configure --with-ssl
make
sudo make install
```


### 安装Django
- 方法一
```shell
sudo pip3 install Django==2.1
```
- 方法二 (此方法安装的为最新版本)
```shell
git clone https://github.com/django/django.git
sudo pip3 install -e ./django
```
- 检测模块是否安装成功
```shell
python3 -c "import django;print(django.get_version())"
```
如果安装django2.2以上版本，需要安装SQLite 3.8.3以上版本

### 创建项目
```shell
mkdir myproject
cd myproject
django-admin startproject mysite .
python3 manage.py runserver
```
---

- **<font color=#FF0000 size=4 face="黑体" >报错</font>**

运行的`python3 manage.py runserver`出现了报错，`django.db.utils.NotSupportedError: URIs not supported`，是SQLite 的原因，SQLite 3.8以前的版本都有这个问题。

查看SQLite的版本，命令如下：
```shell
$ sqlite3 --version
3.6.20
$ python3 -c "import sqlite3; print(sqlite3.version)"
2.6.0
$ python3 -c "import sqlite3; print(sqlite3.sqlite_version)"
3.6.20
```
- **<font color=#FF0000 size=4 face="黑体" >解决办法：</font>**
修改`/usr/local/lib/python3.6/site-packages/django/db/backends/sqlite3/base.py`
  的155行的'uri': True ，True 该给False。

```shell
155         kwargs.update({'check_same_thread': False, 'uri': True})
156         return kwargs
```
然后重新运行`python3 manage.py runserver`

---
然后在浏览器中输入 `http://127.0.0.1:8000/` 即可看到django创建的网页

但是如果在服务器上，需要修改settings.py脚本中的host地址，
```shell
.
├── db.sqlite3
├── manage.py
└── mysite
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-36.pyc
    │   ├── settings.cpython-36.pyc
    │   ├── urls.cpython-36.pyc
    │   └── wsgi.cpython-36.pyc
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```
将`ALLOWED_HOSTS = []` 改为 `ALLOWED_HOSTS = ['172.26.0.23']` 然后运行`python3 manage.py runserver 0.0.0.0:8000`
然后在浏览器中输入`http://172.26.0.23:8000/` 即可看到django创建的网页

### 创建应用
```
python3 manage.py startapp blog
.
├── blog
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── db.sqlite3
├── manage.py
└── mysite
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-36.pyc
    │   ├── settings.cpython-36.pyc
    │   ├── urls.cpython-36.pyc
    │   └── wsgi.cpython-36.pyc
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```
创建完应用，需要进行如下配置：

----
- 设置 `./mysite/setttings.py`文件，添加blog应用：
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
]
```

- 设置 ./blog/models.py文件，其实就是设置数据储存的字段：
```python
from django.db import models
from django.utils import timezone
from django.contrib.auth.models import User

# Create your models here.
class BlogArticles(models.Model):
    title = models.CharField(max_length=300)
    author = models.ForeignKey(User, related_name="blog_posts", on_delete=models.CASCADE)
    body = models.TextField()
    publish = models.DateTimeField(default=timezone.now)

    class Meta:
        ordering = ("-publish",)

    def __str__(self):
        return self.title
```
- 运行 `python3 manage.py makemigrations` 创建模型
- 运行`python3 manage.py migrate` 创建数据库

----

### 发布博客文章
1. 首先要创建超级用户，设置用户名，邮箱和密码。
```shell
python3 manage.py createsuperuser
```
然后运行`python3 manage.py runserver 0.0.0.0:8000`在浏览其中输入`http://172.26.0.23:8000/admin/` 就会出现下面的界面：
![Django 登录](http://pz8nrenoa.bkt.clouddn.com/login.png "django登录")
输入刚刚设置的账户和密码即可登录，登录完成的界面如下所示：
![blog](http://pz8nrenoa.bkt.clouddn.com/blog.png)

2. 要想发表博客，需要做如下设置：
编写`./blog/admin.py`:
```python
from django.contrib import admin
from .models import BlogArticles

# Register your models here.
class BlogArticlesAdmin(admin.ModelAdmin):
    list_display = ("title", "author", "publish")
    list_filter = ("publish", "author")
    search_fields = ('title', "body")
    raw_id_fields = ("author",)
    date_hierarchy = "publish"
    ordering = ['publish','author']

admin.site.register(BlogArticles,BlogArticlesAdmin)
```
刷新页面，就可以看到发布博客的位置：
![添加博客](http://pz8nrenoa.bkt.clouddn.com/blog_write.png)

### 显示博客信息
- 显示文章标题
   - 配置./blog/views.py
   ```python
   from django.shortcuts import render
   from .models import BlogArticles

   # Create your views here.
   def blog_title(request):
       blogs = BlogArticles.objects.all()
       return render(request, "blog/titles.html", {"blogs":blogs})
   ```
   - 在./blog/文件夹下创建templates文件夹，并创建如下文件：
   ```
   templates
   ├── base.html
   └── blog
       └── titles.html
   ```
   - 修改base.html文件：
   ```js
   <! DOCTYPE html>
   <html lang="zh-cn">
   <head>
       <meta http-equiv="X-UA-Compatible" content="IE=Edge">
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1">
       <title>{% block title %}{% endblock %}</title>
       <link rel="stylesheet" href="http://necolas.github.io/normalize.css/">
       <link rel="stylesheet" href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css">
   </head>
   <body>
   <div class="container">
       {% block content %}
       {% endblock %}
   </div>
   <script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
   <script src="http://libs.baidu.com/boostrap/3.0.3/js/bootstrap.min.js"></script>
   </body>
   </html>
   ```
   - 修改./templates/blog/titles.html文件：
   ```js
   {% extends "base.html" %}
   {% block title %}blog titles{% endblock %}
   {% block content %}
   <div class="row text-center vertical-middle-sm">
       <h1>我的博客</h1>
   </div>
   <div class="row">
       <div class="col-xs-12 col-md-8">
           <ul>
               {% for blog in blogs %}
               <li>{{ blog.title }}</li>
               {% endfor %}
           </ul>
       </div>
       <div class="col-xs-6 col-md-4">
           <h2>广告</h2>
           <p>跟老齐学：www.itdiffer.com</p>
           <img width="200px" src="http://pydi4imsi.bkt.clouddn.com/1001.jpg">
       </div>
   </div>
   {% endblock %}   
   ```
   - 修改./mysite/urls.py文件：
   ```python
   from django.contrib import admin
   from django.conf.urls import url,include

   urlpatterns = [
       url(r'^admin/', admin.site.urls),
       url(r'^blog/', include('blog.urls')),
   ]
   ```
   - 修改./blog/urls.py文件：
   ```python
   from django.conf.urls import url
   from . import views

   urlpatterns=[
       url(r'^$', views.blog_title, name="blog_title"),
   ]
   ```
   然后在浏览器中输入`http://172.26.0.23:8000/blog/`即可看到如下界面：
   <img src="http://pz8nrenoa.bkt.clouddn.com/my-blog.png" alt="my blog" style="zoom:75%;" />
- 查看文章内容
   - 修改titles.html：
   ```js
   {% for blog in blogs %}
       <li><a href="{{ blog.id }}">{{ blog.title }}</a></li>
   {% endfor %}
   ```
   - 在./blog/views.py中添加文章请求的函数blog_article():
   ```python
   def blog_article(request, article_id):
       article = BlogArticles.objects.get(id=article_id)
       pub = article.publish
       return render(request, "blog/content.html", {"article":article , "publish":pub })
   ```
   - 创建模板./templates/blog/content.html文件：
   ```js
   {% extends "base.html" %}
   {% block title %}blog article{% endblock %}
   {% block content %}
   <div class="row text-center vertical-middke-sm">
       <h1>{{ article.title }}</h1>
   </div>
   <div class="row">
       <div class="col-xs-12 col-md-8">
           <p class="text-center"><span>{{ article.author.username }}      </span><span style="margin-left:20px">{{ article.publish }}</span></p>
           <div>{{ article.body }}</div>
       </div>
       <div class="col-xs-6 col-md-4">
           <h2>广告</h2>
           <p>跟老齐学：www.itdiffer.com</p>
           <img width="200px" src="http://pydi4imsi.bkt.clouddn.com/1001.jpg">
       </div>
   </div>
   {% endblock %}
   ```
   - 修改./blog/urls.py
   ```js
   from django.conf.urls import url
   from . import views
   
   urlpatterns=[
       url(r'^$', views.blog_title, name="blog_title"),
       url(r'(?P<article_id>\d)/$', views.blog_article,     name="blog_detail"),
   ]
   ```
   
   - 然后刷新页面，点击文章标题，即可出现下面的界面：
   
     <img src="http://pz8nrenoa.bkt.clouddn.com/wenzhang.png" alt="文章" style="zoom:75%;" />

### django的MTV

- **M**: 模型（Model），即数据存取层，模型是网站项目的基础，主要负责处理与数据相关的事务，如读取、写入数据等。
- **T**:模板（Template）,即表现层，处理与表现相关的事务，例如如何在页面中显示相关内容。
- **V**:视图（Views），即业务逻辑层，包含存取模型及调取相应模板的相关逻辑， 是 M （模型）和 T （模板）之间的桥梁。当 Django 得到用户的请求后，根据 URL 映射关系调用相应的视图，视图则调用和处理有关数据。与模板相比，视图确定访问者能看到哪些数据，而模板确定怎么看到这些数据（或者说用什么方式看到这些数据）。

参考链接：[https://simpleisbetterthancomplex.com/series/beginners-guide/1.11/](https://simpleisbetterthancomplex.com/series/beginners-guide/1.11/)
