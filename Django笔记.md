# Django

## 安装

```shell
pip install django
```

## 项目管理

### 创建项目

	django-admin startproject {项目名}
	项目结构
	project
		- manage.py          -- 管理项目，包括启动，创建app和管理数据
		- project
			- __init__.py
			- asgi.py        -- 接受网络请求
			- settings.py    -- 项目配置
			- urls.py        -- uri和函数的映射关系
			- wsgi.py        -- 接受网络请求

### 创建应用

#### app概念

项目下某个功能模块s的集合，比如一个网站项目下可以有用户系统app，商品系统app

#### 命令

	python manage.py startapp {应用名}

#### 目录结构

```
app
│  admin.py        -- django默认提供的admin后台管理
│  apps.py         -- app启动类
│  models.py       -- 对数据库进行操作
│  tests.py        -- 单元测试用
│  views.py        -- uri映射函数
│  __init__.py
│
└─migrations       -- 数据库变更记录

```

#### 注册应用

在django的settings.py中的INSTALLED_APPS数组中添加app的name，app name的取值为apps.py中的类名，会自动在该类中寻找类的name属性

比如应用为app, INSTALLED_APPS中应该添加'app.apps.AppConfig'

#### 绑定urls

在django的urls.py中的urlpatterns数组中添加path("url",app中views中的对应函数)

```python
from app import views   # app是应用名

urlpatterns = [
    path('admin/', admin.site.urls),
    path('/', views.index),
]
```

### 启动项目

    python manage.py runserver

### 项目结构

![项目结构](./images/1593233853499.png)

----------

#### 项目名

urls.py 负责处理http请求，根据正则表达式，匹配app.views.py中对应的响应函数
**settings.py**

* INSTALLED_APPS

  * 要想在django中运行app，必须将app名填写在INSTALLED_APPS列表

* TEMPLATES

  * DIRS 模板文件夹路径，一般默认写好

* DATABASES

  * 数据库配置，默认为sqlite3，如果想改成mysql,应修改为

    ```
    'default': {
    		'ENGINE': 'django.db.backends.mysql',
    		'NAME': 'myproject',
    		'USER': 'root',
    		'PASSWORD': 'your_password',
    		'HOST': 'your_db_ip',
    	}
    ```

* LANGUAGE_CODE

  * 设置网站的语言，默认英文en-us，简体中文为zh-Hans

* TIME_ZONE

  * 设置时区，中国区为Asia/Shanghai

----------

#### app名

* views.py 负责响应各类http请求
* admin.py 
  * 用于后台管理,models.py中的模型类要在admin.py中注册
  * 使用*admin.site.register(模型名)* 注册
  * 注册后的模型可以直接在后台进行数据的增删改查
  * 可以继承admin.ModelAdmin来自定义模型管理类，控制该模型类在后台显示的效果
* migrations 用于数据迁移
* models.py 
  * 模型开发，不需要在数据库中建表，直接写model类，orm会将对象转化为表中的行
  * 直接继承models.Model，用于和数据库交互
  * 根据类生成表
    * python manage.py  makemigrations
    * python manage.py migrate
* tests.py 做简单的测试，一般不用


----------

#### manage.py

* 管理django项目
* 根据模型生成数据库的表和数据
  * python manage.py  makemigrations
  * python manage.py migrate
* 查看数据库中的数据
  * python manage.py shell
  * from app名.models import 模型名 

## 视图

### 返回http响应

```python
from django.shortcuts import HttpResponse

def index(request):
    return HttpResponse("hello")
```

### 返回网页

views.py下渲染网页，默认优先找应用下的templates，找不到再去项目的templates下找。如果希望优先找项目下的templates，可以在settings.py中配置TEMPLATES的DIRS属性

```python
'DIRS': [os.path.join(BASE_DIR, 'template')],        ## 默认值为[]
```

```python
def add_user(request):
    return render(request, 'user_add.html')

def user_list(request):
    users = []
    return render(request, 'user_list.html',{"data_list": users})  # 渲染模板的同时传输数据，data_list是html模板中引用的变量
```

## 静态文件

优先在应用下新建static文件，应用下的views.py中的会优先找应用下的static文件而不是项目下的static文件

django中推荐的写法

```html
{% load static %}
<link rel="stylesheet" href="{% static 'css/main.css' %}">  <!--在需要加载静态文件的时候用{% static 'static文件夹下的路径' %}-->
```

这种写法的优势是，不写绝对路径，方便迁移静态文件目录，在settings.py中的STATIC_URL中配置即可

## 模板语法

在html中写一些占位符，再由django框架对这些占位符进行解释替换

### 引用变量

引用变量需要用{{ 变量名 }}

````html
{{ name }}  <!--引用变量name-->
````

### 循环

```html
{% for data in data_list %}
<tr>
    <td>{{data.username}}</td>
    <td>{{data.password}}</td>
    <td>{{data.mobile}}</td>
</tr>
{% endfor %}
```

### 条件判断

```html
{% if n == 1 %}
{% elif n == 2 %}
{% else %}
{% endif %}
```

# MVT框架

* Model
* View
	* 响应http请求
	* 根据urls.py中url规则来匹配函数来响应http请求，url规则支持正则表达式
* Template


# 后台管理

## 创建管理员
python manage.py createsuperuser

## 美化
 采用simpleui框架，按照官网指导进行配置，取代django自带的admin后台

 - 安装simpleui
	 - pip3 install django-simpleui
	 - 在settings.py中添加simpleui![settings.py配置](./images/1593352734296.png)
	 - python3 manage.py collectstatic

----------
# 部署到Linux


----------


## 部署
1. 上传代码到服务器
2. 安装需要的组件
	* yum install -y epel-release 
	* yum install python-devel nginx
	*  pip install supervisor
 3. 配置防火墙
	 * setenforce 0
	 *  新建firewalld service
		 3.1. 在/usr/lib/firewalld/services下添加django.xml文件
						```    

									<?xml version="1.0" encoding="utf-8"?>
			
									<service>
									<short>Django</short>
									<description>My Django project.</description>
									<port protocol="tcp" port="8001"/>
									</service>
						```
		3.2. firewall-cmd --permanent --add-service=django   
		3.3. systemctl restart firewalld    
	 * 在阿里云上开放8001端口
4. 使用 uwsgi 来部署   
	4.1. pip install uwsgi --upgrade   
	4.2 uwsgi --http :8001 --chdir /root/Django/  --module Django.wsgi    
	```chdir是项目的根目录，module是有wsgi.py的那个目录```   



## 问题
* ModuleNotFoundError: No module named '_sqlite3'
	1. yum install -y sqlite-devel
	2. 重新make install python3
* SQLite 3.8.3 or later is required (found 3.7.17)
	1.  下载sqlite3
		1.  wget https://www.sqlite.org/2020/sqlite-autoconf-3320300.tar.gz
		2.  tar zxvf sqlite-autoconf-3320300.tar.gz
		3. cd  sqlite-autoconf-3320300
		4. ./configure --prefix=/usr/local
		5.  make && make install
	2. 替换老版本
		1. mv /usr/bin/sqlite3  /usr/bin/sqlite3_old
		2. ln -s /usr/local/bin/sqlite3  /usr/bin/sqlite3
		3. vim ~/.bashrc
		4. export LD_LIBRARY_PATH="/usr/local/lib"
		5. source ~/.bashrc
* django.core.exceptions.ImproperlyConfigured: Error loading MySQLdb module.
	* yum install mysql-devel -y
	* yum install -y libmariadbclient-dev
	* pip3 install  mysqlclient

