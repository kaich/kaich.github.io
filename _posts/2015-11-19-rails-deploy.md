---
layout: post
title: rails 部署
---
[版瓦工]: https://bandwagonhost.com/
[ruby-china]: https://github.com/ruby-china/ruby-china/wiki/Ubuntu-12.04-%E4%B8%8A%E4%BD%BF%E7%94%A8-Nginx-Passenger-%E9%83%A8%E7%BD%B2-Ruby-on-Rails
[passenger安装]: https://www.phusionpassenger.com/library/install/nginx/install/oss/trusty/
[rails实战圣经]: https://ihower.tw/rails4/deployment.htm
[How-To Deploy Rails Applications Using Capistrano 3.1]: http://corlewsolutions.com/articles/article-10-how-to-deploy-rails-applications-using-capistrano-3-1-and-windows-7

上一次买了个vps搭建博客其他常用工具。昨天为了部署自己写的rails的网站，于是在[版瓦工]()上面又买了一个vps.在部署的工程中可谓是各种坑啊。于是记录下，历经了三天的时候才粗略部署上去。首先在部署到服务器之前先在自己本地用production跑一下，不要盲目的直接上传，最后导致不知道是自己配置问题还是服务器配置问题。我就花了很多时间在这个坑上。

首先预编译assets

```sh
RAILS_ENV=production bin/rake assets:precompile
```

然后在本地以production环境运行server:

```sh
rails server --environment=production
```

在这个过程中遇到了asset无法访问的问题。解决方案参考[这里](http://stackoverflow.com/questions/18700219/rails-4-assets-not-loading-in-production)

后来又遇到bootstrap-sass 的 Glyphicons 不显示的问题。解决方案草考[这里](https://ruby-china.org/topics/28140),是版本问题。

###环境

* Ubuntu 12.04 LTS
* RVM
* Ruby 2.1.2
* Rails 4.0.3
* Passenger 4.0+
* Nginx（由 Passenger 编译）

###创建账号

假设你已经用 root 帐号通过 SSH 登陆服务器。

出于安全考虑，不要使用 root 帐号运行 web 应用。这里新建一个专门用于部署的用户，例如 deploy 或者其它你喜欢的名字。运行以下命令创建用户：

```sh
# useradd -m -s /bin/bash deploy
```

将用户加入 sudo 群组，以便使用 sudo 命令：

```sh
# adduser deploy sudo
```

为 deploy 用户设置密码：

```sh
# passwd deploy
```

退出当前 SSH 链接，用 deploy 帐号重新登陆。

###安装 RVM 和 Ruby

更新 apt，并安装 curl：

```sh
$ sudo apt-get update
$ sudo apt-get install curl
```

然后安装 RVM：

```sh
$ \curl -sSL https://get.rvm.io | bash
```

RVM 安装完毕后，重新登陆 SSH，运行：

```sh
rvm list known 
```

选择要安装的ruby版本，这里用ruby 2.2.3:

```sh
$ rvm use --install --default 2.2.3
$ rvm use 2.1.2@your_gemset --create --default
```
Ruby 安装过程会请求 apt-get update 的权限，并自动安装系统依赖。安装完毕后，确认目前的 Ruby 版本：

```sh
$ ruby -v
```

应该看到 ruby 2.2.3 字样。

###安裝MySQL
安装mysql:

```sh
sudo apt-get install mysql-common mysql-client libmysqlclient-dev mysql-server
```
安装mysql2 gem:

```sh
sudo gem install mysql2 --no-ri --no-rdoc
```
创建数据库，替换your_production_db_name为你数据库名称:

```sh
mysql -u root -p
CREATE DATABASE your_production_db_name CHARACTER SET utf8;
```

问题：	

1. mysql 安装开始用ubuntu 15 记过死活启动不了，于是重装 unbuntu 14成功启动
2. mysql2安装过程中始终安装不上。 
	解决方案： `apt-get install libgmp-dev` ，具体参考[方案](https://github.com/copiousfreetime/hitimes/issues/48)

###Nginx + Passenger
由于我的vps比较便宜配置低，在用的安装方式会提示说增加swap之类的。但是我的vps又不支持这个操作。于是用下面的安装方式：

[passenger安装][]
关于passenger的安装部署配置最好还是参照[官网](https://www.phusionpassenger.com/library/walkthroughs/deploy/ruby/ownserver/nginx/oss/trusty/deploy_app.html)，因为是官网留坑很少，外面的教程或多或少有些过时等其他方面的问题。

###部署
手动部署请参考[ruby-china][]的部署教程

####自动部署
自动部署使用的是[Capistrano](http://capistranorb.com/),它的安装和使用方法可以参考[rails实战圣经][]和[How-To Deploy Rails Applications Using Capistrano 3.1][]。可以参考[rails实战圣经]了解大概步骤，具体的还是参考那篇英文文档.

###问题：
1. bundle stderr: /usr/bin/env: bundle: No such file or directory Capistrano

		解决方法：项目中添加`gem 'capistrano-rvm'`.可以参考[这里](http://stackoverflow.com/questions/19716131/usr-bin-env-ruby-no-such-file-or-directory-using-capistrano-3-capistrano-rben)
		
2. An error occurred while installing json (1.8.3), and Bundler cannot continue.

		解决方法:`sudo apt-get install ruby-dev`
		
3. git stderr: /usr/bin/env: git: No such file or directory

		解决方法：`apt-get install git-core`
		
4. Cannot install NodeJs: /usr/bin/env: node: No such file or directory

		解决方法:`sudo ln -s "$(which nodejs)" /usr/bin/node`
		
5. 访问修改文件的时候出现permission deny问题

		解决方法:尝试在命令前面加sudo , 获取查看文件路径是否存在。
		
6. 服务器所有的asset都报404错误

		解决方法: 首先你再部署之前`RAILS_ENV=production bin/rake assets:precompile`去预编译asset。如果还是有这个问题升级你的rails和gems。[参考](https://github.com/capistrano/rails/issues/111)

由于rails-asset服务器不稳定问题。于是改投[bower-rails](https://github.com/rharriso/bower-rails/)。记住在服务器上装nodejs的相关环境。
例如：
```sh
sudo apt-get install npn 
sudo npm install bower -g
```

关于使用bower-rails有一个比较坑的问题，如果通过它安装的asset有css.我们不能像js一样直接require或者import.我们必须用stylesheet_link_tag这种方法去加载它。[bower-rails](https://github.com/rharriso/bower-rails/)原文如下：

> Some bower components (eg. Bootstrap) have relative urls in the CSS files for imports, images, etc. Rails prefers using helper methods for linking to assets within CSS. Relative paths can cause issues when assets are precompiled for production.

Remember that you should have bower installed either locally in your project or on a remote server.
