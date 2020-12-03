---
title: django 工程目录结构
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- python
- django
categories:
- tool kits
---

## django 工程目录结构

**转载自：http://www.loonapp.com/blog/11/**

> 修复了错别字、语句不通顺和调整了排版格式

你已经配置好你的Heroku账户（译者注：Heroku是一个老牌的免费云空间），并且创建了第一个Heroku应用，让我们来讨论一个非常重要的话题（虽然经常被忽略）：Django工程结构管理。

<!--more-->

> 概述

多数Django工程非常混乱。不幸的是默认的Django工程布局并没有对此有任何帮助，它过于简单的工程管理导致在处理大的工程时带来了很多维护性问题。

本文将帮助让你的工程有个合理的布局：

- 遵循最佳实践
- 让你的工程尽可能地直观--你（作为开发者）可以立即认出代码每个部分的作用
- 让你工程仍然保持规范随着你的工程中的应用越来越多。
- 让你工程在不同环境下部署更加方便
- 让其他程序员喜欢你的代码



> 具体步骤

这部分我将和你一起开始一个新的项目。过程中，你需要将你的项目目录结构调整为下面描述的布局。

本文描述了高维护性、结构分明的Django项目布局的最佳实践。



#### 基础 - 默认的Django项目

在深入之前，让我们创建一个新的Django项目（工程）

```cpp
$django-admin.py startproject djanolicious
$cd djangolicious
$tree .
.
├── djangolicious
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```

(*1个目录，5个文件*)

在根目录djangolicious下，可以得到：

- 项目目录：djangolicious
- manage.py脚本：用于管理Django站点

在项目目录djangolicious里包含：

- settings.py: 包含项目的所有配置参数

- urls.py: URL根配置

- wsgi.py: 内置runserver命令的WSGI应用配置

- \__init__.py: 用来告诉python，当前目录是python模块


**管理项目需求说明**

首先我们在项目中新建一个文件：requirements.txt。每个Django项目都应该有一个顶级的requirements.txt文件来列出项目中所有使用到的python包。

Note：如果你对于requirements文件不太熟悉，你可以阅读[Heroku指引](https://devcenter.heroku.com/articles/python-pip) 来通过pip管理python的需求关系。

requirement.txt中类似如下内容：

```cpp
Django==1.6
psycopy2==2.4.5
South==0.7.3
gunicorn==0.14.1
nrerelic==1.2.0.246
django-cerlery==2.4.2
```



创建requirements文件是为了让其他开发者 拷贝你的项目代码后可以快速地根据此文件中内容安装好必须的python依赖包。这样他们可以方便地运行你的代码，而不必煞费苦心地猜测项目依赖包的版本。

现在你知道为什么我们需要这么做了，照做吧！



#### 第一步 - 模块化

  在项目目录中创建一个顶级的requirement.txt是必须遵循的要求，而且这样可以保证可以方便地管理项目依赖关系

  这意味着：很有可能，项目的开发环境依赖关系不同于你的生产环境，所有你需要将你的开发和生产环境的依赖都放到requirement.txt中，但是这会使得管理起来比较困难。

  所以，最好是区分好不同环境的依赖和需求

  我们的做法如下（在项目djangolicious根目录下):

```cpp
$ls
djangolicious manage.py
$touch requirements.txt
$mkdir requirements
$touch requirements/{common.txt,dev.txt,prod.txt,test.txt}
$tree.
.
|-- djangolicious
|   |-- __init__.py
|   |-- settings.py
|   |-- urls.py
|   |-- wsgi.py 
|-- manage.py
|-- requirements
|   |-- common.txt
|   |-- dev.txt
|   |-- prod.txt
|   |-- test.txt
|-- requirements.txt
```

(*2个目录，10个文件*)



  可以看出，我新建了一个顶级目录：requirements,包含一系列的需求说明文件，分别针对每个环境。

  如果你的应用只需要在开发环境下运行，那么只需要在一个dev.txt文件。如果你的应用需要开发、生产、测试、tom和rudy环境下运行--那么就分别为他们创建一个.txt文件

Note：common.txt内含各种环境下共享的需求说明。例如Django。所有环境下都需要Django，不管是开发环境还是生产环境，你都需要使用它。

  将各类需求文件分开的目的是，当作为程序员的我只需要在本地环境下运行项目，那么我只需要安装requirement/dev.txt中提到的软件包，而不需要安装其他的包（生产环境，staging，测试环境等等）

  但是为什么我这么关心哪些包是我必须安装的？为什么我不将他们全部安装？

- 安装依赖包需要耗费很长时间，对于大型项目来说可能会耗费大块的时间（30分钟以上）。

- 很多需求依赖于外部软件或者库文件按装到你的本地机器来完成编译。这么避免安装库文件可以节省时间还可以免去大量不必要的麻烦，比如要安装哪个版本的libxml2和libpq-dev。

- 降低了初学者学习的门槛。如果的你项目组来了一个新的开发，尝试提交代码，对他来说安装很少的软件包就可以运行系统要比安装所有软件包要简单的多。


#### 第二步 - 定义需求文件

  现在我们明白了为什么要模块化需求说明文件，下面了解下实践中需求说明文件的具体内容。

  下面我列出了4个从我实际项目中拿来的需求说明文件，我将详细的给予说明。

  首先，requirements/common.txt文件列出了所有基本的需求包，其中的么个软件包在任何环境都是必须的（不管是开发环境，测试环境还是生产环境等的）：

```cpp
# requirements/common.txt
Django==1.4
django-cache-machine==0.6
django-celery==2.5.5
django-dajaxice==0.2
django-guardian==1.0.4
django-kombu==0.9.4
django-pagination==1.0.7
django-sorting==0.1
django-tastypie==0.9.11
Fabric==1.4.1
lxml==2.3.4
pyst2==0.4
South==0.7.4
Sphinx==1.1.3
```

 

下面的requirements/dev.txt文件包含了我的开发环境所需要包，其中的包只有是在开发环境下才会用到。

```cpp
# requirements/dev.txt
-r common.txt
django-debug-toolbar==0.9.4
```



 在我的开发环境，我通常使用轻量级的SQLite3数据库（所以我不需要安装任何驱动程序），而且非常好用的包django-debug-toolbar可以允许我检查数据库查询和性能问题，等等。

  可能你会疑惑文件第一行的作用，'-r common.txt'告诉pip引入所有通用的依赖包附加到后面列举内容。

  这将允许我在命令行中直接运行pip intal -r requirements/dev.txt来安装开发环境需要的所有依赖包：

```cpp
$ pip install -r requirements/dev.txt
Downloading/unpacking Django==1.4 (from -r requirements/common.txt (line 1))
  Downloading Django-1.4.tar.gz (7.6Mb): 7.6Mb downloaded
  Running setup.py egg_info for package Django
 
Downloading/unpacking django-cache-machine==0.6 (from -r requirements/common.txt (line 2))
  Downloading django-cache-machine-0.6.tar.gz
  Running setup.py egg_info for package django-cache-machine
 
... snipped for brevity ...
```



从上面的运行结果可以看出，非常好用！当我们使用pip安装requirements/dev.txt中包，它不仅成功安装了开发环境中需要的依赖包，同时也将common.txt中列举的包都安装好了！非常漂亮！



下面是一个简单的requirements/pord.txt需求说明文件。其中包含了所有生产环境的依赖包和基本的依赖包：

```cpp
# requirements/prod.txt
-r common.txt
boto==2.1.1
cssmin==0.1.4
django-compressor==1.1.2
django-htmlmin==0.5.1
django-pylibmc-sasl==0.2.4
django-storages==1.1.3
gunicorn==0.14.1
newrelic==1.2.0.246
psycopg2==2.4.5
pylibmc==1.2.2
raven==1.3.5
slimit==0.6
```



最后，这是一个比较旧的requirements/test.txt文件，列出测试环境下的依赖包。这些包用于项目的单元测试环节。

```cpp
# requirements/test.txt
-r common.txt
django-coverage==1.2.2
django-nose==0.1.3
mock==0.8.0
nosexcover==1.0.7
```



- 当我需要在本地开发环境下运行我的代码，我就安装requirements/dev.txt中的依赖包
- 当我在生产环境下运行我的代码，就安装requirements/prod.txt中的依赖包
- 当我要针对我的代码做一些测试的时候，我就安装requirements/test.txt中的依赖包

重点在于将你的项目依赖文件按照以下原则来拆分：

- 简单的
- 高效的
- 直观的



#### 第三步 - Heroku最佳实践

  现在，我们模块化了我们的需求说明文件。我敢说你一定疑惑：为什么在项目根目录下还有个requirement.txt的文件？

  原因如下：

- 标准化要求存在requirements.txt文件
- Heroku在你部署项目的时候会自动读取你根目录下下的requirements.txt文件，并且将这些需求包安装起来。

  Heroku会安装你在requirements.txt中定义的所有包，你可以有多种选择：

- 让Heroku安装所有的依赖包：comon.txt,dev.txt,prod.txt等
- 让Heroku只安装他需要的包

我们使用Heroku来部署我们的站点，所有最好是让Heroku只安装必须的包。

因为Heroku需要做的事情少了，这会让我们部署后的项目更加快速。

打开根目录下的requirements.txt文件然后输入以下内容：

```
`#Install all of our production dependencies only. -r requirements/prod.txt`
```

这样Heroku就会安装我们的要求来安装需要的包。



**分离应用和库文件**

管理Django工程接下来的工作就是把你的应用从库中分离出来。

总所周知，每个Django工程包括一系列的应用。有些应用中包含模型和视图等等。还有些是辅助性的应用。

通常，这些辅助应用用于自定义templatetag，管理命令以及一些其他代码。请不要将这些放到别的应用里面。

幸运的没，有一种简单的方法来构建的Django项目：

- 开发人员可以很容易地找到你的django应用
- 开发人员可以很容易的找到你的django库文件
- 不要在你工程目录下包含大量的自定义应用，不要让你的目录结果混乱，而很难找到你想要的东西

我都会在每个Django工程的主目录下创建两个目录：apps和libs用来各自存放应用和库文件。

再看我们的例子工程：djangolicious，我的做法如下：

```cpp
$ mkdir djangolicious/apps
$ mkdir djangolicious/libs
$ touch djangolicious/apps/__init__.py
$ touch djangolicious/libs/__init__.py
$ tree .
.
├── djangolicious
│   ├── apps
│   │   └── __init__.py
│   ├── __init__.py
│   ├── libs
│   │   └── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
├── requirements
│   ├── common.txt
│   ├── dev.txt
│   ├── prod.txt
│   └── test.txt
└── requirements.txt
```

(*4个目录，12个文件*)

t

如上所示，我的djangolicious工程中包含了新的apps目录和libs目录。剩下的就是将Django应用和库移动到合适的位置。

在djangolicious的例子中，我创建了一些django应用和库，现在到时候将django应用移动到合适位置。

```cpp
$ cd djangolicious/apps
$ django-admin.py startapp blog
$ django-admin.py startapp reader
$ django-admin.py startapp news
$ cd ../libs
$ django-admin.py startapp management
$ django-admin.py startapp display
$ cd ../..
$ tree .
.
├── djangolicious
│   ├── apps
│   │   ├── blog
│   │   │   ├── __init__.py
│   │   │   ├── models.py
│   │   │   ├── tests.py
│   │   │   └── views.py
│   │   ├── __init__.py
│   │   ├── news
│   │   │   ├── __init__.py
│   │   │   ├── models.py
│   │   │   ├── tests.py
│   │   │   └── views.py
│   │   └── reader
│   │       ├── __init__.py
│   │       ├── models.py
│   │       ├── tests.py
│   │       └── views.py
│   ├── __init__.py
│   ├── libs
│   │   ├── display
│   │   │   ├── __init__.py
│   │   │   ├── models.py
│   │   │   ├── tests.py
│   │   │   └── views.py
│   │   ├── __init__.py
│   │   └── management
│   │       ├── __init__.py
│   │       ├── models.py
│   │       ├── tests.py
│   │       └── views.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
├── requirements
│   ├── common.txt
│   ├── dev.txt
│   ├── prod.txt
│   └── test.txt
└── requirements.txt
```



9个目录，32个文件

现在我们的工程已经有了实际的架构！已经将django应用和库文件清晰地分开了。这样不仅很容易找到想要的应用或者库，而且目录结构也是非常的清晰。

移动应用和库文件后，还需要更新你的引入路径。如果你之前的是这么写的：

```cpp
# blog/views.py
from djangolicious.news.models import Newspaper
from djangolicious.display.templatetags import top_stories
```



那么你需要改成：

```cpp
# blog/views.py
from djangolicious.apps.news.models import Newspaper
from djangolicious.libs.display.templatetags import top_stories
```



尽管import语句变长了，我发现这对于我这个开发者来找到我所引入的需要修改的app、librarie是很有帮助的。

你还需要更新你的settings文件来包含新的应用的路径：

```cpp
# settings.py
INSTALLED_APPS = (
    ...
    'djangolicious.apps.blog',
    'djangolicious.apps.news',
    'djangolicious.apps.reader',
    ...
)
```



#### 构建一个完美的Django settings模块

构建完美的Django settings模块被认为是Django开发的“必杀技”。每个开发者都有着自己的想法，也可能会为此争论。

**然而，很多人的做法真实非常错误的。**

在此，我来展示了一种正确的方法来构建完美的Django settings模块，不管你工程的大小，需求以及其他因素。

我们所创建的settings模块：

- 允许你方便地分离Django各种环境（开发，生产，测试等等）。
- 允许你保持所有配置信息都在版本控制下。
- 允许你通过环境变量将密码和其他证书从基本代码中分离出来。
- 让你可以方便地修改配置。

**第一步 - 模块化，模块化，模块化**

就像我们在前面的章节中处理需求说明文件那样，配置信息也需要模块化！

首先，让我们处理掉讨厌的默认settings.py，取而代之的是创建一个更好的目录结构：

```cpp
$ rm djangolicious/settings.py
$ mkdir djangolicious/settings
$ touch djangolicious/settings/{__init__.py,common.py,dev.py,prod.py,test.py}
$ tree .
.
├── djangolicious
│   ├── apps
│   │   ├── blog
│   │   │   ├── __init__.py
│   │   │   ├── models.py
│   │   │   ├── tests.py
│   │   │   └── views.py
│   │   ├── __init__.py
│   │   ├── news
│   │   │   ├── __init__.py
│   │   │   ├── models.py
│   │   │   ├── tests.py
│   │   │   └── views.py
│   │   └── reader
│   │       ├── __init__.py
│   │       ├── models.py
│   │       ├── tests.py
│   │       └── views.py
│   ├── __init__.py
│   ├── libs
│   │   ├── display
│   │   │   ├── __init__.py
│   │   │   ├── models.py
│   │   │   ├── tests.py
│   │   │   └── views.py
│   │   ├── __init__.py
│   │   └── management
│   │       ├── __init__.py
│   │       ├── models.py
│   │       ├── tests.py
│   │       └── views.py
│   ├── settings
│   │   ├── common.py
│   │   ├── dev.py
│   │   ├── __init__.py
│   │   ├── prod.py
│   │   └── test.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
├── requirements
│   ├── common.txt
│   ├── dev.txt
│   ├── prod.txt
│   └── test.txt
└── requirements.txt
```



和需求说明文件一样，settings模块也应该针对每个环境一个配置文件（dev.py,prod.py,test.py）,和一个被各种环境共享的文件(common.py)。



**资源**

严格来说，管理Django工程发很需要技术。

然而我也还没有很多好的书籍或者资料可以推荐给你。

要想更好地维护和管理工程，就需要你创建很多项目，并且持续地改进你的代码



转载自：http://www.loonapp.com/blog/11/



##### 其它目录布局

```python
manage.py
settings.py
test.py
requirements.py

所有的app都放到apps目录下，这样的话在settings里添加app时候只用写成apps就可以了
apps/
apps/accounts/
apps/bbs/
...

base目录
base/models.py
base/common.py
...

utils目录用来放自己写的一些单元
utils/http.py
utils/email.py
utils/middlewares.py
utils/context.py
...
```



https://www.cnblogs.com/Amagasaki/articles/3555509.html

https://github.com/pydanny/cookiecutter-django


