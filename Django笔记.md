---
title: Django笔记
tags: django,python,web开发
renderNumberedHeading: true
grammar_cjkRuby: true
---

-----

# MVT框架

* Model
* View
	* 响应http请求
	* 根据urls.py中url规则来匹配函数来响应http请求，url规则支持正则表达式
* Template

----------

# 项目管理

## 创建项目

	django-admin startproject {项目名}

## 创建应用

	python manage.py startapp {应用名}
	
## 启动项目
    python manage.py runserver

## 项目结构

![项目结构](./images/1593233853499.png)

----------

### 项目名

urls.py 负责处理http请求，根据正则表达式，匹配app.views.py中对应的响应函数
**settings.py**
* INSTALLED_APPS
	* 要想在django中运行app，必须将app名填写在INSTALLED_APPS列表中
* LANGUAGE_CODE
	* 设置网站的语言，默认英文en-us，简体中文为zh-Hans
* TIME_ZONE
	* 设置时区，中国区为Asia/Shanghai
* TEMPLATES
	* DIRS 模板文件夹路径，一般默认写好
	

----------

### app名

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

### manage.py

* 管理django项目
* 根据模型生成数据库的表和数据
	* python manage.py  makemigrations
	* python manage.py migrate
* 查看数据库中的数据
	* python manage.py shell
	* from app名.models import 模型名 


----------

# 后台管理

### 创建管理员
python manage.py createsuperuser


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
 

