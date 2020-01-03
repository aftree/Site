# Django

Django 一，简介 Django是一个开放源代码的Web应用框架，由Python写成。采用了MTV的框架模式，即模型M，视图V和模版T。它最初是被开发来用于管理劳伦斯出版集团旗下的一些以新闻内容为主的网站的，即是CMS（内容管理系统）软件。并于2005年7月在BSD许可证下发布。这套框架是以比利时的吉普赛爵士吉他手Django Reinhardt来命名的。

1，框架介绍 Django 项目是一个Python定制框架，它源自一个在线新闻 Web 站点，于 2005 年以开源的形式被释放出来。Django 框架的核心组件有：

用于创建模型的对象关系映射 为最终用户设计的完美管理界面 一流的 URL 设计 设计者友好的模板语言 缓存系统。 Django\(发音：\[\`dʒæŋɡəʊ\]\) 是用python语言写的开源web开发框架\(open source web framework\)，它鼓励快速开发,并遵循MVC设计。Django遵守BSD版权，初次发布于2005年7月, 并于2008年9月发布了第一个正式版本1.0 。

Django 根据比利时的爵士音乐家[Django Reinhardt](https://baike.baidu.com/item/Django%20Reinhardt)命名，他是一个吉普赛人，主要以演奏吉它为主，还演奏过小提琴等。

由于Django在近年来的迅速发展，应用越来越广泛，被著名IT开发杂志SD Times评选为2013 SD Times 100，位列“API、库和框架”分类第6位，被认为是该领域的佼佼者

2，架构设计 Django是一个基于MVC构造的框架。但是在Django中，控制器接受用户输入的部分由框架自行处理，所以 Django 里更关注的是模型（Model）、模板\(Template\)和视图（Views），称为 MTV模式。它们各自的职责如下：

层次 职责 模型（Model），即数据存取层 处理与数据相关的所有事务： 如何存取、如何验证有效性、包含哪些行为以及数据之间的关系等。 模板\(Template\)，即表现层 处理与表现相关的决定： 如何在页面或其他类型文档中进行显示。 视图（View），即业务逻辑层 存取模型及调取恰当模板的相关逻辑。模型与模板的桥梁。 从以上表述可以看出Django 视图不处理用户输入，而仅仅决定要展现哪些数据给用户，而Django 模板 仅仅决定如何展现Django视图指定的数据。或者说, Django将MVC中的视图进一步分解为 Django视图 和 Django模板两个部分，分别决定 “展现哪些数据” 和 “如何展现”，使得Django的模板可以根据需要随时替换，而不仅仅限制于内置的模板。

至于MVC控制器部分，由Django框架的URLconf来实现。URLconf机制是使用正则表达式匹配URL，然后调用合适的Python函数。URLconf对于URL的规则没有任何限制，你完全可以设计成任意的URL风格，不管是传统的，RESTful的，或者是另类的。框架把控制层给封装了，无非与数据交互这层都是数据库表的读,写,删除,更新的操作。在写程序的时候，只要调用相应的方法就行了，感觉很方便。程序员把控制层东西交给Django自动完成了。 只需要编写非常少的代码完成很多的事情。所以，它比MVC框架考虑的问题要深一步，因为我们程序员大都在写控制层的程序。现在这个工作交给了框架，仅需写很少的调用代码，大大提高了工作效率。

3，设计哲学 Django的主要目的是简便、快速的开发数据库驱动的网站。它强调代码复用，多个组件可以很方便的以“插件”形式服务于整个框架，Django有许多功能强大的第三方插件，你甚至可以很方便的开发出自己的工具包。这使得Django具有很强的可扩展性。它还强调快速开发和DRY\(Do Not Repeat Yourself\)原则。

Django基于MVC的设计十分优美：

对象关系映射 \(ORM,object-relational mapping\)：以Python类形式定义你的数据模型，ORM将模型与关系数据库连接起来，你将得到一个非常容易使用的数据库API，同时你也可以在Django中使用原始的SQL语句。 URL 分派：使用正则表达式匹配URL，你可以设计任意的URL，没有框架的特定限定。像你喜欢的一样灵活。 模版系统：使用Django强大而可扩展的模板语言，可以分隔设计、内容和Python代码。并且具有可继承性。 表单处理：你可以方便的生成各种表单模型，实现表单的有效性检验。可以方便的从你定义的模型实例生成相应的表单。 Cache系统：可以挂在内存缓冲或其它的框架实现超级缓冲 －－ 实现你所需要的粒度。 会话\(session\)，用户登录与权限检查，快速开发用户会话功能。 国际化：内置国际化系统，方便开发出多种语言的网站。 自动化的管理界面：不需要你花大量的工作来创建人员管理和更新内容。Django自带一个ADMIN site,类似于内容管理系统 4，工作机制

1.用manage .py runserver 启动Django服务器时就载入了在同一目录下的settings .py。该文件包含了项目中的配置信息，如前面讲的URLConf等，其中最重要的配置就是ROOT\_URLCONF，它告诉Django哪个Python模块应该用作本站的URLConf，默认的是urls .py

2.当访问url的时候，Django会根据ROOT\_URLCONF的设置来装载URLConf。

3.然后按顺序逐个匹配URLConf里的URLpatterns。如果找到则会调用相关联的视图函数，并把HttpRequest对象作为第一个参数\(通常是request\)

4.最后该view函数负责返回一个HttpResponse对象

5，环境搭建 pip install django

6，第一个django项目 使用pycharm新建项目

项目目录

manage.py： 命令行工具，允许以多种方式与项目进行交互。在CMD窗口下，将路径切换到django项目下输入 python manage.py help可以查看工具的具体功能

init.py： 初始化文件，通常无需修改

settings.py： 项目配置文件

urls.py： 项目的URL设置，网站地址信息

wsgi.py： WebServerGatewayInterface服务器网关接口，Python应用与Web服务器间的接口

templates：模板文件目录

运行项目

复地址到浏览器

成功！！！

创建APP

项目创建完成后，创建项目应用，简称App，是网站的功能 每个App是网站的一页或者多页 App的创建由manage.py实现

● cd myDjango,切换目录

● 使用manage.py创建app index

● migrations： 用于数据库的迁移

● init.py： 初始化文件

● admin.py： 当前App的后台管理系统

● app.py： 当前App的配置信息，通常无需修改

● model.py： 定义在映射类关联数据库，MTV中的Model

● tests.py： 自动化测试后模块

● views.py： 逻辑处理模块，MTV中的Views

修改配置文件

urls.py和views.py代码：

重新启动项目

访问项目

二，Django配置信息 配置信息主要由项目中settings.py文件实现，主要配置有：项目路径、密钥配置、域名访问权限、App列表、配置静态资源、配置模板文件、数据库配置、中间件、缓存配置。

1，基本配置信息 一个简单的项目必备的基本配置信息有：项目路径、秘钥配置、域名访问权限、App列表和中间件。以MyDjango项目为例，settings.py的基本配置如下：

上述代码列出了项目路径BASE\_DIR、密钥配置SECRET\_KEY、调试模式DEBUG、访问权限ALLOWED\_HOSTS和App列表INSTALLED\_APPS，各个配置说明如下：

项目路径BASE\_DIR：主要通过os模块读取当前项目在系统的具体路径，代码在创建项目时自动生成，通常无需修改。

密钥配置SECURIT\_KEY：是一个随机值，在项目创建的时候自动生成，通常无需修改。主要用于重要数据的加密处理，提高系统安全性。主要用于用户密码，CSRF机制（表单提交），会话session等数据加密。

调试模式DEBUG：该值为布尔类型。开发阶段True，项目部署上线改为False。

域名访问权限ALLOWED\_HOSTS：设置可访问的域名，默认为空。DEBUG=True同时ALLOWD\_HOSTS为空代表项目只允许以localhost或者127.0.0.1在浏览器上访问。DEBUG=False时ALLOWD\_HOSTS为必填项，设置为ALLOWD\_HOSTS=\[‘xxx.xxx.xxx.xxx’\]

App列表INSTALLED\_APPS，告诉django有哪些App，项目中已有配置信息如下：

admin 内置的后台管理系统 auth 内置的用户认证系统 contenttypes 记录项目中的model元数据 sessions Session会话功能，用于标识访问网站的用户身份信息 messages 消息提示功能 staticfiles 查找静态资源路径 ​ 如果项目中加入了App，需要在INSTALLED\_APPS列表中加入App名字，如

2，模板路径 模板是一种较为特殊的HTML文档。这个文档中嵌入了一些Python识别的变量和指令，然后程序解析这些变量和命令，生成完整的HTML网页并返回给用哦过户浏览。 MTV框架中的T。创建项目时，Django已初始化模板配置信息：

● BACKEND: 模板引擎，用于识别模板中的变量和指令

● DIRS: 设置模板所在路径，告诉Django模板位置

● APP\_DIRS: 是否在App文件中查找模板文件

● OPTIONS: RequestContex中上下文的调用函数，通常不做修改

3，静态资源 静态资源指，网站中不变的文件。静态资源包括CSS文件、JavaScript文件以及图片等资源文件。

CSS（层叠样式表）一种用来表现HTML或XML文件样式的计算机语言。 JavaScript是一种直译式脚本语言在HTML网页上使用，用于给网页增加动态功能。 静态文件存放在配置文件settings.py文件上，如下：

上述配置将静态资源存放在文件夹static，这个文件夹只能放在App里面。

项目启动时，Django根据静态资源存放路径查找相关文件，查找功能由App列表INSTALLED\_APPS的staticfiles实现。在index App中添加‘python package’并放置文件。

启动项目后，浏览器访问：[http://127.0.0.1:8000/static/FileName](http://127.0.0.1:8000/static/FileName)

如果想在djangoDemo的根目录下存放静态资源，可以在settings.py中设置STATICFILES\_DIRS属性。该属性以列表形式表示，设置方式如下：

分别在根目录下创建文件夹public\_static，在App（index）下创建index\_static文件夹

4，数据库配置 选择项目所使用的数据库类型，不同数据库需要设置不同的数据库引擎，数据库引擎用于实现项目与数据库的连接，Django提供四种数据库引擎：

项目创建时，默认sqlite3数据库，这是一款轻型的数据库，常用于嵌入式系统开发，配置信息如下：

如果把上述连接信息修改为MySQL数据库，首先安装MySQL连接模块‘mysqlclient’ ,输入命令： 在线安装：pip install mysqlclient 离线安装：pip install --no-index --find-links=file:所在目录 mysqlclient-1.4.1-cp37-cp37m-win\_amd64.whl

安装后，在Python Console进行验证

完成mysqlclient模块晚装后，配置settings.py的MySQL连接信息，如下

Django除了支持PostgreSQL、Sqlite3、MySQL和Oracle以外，还支持SQLServer和MongoDB的连接

5，中间件 中间件是处理Django的request和response对象的过程。当用户在网站中进行某个按钮等操作时，这个动作是用户向网站发送request，而网站根据操作返回相关内容，这个过程叫response

一般情况下，Django默认的中间件配置均可满足大部分的开发需求。在MIDDLEWARE中添加LocalMiddleware中间件，使得Django内置功能支持中文显示，如下：

SecurityMiddleware 内置安全机制，保护用户与网站通信安全

SessionMiddleware 会话Session功能

CommonMiddleware 处理请求信息，规范化请求内容

CsrfViewMiddleware 开启CSRF防护功能

AuthenticationMiddleware 开启内置用户验证系统

MessageMiddleware 开启内置信息提示功能

XFrameOptionsMiddleware 防止恶意程序点击劫持

LocalMiddleware 支持中文语言

三，编写URL规则 URL（Uniform Resource Locator，统一资源定位符）是互联网上标准资源地址。用于指出文件的路径位置。Django中，URL也成为URLconf。

1，URL编写规则 每个App中设置独立的静态资源和模板文件夹并添加一个.py文件，命名为urls.py。

在App的urls.py中写入对应URL，项目根目录urls.py 来管理每个App中的urls.py文件。根目录下urls.py编写URL规则如下

项目目录下的urls.py

from django.contrib import admin from django.urls import path,include urlpatterns=\[ path\('admin/',admin.site.urls\), path\('',include\('index.urls'\) \] 1 2 3 4 5 6 7 8 导入Admin功能模块 导入URL编写模块 整个项目URL集合，每个元素代表一条URL信息 设定Admin的URL（127.0.0.1:8000/admin） URL为空，代表网站域名（127.0.0.1:8000）

首页地址分发给了index的urls.py处理，因此需要配置index的urls.py编写信息，代码如下：

index的urls.py编写规则与根目录urls.py大致相同，基本上所有的URL都是有固有的编写格式。 上述代码导入了同一目录下的views.py文件，该文件用于编写视图函数，处理URL请求信息并且返回网页给用户。代码如下：

index函数必须设置参数request，该参数代表当前用户的请求对象。包括：用户名，请求内容，请求方式等信息。启动djangoDemo项目后运行结果如下：

2，带变量的URL 日常开发过程中，有时一个URL可以代表多个不同的页面，如编写带有日期的URL，根据前面编写方式，按照一年计算，需要开发者编写365个不同的URL才能实现，这种做法显然不可取，因此Django在编写URL时，需要对URL设置变量，使URL具有多样性。

URL的变量类型有字符类型、整型、slug和uuid，最为常见的是字符型和整型。

● 字符类型：匹配任何非空字符串，不含斜杠。没有指定类型时，默认该类型

● 整型： 匹配0和正整数

● slug： 可以理解为注释、后缀或附属等概念

● uuid： 匹配一个uuid格式的对象 UUID 是 通用唯一识别码（Universally Unique Identifier）的缩写，是一种软件建构的标准

根据上述变量类型，在index的urls.py里添加带有字符类型、整型和slug的URL地址信息，代码如下：

在URL中使用变量符号“&lt;&gt;”可以为URL设置变量。括号里面以冒号划分为两部分，前面代表变量类型，后面代表变量名称，变量名称可自定义。上述URL中设置三个变量值，分别是、int:month、slug:day，变量说明如下：

● 变量名为year，数据格式为字符类型，与str:year含义一样

● int:month 变量名为month，数据格式为整型

● slug:day 变量名为day，数据类型为slug

然后在views.py中编写视图函数mydate的处理方法，代码如下：

视图函数mydate有四个函数参数，其中year、month和day来自URL的变量。URL的变量和试图函数要一一对应，如果试图函数的参数与URL变量对应不上会抛出异常。项目启动如下：

3，设置参数name 除了URL里面设置变量之外，Django还可以对URL进行重命名。在index的urls.py、views.py和模板myyear.html中添加如下代码：

urls.py：

views.py：

创建templates文件夹，并且在templates文件夹中添加myyear.html文件

上述代码分别从URL、视图函数、HTML模板来说明name具体作用：

用户访问URL，项目根据URL信息选择试图函数myyear处理，并且将URL命名myyear 视图函数myyear将模板myyeear.html作为响应内容弄个并且生成相应的网页返回给用户 在模板myyear.html中分别设置两个标签a,虽然两个标签a的href属性值的写法有所不同，但实质两者都指向名为myyeear的URL地址信息。 第二个标签a的href为

，这是Django的模板语法，模板语法以

表示。 其中，url ‘myyear’ 是将命名为myyear的URL地址信息作为href属性值 运行结果如下：

4，设置额外参数 除了参数name之外，还有一种参数类型是以字典的数据类型传递的，该参数没有具体命名，只要是字典形式即可，而且该参数只能在试图函数中读取和使用。

## 参数为字典的url

re\_path\('dict/\(?P\[0-9\]{4}\).htm',views.myyar\_dict,{'month':'05'},name='myyar\_dict'\)

## 参数为字典的url的视图函数

def myyar\_dict\(request,year,month\): return render\(request,'myyar\_dict.html',{'month':month}\) 1 2 3 4 5 myyar\_dict.html:

四，视图 视图（View）是Django的MTV框架结构的V部分，主要负责处理用户请求和生成相应的响应内容，然后在页面及其他类型文档中显示。也可以理解为视图是MVC框架里面的C部分（控制器），主要处理功能和业务上的逻辑。

1，构建网页内容 我们可以看到视图函数都是通过return方式返回数据内容的，然后生成相应的网页内容呈现在浏览器上。而视图函数的return具有多个响应类型如下：

响应类型代表HTTP状态码，其核心作用是Web Server服务器用来告诉客户端-当前Web服务器的响应状态。上述响应主要来自于模块django.http，该模块是实现响应功能的核心。实际开发中，使用该模块实现文件下载功能，在index的urls.py和views.py中分别添加以下代码：

urls.py代码

views.py代码

上述文件下载功能说明如下：

1.接收到用户请求后，视图函数download首先定义HttpResponse的响应类型文件（text/csv）类型，生成response对象

2.response对象上定义Content-Disposition（Content-Disposition 响应头，设置文件在浏览器打开还是下载），设置浏览器下载文件的名称。attachment设置文件的下载方式，filename为文件名。

3.使用CSV模块加载response对象，把数据四恶如response对象所设置的CSV文件，并将response对象返回到浏览器上，从而实现文件下载。

访问 [http://127.0.0.1:8000/download.html从而实现文件下载，运行如下：](http://127.0.0.1:8000/download.html从而实现文件下载，运行如下：)

django.http除了实现文件下载外，要使用该模块生成HTML网页，可以在响应内容中编写HTML源码，如HttpResponse\(’…’\)。但不适合实际开发。Django在django.http模块上进行了封装，从而有了render\(\)、render\_to\_response\(\)和redirect\(\)函数。

​ render\(\)和render\_to\_response\(\)实现功能一致。render\_to\_response\(\)在2.0版本之后开始弃用。render\(\)语法如下：

函数render\(\)的参数request和template\_name是必须参数，其余参数是可选参数。各个参数说明如下：

request： 浏览器向服务器发送请i去对象，包括用户信息、请求内容、请求方式等 template\_name：HTML模板文件名，用于生成HTML网页 context： 对HTML模板的变量赋值，以字典形式表示，默认是空字典 content\_type： 响应数据的数据格式 status： HTTP状态码，默认为200 using： 设置HTML模板转换生成HTML网页的模板引擎 ​ 在项目index APP中的templates放置 华为商城首页-重命名为index.html，static用于存放该HTML模板的静态资源。我们在urls.py和views.py中编写如下代码：

从试图函数的context={‘title’:‘首页’}可知，将index.html模板变量的title值设置成首页，返回状态码是500。启动项目后，运行结果如下：

除了render函数外，还有redirect\(\)函数。redirct\(\)函数用于实现请求重定向。重定向的链接以字符串形式表示，链接地址信息可以支持相对路径和绝对路径，代码如下：

urls.py

views.py from django.shortcuts import render,redirect from django.http import HttpResponse import csv 1 2 3 4

2，数据可视化 视图除了接收用户请求、返回内容之外，还可以与模型（Model）实现数据交互（操作数据库）。视图相当于一个处理中心，负责接收用户请求，然后根据请求信息读取并处理后台数据，最后生成HTML网页返回给用户。

​ 视图操作数据库实质是从models.py导入数据库映射对象，models.py的数据库对象是通过Django内置的ORM框架构建数据库映射的，从而生成数据库对象。我们在index的models.py中编写如下代码：

上述代码将Product类和数据表Product构成映射关系，代码只是搭建两者的关系，数据库中，没有生成响应数据表。在PyCharm的Terminal窗口或者打开安装应用中的Anaconda Prompt终端，使用：

python manage.py XXX指令通过Product类创建数据表Product类，

创建命令如下：

python manage.py makemigrations python manage.py migrate 1 2

## 根据model.py生成相关的.py文件，该文件用于创建数据表

## 创建数据表

指令执行完成，数据库中可以看到新创建的数据表，如下：

连接数据库：mysql -u root -p

查看表： show tables

当命令执行完成后，Django会默认创建多个数据表，其中index\_product对应index的models.py所定义的Product类，其余的数据表都是Django内置功能生成的，主要用于Admin站点、用户认证和Session会话等功能。在数据表index\_product中添加数据：

完成数据表的数据添加后，接着将数据表的数据展现在网页上。首先将模板文件index.html左侧导航栏的代码注释掉，如下：

添加新的代码

新添加的代码是Django的模块语法，主要将视图的变量传递给模板，通过模板引擎转换成HTML语言。上述代码使用的循环和判断语句对变量进行分析处理。最后在视图函数中编写代码，将数据表中的数据与模板连接起来，实现数据可视化，代码如下：

from .models import Product

上述代码中，视图函数index处理流程如下：

type\_list用于查询数据表字段type的数据并将数据去重，name\_list用于查询数据字段type和name的全部数据，两种独特的查询方式都是由Django内置的ORM框架提供的。 将查询所得数据以字典格式写入变量context中，变量context是render\(\)函数的参数值，起作用是将变量传递给HTML模板。 当HTML模板接收到变量type\_list和name\_list后，模板引擎解析模板语法并生成HTML文件，运行结果如下：

从上述例子可以看到，如果想将数据库的数据展现在网页上，需要视图、模型、模板共同实现，实现步骤如下：

定义数据模型，以类的方式定义数据表的字段。在数据库创建数据表时，数据表由模型定义的类生成。 在视图导入模型所定义的类，该类也称为数据表对象，Django为数据表对象提供独有的数据操作方法，可以实现数据库操作，从而获取数据表的数据。 视图获取数据后，将数据以字典、列表或对象的方式传递给HTML模板，并由模板引擎接受和解析，最后生成相应的HTML网页 3，获取请求信息 我们知道视图是由于接收并处理用户的请求信息，请求信息存放在视图函数的参数request中，为了进一步了解参数request的属性，在Pycharm中使用debug模式启动项目，并且在视图函数中设置断点，然后查看request对象的全部属性。

requst的8种常用属性：HttpRequest.scheme 请求的协议、 HttpRequest.body 请求体 、HttpRequest.path 请求路径 、HttpRequest.method 请求方法 、HttpRequest.encoding 请求的字符集 、HttpRequest.GET 请求的GET数据 、HttpRequest.POST 请求的POST数据 HttpRequest.META 请求的META属性

上述属性中GET、POST、和method是每个Web开发人员必须掌握的基本属性。其中GET和POST用于获取用户的请求参数，method用于获取用户的请求方法。

以视图函数login为例，写入如下代码：

urls.py

views.py

视图函数login分别使用GET、POST和method，说明如下：

首先使用method对用户请求方式进行判断，一般情况下，用户打开浏览器访问某个URL地址都是GET请求；而在网页上输入信息并点击某个按钮时，POST请求居多，如用户登录、注册等。 如果判断请求方式时POST（GET）则通过POST（GET）来获取用户请求参数。不同的请求方式需要使用不通过的属性来获取用户提交的请求参数。 ​ 启动项目，在浏览器上分别输入以下URL地址：

​ [http://127.0.0.1:8000/login.html](http://127.0.0.1:8000/login.html)

​ [http://127.0.0.1:8000/login.html?name=Tom](http://127.0.0.1:8000/login.html?name=Tom)

​ 运行结果如下：

4，通用视图 Web开发时一项无聊而单调的工作，特别时在视图编写功能方面更为显著。为了减少这类痛苦，Django植入了通用视图这一功能，该功能封装视图开发常用代码和模式，可以在无需编写大量代码情况下，快速完成数据视图的开发。

​ 通用视图是通过定义和声明类的形式实现的，根据用途划分三大类：TemplateView、ListView和DetailView。三者说明如下：

TemplateView直接返回HTML模板，但无法将数据库展示出来 ListView能将数据库的数据传递给HTML模板，通常获取某个表的所有数据 DetailView能将数据库的数据传递给HTML模板，通常获取数据表的单条数据 ​ 我们将视图函数改用ListView实现。沿用index.html模板文件，然后在urls.py中添加URL地址信息，代码如下：

​ urls.py

from django.urls import path, re\_path from . import views urlpatterns = \[ path\('index/', views.ProductList.as\_view\(\)\) \] 1 2 3 4 5 6 ​ 如果URL所置县过的处理程序是由通用视图执行的，那么编写URL时，URL所指向的处理程序应当是一个通用视图，并且该通用视图必须使用as\_view\(\)方法，因为通用视图实质上是一个类，使用as\_view\(\)方法相当于对类进行实例化并由类方法as\_view\(\)执行处理。最后views.py中编写视图ProductList代码：如下：

ProductList代码说明如下：

1.定义ProductList类，该类继承自ListView类，具有ListView的所有属性

2.context\_object\_name设置HTML模板的变量

3.template\_name设置HTML模板

4.queryset查询数控数据，查询结果赋值给context\_object\_name所设置的变量

5.重写函数get\_queryset，该函数功能与query\_set实现功能一致

6.重写函数get\_context\_data,该函数设置HTML模板的其他变量

通用视图的代码编写规则有一定的固定格式，根据这个固定格式可以快速开发数据视图，除此之外，通用视图还可以获取URL的参数和请求信息，使得通用视图更加灵活。

urls.py:

path\('index/.html',views.ProductList.as\_view\(\),{'name':'phone'}\) 1 通用视图ProductList类

def get\_queryset\(self\):

```text
#获取url变量的id
print(self.kwargs['id'])
#获取url参数name
print(self.kwargs['name'])
#获取请求方式
print(self.request.method)
type_list = Product.objects.values('type').distinct()
return type_list
```

1 2 3 4 5 6 7 8 9 五，模板 1，变量与标签 变量是模板中最基本的组成单位，模板变量是由视图函数生成的。变量以表示，variable是变量名，变量类型可以是Python支持的数据类型，使用方法如下：

## variable为字符串类型或整型，如variable='Python'

## 输出Python

## variable为字典或数据对象，通过点号\(.\)来访问其属性值

## 如variable={"name":"Lily","info":{"home":"Beijing","homeplace":"ShangHai}}

## 输出Lily

## 输出Beijing

1 2 3 4 5 6 7 8 9 10 以MyDjango为例，抽取index.html部分代码，代码如下：

 {{ title }} 

```text
<link rel="stylesheet" type="text/css" href="{% static "css/hw_index.css" %}" >
<link rel="icon" href="{% static "img/hw.ico" %}">
<script src="{% static "js/hw_index.js" %}"></script>
```

&lt;/head&gt;

```text
<li>
    <h3><a href="#">{{ type.type }}</a></h3>
    <p>
        {% for name in name_list %}
            {% if name.type == type.type %}
                <span>{{ name.name }}</span>
            {% endif %}
        {% endfor %}
    </p>
</li>
{% endfor %}
```

&lt;/ul&gt; 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 上述代码分别使用模板的变量和标签，说明如下：

1. 代表模板变量，从变量名title可以知道，变量的数据类型是字符串或整型

2.

是模板的内置标签，load标签用于导入静态资源信息

3.

是模板的内置把标签，static标签用于读取静态资源的文件内容

4.

是for遍历标签，将变量进行遍历输出

5.

是if判断标签，主要对变量进行判断处理

6. 代表变量type\_list的某个属性

在上述常用标签中，每个标签的使用方法都是各不相同的。我们通过简单的例子进一步了解标签的使用方法，代码如下：

## for标签，支持嵌套，myList可以是列表或某一个对象

## item可自定义命名，{% endfor %}代表循环去终止符，代表该区域代码由for循环输出

## if标签，支持嵌套，判断条件符必须与变量之间使用空格隔开，否则程序会抛出异常

## {% endif %}与{% endfor %}作用是相同的

## url标签

## 生成不带变量的URL地址

## 相关的路由地址：path\('search/.html',views.search,name='search'\)

## 字符串search是URL的参数name的值，1是URL的变量page的值

&lt;a href="

" target="\_blank"&gt;第一页&lt;/a&gt;

## total标签

## load标签，导入静态文件标签库staticfiles，staticfiles来自settings.py的INSTALLED\_APPS

## static标签，来自讲台文件标签库staticfiles

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 在for标签中，模板还提供一些特殊的变量来获取for标签的循环信息，说明如下：

● forloop.counter： 循环的当前索引值，从1开始计数；常用于生成一个表格或者列表的序号！

● forloop.counter0： 循环的当前索引值，从0开始计数；

● forloop.revcounter： 循环结束的次数（从1开始）

● forloop.revcounter0 循环结束的次数（从0开始）

● forloop.first： 判断当前是否循环的第一次，是的话，该变量的值为True。我们经常要为第一行加点特殊的对待，就用得上这个判断了，结合if。

● forloop.last： 如果这是最后一次循环，则为真

● forloop.parentloop： 对于嵌套循环，返回父循环所在的循环次数。某些场景下，这是个大杀器，能解决你很多头疼的问题。

上述变量来自于forloop对象，该独享的模板引擎解析for标签时所称城的。举例如下：

1 2 3 4 5 6 7 8 9 2，模板继承 模板继承是通过模板标签实现的，其作用是将多个HTML模板的共同代码集中在一个新的HTML模板中，然后各个模板可以通过调用新的HTML模板，从而生成新的HTML网页，减少模板间代码重复使用。代码如下：

&lt;!DOCTYPE html&gt;

{{ title }}

 &lt;a href="

" target="\_blank"&gt;首页&lt;/a&gt; Hello Django &lt;/body&gt; &lt;/html&gt; 1 2 3 4 5 6 7 8 9 10 11 上述代码是一个完整的HTML模板，一个完整的模板有和两大部分，其中部分在大多数情况下都是相同的，因此可以将部分写道同用模板中，将共用模板命名为base.html，代码如下：

&lt;!DOCTYPE html&gt;

{{ title }}

&lt;/body&gt; &lt;/html&gt; 1 2 3 4 5 6 7 8 9 10 在base.html的代码中可以看到，里的内容变为

，block标签相当于一个函数，body是对该函数的命名，开发者可自行命名。在一个模板中可以添加多个block标签，只要每个block标签的命名不相同即可。接着在模板index.html中调用共同模板base.html，代码如下：

&lt;a href="

" target="\_blank"&gt;首页&lt;/a&gt;

Hello Django

1 2 3 4 5 模板index.html调用统统模板base.html的实质是由模板继承实现的，调用步骤如下：

在模板index.html中使用

来继承base.html 由标签

结束block标签 3，自定义过滤器 过滤器主要是对变量内容进行处理，例如替换、逆序、转义等。通过过滤器处理变量可以将变量的数据格式和内容转化为我们想要的效果。使用方法如下：

 1 模板引擎解析带过滤器的变量时，首先通过filter处理变量variable，然后将处理后的变量显示在网页上。其中，variable代表模板变量，管道符号‘\|’代表变量使用过滤器，filter代表某个过滤器。变量可以支持多个过滤器同时使用，如下：

 1 在使用的过程中，某些过滤器还可以传入参数，但仅支持一个参数的传入。带参数过滤器的使用方法如下：

 1 Django为开发者提供内置过滤器

[https://www.cnblogs.com/waltsmith/p/8088099.html](https://www.cnblogs.com/waltsmith/p/8088099.html)

实际开发中，如果内置过滤器不符合开发需求，我们需要自定义过滤器来解决问题。首先在MyDjango中添加文件和文件夹，如下：

在MyDjango项目中添加user\_defined文件夹，该文件夹下继续添加文件夹templatetags。templatetags用于存放自定义过滤器的代码文件，该文件夹也可以存放在项目的App中，但是需要注意的是，文件夹的命名必须为templatetags，否则Django在运行的时候无法识别自定义过滤器。最后，在templatestags文件夹下创建myfilter.py文件，该文件的编写自定义过滤器的实现代码。

​ 完成过滤器目录搭建，接着是配置过滤器信息，在配置文件settings.py的INSTALLED\_APPS里面添加user\_defined。当项目启动时，Django会从INSTALLED\_APPS的配置中查找到过滤器，若过滤器设置在index的目录下，则只需在INSTALLED\_APPS配置index即可，如下：

完成上述两个环境配置后，编写自定义过滤器代码myfilter.py中添加以下代码：

from django import template

## 声明一个模板对象，也成为注册过滤器

register = template.Library\(\)

## 声明并定义过滤器

@register.filter def myreplace\(value, args\): oldValue = args.split\(':'\)\[0\] newValue = args.split\(':'\)\[1\] return value.replace\(oldValue, newValue\) 1 2 3 4 5 6 7 8 9 上述代码用于实现HTML模板的字符串替换功能，与Python的replace函数相同，说明如下：

导入模板功能template，通过template声明Library对象，给对象赋值给register，称为注册过滤器 过滤器以函数形式实现，在函数前使用register.filter装饰器表示该函数是一个过滤器 函数参数可设置一个或两个，上述参数分别为value和args，value是HTML模板的变量，参数args是过滤器函数定义的函数参数 过滤函数最后必须将处理结果返回，否则出现异常 最后HTML模板中使用我们自定义的过滤器，以index.html模板的title为例，代码如下：

&lt;!DOCTYPE html&gt;

 {{ title \| myreplace:'首页\|我的首页' }} 

```text
<link rel="stylesheet" type="text/css" href="{% static "css/hw_index.css" %}" >
<link rel="icon" href="{% static "img/hw.ico" %}">
<script src="{% static "js/hw_index.js" %}"></script>
```

&lt;/head&gt;

 1 2 3 4 5 6 7 8 9 10 11 12 在HTML模板中使用自定义过滤器可以分为两个步骤，说明如下：

用于导入templatetags文件夹的myfilter.py文件中所定义的功能，用来告诉Django在那个地方可以找到自定义过滤器。 把变量title含有‘首页’的内容替换成‘我的首页’。其中，myreplace是过滤器的函数名，‘首页：我的首页’是函数参数args的值，函数参数value的值为模板变量title的值。

六，模型与数据库 Django对各种数据库提供了很好的支持，包括：PostgreSQL、MySQL、SQLite和Oracle，并且为这些数据库提供统一的调用API，这些API统称为ORM框架。通过使用Django内置ORM框架可以实现数据库连接和读写操作。

1，构建模型 ORM框架是一种程序技术，用于实现面向对象编程语言中不同类型系统的数据之间的转换。从效果上说，其实是创建了一个可在编程语言中使用的"虚拟对象数据库"，通过对虚拟对象数据库操作从而实现对目标数据库的操作，虚拟对象数据库与目标数据库是相互对应的。在Django中，虚拟对象数据库也成为模型。通过模型实现对目标数据库的读写操作，实现方法如下：

配置目标数据库信息，主要在settings.py中设置数据库信息 构建虚拟对象数据库，在App的modle.py文件中以类的形式定义模型 通过模型在目标数据库中创建相应的数据库 在视图函数通过对模型操作实现目标数据库的读写操作

我们使用Navicat Prenium数据库管理工具查看mysql数据库的信息，如下：

数据表只能通过模型创建，因为Django对模型和目标数据库之间由自身的映射规则，如果自己在数据库中创建数据表，可能不一定符合Django的见表规则，从而导致模型和目标数据库无法建立通信。在项目index的models.py文件中定义模型，代码如下：

from django.db import models

## Create your models here.

## 创建产品分类表

class Type\(models.Model\): id = models.AutoField\(primary\_key=True\) type\_name = models.CharField\(max\_length=20\)

## 创建产品信息表

class Product\(models.Model\): id = models.AutoField\(primary\_key=True\) name = models.CharField\(max\_length=50\) weight = models.CharField\(max\_length=20\) size = models.CharField\(max\_length=20\) type = models.ForeignKey\(Type, on\_delete=models.CASCADE\) 1 2 3 4 5 6 7 8 9 10 11 12 13 上述代码分别定义了Type和Product，定义说明如下：

模板以 类的形式定义，继承Django的model.Model类。一个类代表目标数据库的一张数据表，类的命名一般首字母大写 模板的字段以类属性进行定义，如id = models.IntegerField\(primary\_key=True\) 代表在数据表Type中命名一个id字段，该字段数据类型为整型并且设置为主键 完成模型定义后，接着在目标数据库中创建相应数据表，在目标数据库中创建表是通过Django的管理工具manage.py完成，创建命令如下：

## 根据model.py生疮相关.py文件，该文件用于创建数据库

## 创建数据表

在目标数据库中创建数据表需要执行两次命令，分别是makemigrations和migrate指令。创建过程说明如下：

​ makemigrations指令用于将index所定义的模型生成0001\_initial.py文件，该文件放在index的migrations文件夹，打开0001\_initial.py文件，其文件内容如下：

0001\_initial.py文件将model.py的内容生成数据表脚本代码。而migrate指令更加脚本代码在目标数据库中生成相应数据表。指令完成后，可在数据库中查看到已创建的数据表，如下：

数据表index\_product和index\_type是由index的模型所创建的，分别对应模型Product和Type。其他数据表是Django内置功能所使用的数据表，分别是session、用户认证管理和Admin日志记录等。

我们创建了index\_prodect和index\_type，而表字段是在模型中定义的，在哦行Type和Product中定义的字段类型由整型和字符类型，但实际开发中，我们需要定义不同的数据类型满足各种要求，因此Django划分了多种不同的数据类型，如下：

Django提供的字段类型还会对数据进行正则处理和验证功能，进一步完善数据的严谨性。除了表字段类型外，每个表字段还可以设置相应的参数，让字段更加完善。字段说明如下：

2，数据表的关系 一个模型对应目标数据库的一个数据表，每个数据表之间也是存在关联的，表与表之间由三种关系:一对一、一对多、多对多。

​ 一对一存在两个数据表中，第一个表的某一行数据与第二个表的某一行数据相关，同时第二个表的某一行数据也至于第一个表的某一行数据相关，这种关系称为一对一关系。

ID 姓名 国籍 参加节目 1001 王大锤 中国 万万没想到 1002 李光洙 韩国 Running Man 1003 高晓松 未知 晓说 ID 出生日期 逝世日期 1001 1988 NULL 1002 1981 NULL 1003 未知 NULL 两个表中，ID分别一一对应，而且不会在同一个表中有重复的ID，使用这种关系通常是一个数据表中有太多字段，将常用的字段抽取出来并组成一个新的数据表。在模型中可以通过OneToOneField来构建表的一一对应关系，代码如下：

class Performer\(models.Model\): id = models.IntegerField\(primary\_key=True\) name = models.CharField\(max\_length=20\) nationality = models.CharField\(max\_length=20\) masterpiece = models.CharField\(max\_length=50\)

class Performer\_info\(models.Model\): id = models.IntegerField\(primary\_key=True\) performer = models.OneToOneField\(Performer,on\_delete=models.CASCADE\) birth = models.CharField\(max\_length=20\) elapse = models.CharField\(max\_length=20\) 1 2 3 4 5 6 7 8 9 10 11

使用Django管理工具manage.py创建数据表Performer和Performer\_info，创建数据表前最好删除0001\_initial.py文件并清空数据库中的数据表。如下：

一对多存在于两个或两个以上的数据表中，第一个表的数据可以与第二个表的一行到多行数据进行关联，但第二个表的每一行数据只能与第一个表的某一行进行关联，如下：

ID 姓名 国籍 1001 王大锤 中国 1002 李光洙 韩国 1003 高晓松 未知 ID 参加节目 1001 万万没想到 1002 Running Man 1003 晓说 1003 奇葩说 表1字段ID的数据可以重复并且在表2中找到对应数据，而表1字段ID是唯一的，这是一种最常见的表关系。在模型中可以通过ForeignKey来构建一对多关系，代码如下：

class Performer\(models.Model\): id = models.IntegerField\(primary\_key=True\) name = models.CharField\(max\_length=20\) nationality = models.CharField\(max\_length=20\)

class Program\(models.Model\): id = models.IntegerField\(primary\_key=True\) performer = models.ForeignKey\(Performer,on\_delete=models.CASCADE\) name = models.CharField\(max\_length=20\) 1 2 3 4 5 6 7 8 9 使用Django的管理工具manage.py创建数据表Performer和Program，创建数据表前最好先删除001\_initial.py文件并清空数据库里的数据表，生成的数据表如下：

多对多存在与两个或者两个以上数据表中，第一个表的某一行数据可以与第二个表的一道多行数据进行关联，同事第二个表的某一行数据也可以与第一个表的一到多行数据进行关联，如下：

ID 姓名 国籍 1001 王大锤 中国 1002 李光洙 韩国 1003 高晓松 未知 ID 参加节目 1001 万万没想到 1002 Running Man 1003 晓说 1004 英雄联盟 ID 节目ID 演员ID 1 1001 1001 2 1001 1002 3 1002 1001 从上面三个数据表中可以发现，一个演员可以参加多个节目，而一个节目也可以由多个演员共同演出。每个表的字段ID都是唯一的，节目ID和演员ID出现了重复数据，多对多关系需要使用新的数据表来管理两个表的关系。在模型中可以通过ManyToManyField来构建数据表的多对多关系，代码如下：

class Performer\(models.Model\): id = models.IntegerField\(primary\_key=True\) name = models.CharField\(max\_length=20\) nationality = models.CharField\(max\_length=20\)

class Program\(models.Model\): id = models.IntegerField\(primary\_key=True\) name = models.CharField\(max\_length=20\) performer = models.ManyToManyField\(Performer\) 1 2 3 4 5 6 7 8 9 Django创建多对多关系的时候只需要创建两个数据库对象，在创建数据表的时候会自动生成三个数据表来建立多对多关系，如下：

3，数据表的读写 前两节主要通过模型来构建目标数据库的数据表，本节主要通过模型操作来实现数据库的读写操作。数据库的读写操作主要是对数据库进行增、删、改、查。数据表index\_type和index\_product分别在两个数据表中添加如下内容数据。

手动在navicat客户端的mydjango数据库的index\_type数据表中加入以下数据：

为了更改的演示数据库的读写操作，在MyDjango项目中使用shell模式进行演示（该模式主要为方便开发人员和执行脚本）在PyCharm的Terminal下启动shell模式，输入‘python manage.py shell’指令即可开启

在shell模式下，如果相对数据表index\_product插入数据，输入如下代码：

In \[1\]: from index.models import In \[2\]: p = Product\(\) In \[3\]: p.name = '荣耀 V9' In \[4\]: p.weight = '111g' In \[5\]: p.size = '120_75_7mm' In \[6\]: p.type\_id = 1 In \[7\]: p.save\(\) 1 2 3 4 5 6 7 从models.py中导入Product 对模型Product声明并实例化，生成对象P 对对象p的属性进行赋值，对象p的属性来自模型Product所定义的字段 完成赋值，需要对p进行保存 代码执行结束后，可以查看到插入数据的情况：

除了上述方法外，数据的插入还有以下两种方式：

方法一：（通过Django的ORM框架提供的API实现，使用create方法实现数据插入） Product.objects.create\(name=‘荣耀V12’,weight=‘111g’,size=‘120757’,type\_id=1\)

方法二：（在实例化时直接设置属性值） p = Product\(name=‘荣耀V12’,weight=‘111g’,size=‘120757’,type\_id=1\)

如果想对现有数据进行更新，实现步骤与数据插入大致相同，唯一的区别在模型实例化后，要更新数据，需要先进行依次数据查询，将查询结果以对象形式赋给p，最后对p的属性进行数据的更新，代码如下：

In \[1\]: p = Product.objects.get\(id=5\) In \[2\]: p.name='华为荣耀V9‘ In \[3\]: p.save\(\) 1 2 3 七，表单与模型 表单是搜集用户数据信息的各种表单元素的集合。其作用是实现网页上的数据交互，数据在网站上输入数据信息，然后提交但网站服务器上处理。

​ 用户表单是Web开发的一项基本功能，Django表单功能是由Form类实现，主要分为两种：django.forms.Form、django.forms.ModelForm。前者是一个基础的表单功能，后者是在前者基础上结合模型所生成的数据表单。

1，初识表单 传统的表单生成方式是在模板文件中编写HTML代码实现的，在HTML语言中，表单由标签实现。表单生成方式如下：

&lt;!DOCTYPE html&gt;

## 表单

 First name:  
   
 Last name:  
   
  
  \# 表单 &lt;/body&gt; &lt;/html&gt; 1 2 3 4 5 6 7 8 9 10 11 12 13 14 一个完整的表单有四部分构成：提交地址、请求方式、元素空间和提交按钮。说明如下：

提交地址用于设置用户提交的表单数据应由哪个URL接收和处理，由控件的属性action决定。当用户向服务器提交数据时，如果属性action为空，提交的数据应有当前URL来接收和处理，否则网页会跳转到属性action所指向的URL地址。 请求方式用于设置表单提交方式，通常时GET请求或POST请求，由控件的属性method决定。 元素控件时供用户输入数据信息的输入框。由HTML的控件实现，其控件属性type用于设置输入框的类型，常用的输入框类型有文本框、下拉框和复选框。 提交按钮供用户提交数据到服务器，该按钮也是由HTML的控件实现的。但该按钮具有一定的特殊性，因此不归纳到元素控件的范围内。 在模板文件中，直接编写表单时一个较为简单的实现方式，如果表单元素较多，会在无形中增加模板的代码量，这样对日后维护和更新造成极大的不便。为了简化表单的实现过程和提高表单的灵活性，Django提供了完善的表单功能。我们对MyDjango目录做了细微调整，如下：

在MyDjango的index中添加了而空白文件form.py，该文件主要用于编写表单的实现功能，文件夹可自行命名；同时在文件夹templates中添加模板文件data\_form.html，该文件用于将表单数据像是在网页上。form.py、views.py和data\_form.html中分别添加如下代码：

## form.py代码，定义ProductForm表单对象

from django import forms from .models import \*

## 表单

class ProductForm\(forms.Form\): name = forms.CharField\(max\_length=20, label='名字',\) weight = forms.CharField\(max\_length=50, label='重量'\) size = forms.CharField\(max\_length=50, label='尺寸'\)

## 设置下拉框的值

choices\_list = \[\(i+1, v\['type\_name'\]\) for i,v in enumerate\(Type.objects.values\('type\_name'\)\)\] type = forms.ChoiceField\(choices=choices\_list, label='产品类型'\)

## views.py代码，将ProductForm实例化将其传递到模板中生成网页内容

from django.shortcuts import render from .form import \* def index\(request\): product = ProductForm\(\) return render\(request, 'data\_form.html',locals\(\)\)

## data\_form.html代码，将表单对象的内容显示在网页上

&lt;!DOCTYPE html&gt;

Title &lt;/head&gt;

&lt;/body&gt; &lt;/html&gt; 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 上述代码显示了Django内置表单功能的使用方法，主要由form.py、view.py和data\_form.html共同实现，实现说明如下：

在form.py中定义表单ProduceForm，表单以类的形式表示。在表单中定义不同类型类的属性，这些属性在表单中称为表单字段，每个字段代表HTML里的一个控件，这是表单的基本组成单位。 在views.py中导入form.py所定义的ProductForm类，在视图函数index中ProductForm实例化生成对象product，再将对象product传递给模板data\_form.html 模板data\_form.html将对象product以HTML的的形式展示在网页上

2，表单的定义 Django的表单功能主要是通过定义表单类，再由类的实例化生成HTML的表单元素控件，这样，可以在模板中减少HTML的编码，每个HTML的表单元素控件有表达那字段决定，代码如下:

## 表单类ProductForm的表单字段name

name = forms.CharField\(max\_length=20, label='名字',\)

## 表单字段name所生成的HTML元素控件

| 名字: |
| :--- |


|  |
| :--- |


字段name的参数label将转换成HTML的标签 字段name的form.CharField类型转换成HTML的控件，标签是一个输入框控件，type="text"代表当前输入框为文本输入框，参数type用于设置输入框的类型 字段name的命名转换成控件的参数name，表单字段的参数max\_length将转换成控件的参数required maxlength。 除了上述表单字段外，Django还提供了多种内置的表单字段

表单字段除了转换HTML控件外，还有一定的数据格式规范，数据格式规范主要由字段类型和字段参数实现，每个不同类型的表单字段都由一些自己特殊的参数，但每个表单字段都具有共同参数，如下：

required=True \#请求不能为空 widget=None \#HTML插件 label=None \#用于生成lable标签或显示内容 initial=None \#初始值 help\_text='' \#帮助信息（在标签旁边显示） error\_messages=None \#（错误信息{‘required’:'不能为空'，‘invalid’：‘格式错误’}） show\_hidden\_initial=False\#是否在当前插件后面加一个隐藏的并且有默认值的插件（可用于检验两次输入是否一致） validators=\(\) \#自定义验证规则 localize=False \#是否支持本地化 disabled=False \#是否可以编辑 label\_suffix=None \#label内容后缀 1 2 3 4 5 6 我们对form.py的表单ProductForm的字段进行优化，代码如下：

from django import forms from .models import \* from django.core.exceptions import ValidationError \# 自定义数据验证函数 def weight\_validate\(value\): if not str\(value\).isdigit\(\): raise ValidationError\('请输入正确的重量'\) \# 表单 】 class ProductForm\(forms.Form\): \# 设置错误信息并设置样式 name = forms.CharField\(max\_length=20, label='名字', widget=forms.widgets.TextInput\(attrs={'class': 'c1'}\), error\_messages={'required': '名字不能为空'},\) \# 使用自定义数据验证函数 weight = forms.CharField\(max\_length=50, label='重量', validators=\[weight\_validate\]\) size = forms.CharField\(max\_length=50, label='尺寸'\) \# 获取数据库数据 choices\_list = \[\(i+1, v\['type\_name'\]\) for i,v in enumerate\(Type.objects.values\('type\_name'\)\)\] \# 设置CSS样式 type = forms.ChoiceField\(widget=forms.widgets.Select\(attrs={'class': 'type','size':'4'}\), choices=choices\_list,label='产品类型'\) 1 2 3 4 5 6 7 8 9 10 优化的代码分别使用了widget、label、error\_messages和validators，这四个参数是实际开发过程中常用的参数，参数说明如下：

参数widget是一个form.widgets对象，其作用是设置表单字段的CSS央视。widget的对象类型应与表单字段类型相符合，如果字段类型为CharField和widget的对象类型为form.widgets.TextInput，二者的含义与作用是一致的，都代表文本输入框；如果字段类型为ChoiceField和widget的对象类型为form.widgets.TextInput相结合，前者是下拉选择框，后者是文本输入框，那么网页就会优先显示为文本输入框。 参数label会转换成HTML的标签，作用是对控件的描述和命名，使用户理解控件的作用和含义。 参数error\_messages用于设置数据验证是被后的错误信息，参数值以字典形式表示，字典的健为表单字段的参数名，字典的值为错误信息。 参数validators用于自定义数据验证函数，当前用户提交表单数据后，首先执行自定义的验证函数，当数据验证失败后，会抛出设置参数error\_messages，因为数据验证已由参数validators优先处理。 为了进一步验证优化后的表单是否正常运行，我们对views.py的视图函数index代码进行优化，如下：

from django.shortcuts import render from django.http import HttpResponse from .form import \* def index\(request\):

## GET请求 if request.method == 'GET':

product = ProductForm\(\) return render\(request, 'data\_form.html',locals\(\)\) \# POST请求 else: product = ProductForm\(request.POST\) if product.is\_valid\(\): \# 获取网页控件name的数据 \# 方法一 name = product\['name'\] \# 方法二 \# cleaned\_data将控件name的数据进行清洗，转换成Python数据类型 cname = product.cleaned\_data\['name'\] return HttpResponse\('提交成功'\) else: \# 将错误信息输出，error\_msg是将错误信息以json格式输出 error\_msg = product.errors.as\_json\(\) print\(error\_msg\) return render\(request, 'data\_form.html', locals\(\)\) 1 2 3 4 5 6 7 上述代码对views.py的视图函数index进行优化，说明如下：

判断用户请求方式，不同的请求方式执行不同的程序处理，函数index分别对GET和POST请求做出不同响应处理。 用户在浏览器中访问：[http://127.0.0.1:8000/，等同于向MyDjango发送一个GET请求，函数index将表单ProductionForm实例化并传输给模板，由模板引擎生成HTML表单返回给用户。](http://127.0.0.1:8000/，等同于向MyDjango发送一个GET请求，函数index将表单ProductionForm实例化并传输给模板，由模板引擎生成HTML表单返回给用户。) 当用户在网页上输入相关信息后单机‘提交’按钮，等同于向MyDjango发送一个POST请求，函数index首先获取表单数据对象product，然后由is\_valid\(\)函数对数据对象product进行数据验证。 如果验证成功，可以使用 product\[‘name’\] 或product.cleaned\_data\[‘name’\]方法来获取用户在某个控件上的键入值。只要将获取到输入值和模型相互使用，就可以实现表单与模型的信息交互。 如果验证失败，使用errors.as\_json\(\)方法获取验证失败的信息，然后待验证失败的信息通过模板返回给用户。 从上述例子可以发信啊，模板data\_form,html的表单是使用HTML的

标签展现在网页上，除此以外，表单还可以使用其他HTML标签展现，秩序将模板data\_form.html的对象product使用以下方法即可生成其他HTML标签： 将表单生成HTML的ul标签  将表单生成HTML的P标签  生成单个的HTML控件  获取表单字段参数lable属性值  3，模型与表单 Django的表单关系有两种：django.form.Form和django.form.ModelForm。前者是一个基础表单功能，后者是在前者基础上结合模型所生成的数据表单。数据表单是将模型的字段转成表单的字段，再从表单字段生成HTML的元素空间，这是日常开发中常用的表单之一。该部分通过讲解表单功能模块ModelForm实现表单数据与模型数据之间的交互开发。

首先在form.py中定义表单ProductModelForm,该表单继承自父类form.ModelForm 代码如下：

from django import forms from .models import \* from django.core.exceptions import ValidationError

## 数据库表单

class ProductModelForm\(forms.ModelForm\):

## 重写ProductModelForm类的初始函数**init**

def **init**\(self, _args, \*\*kwargs\): super\(ProductModelForm, self\).**init**\(_args, \*\*kwargs\)

```text
# 设置下拉框的数据 
type_obj = Type.objects.values('type_name') 
choices_list = [(i + 1, v['type_name']) for i, v in enumerate(type_obj)] 
self.fields['type'].choices = choices_list 
# 初始化字段name 
self.fields['name'].initial = '我的手机' 
# 添加模型外的表单字段 
productId = forms.CharField(max_length=20, label='产品序号', initial='NO1') 
# 模型与表单设置 
class Meta: 
# 绑定模型 
model = Product 
# fields属性是设置转换字段，'__all__'是将全部模型字段转换成表单字段 
# fields = '__all__' 
fields = ['name','weight','size','type']
# exclude是禁止模型字段转换表单字段 
exclude = [] 
# labels设置HTML元素控件的label标签 
labels = { 'name': '产品名称', 'weight': '重量', 'size': '尺寸', 'type': '产品类型' } 
# 定义widgets，设置表单字段的CSS样式 
widgets = { 'name': forms.widgets.TextInput(attrs={'class': 'c1'}), } 
# 定义字段的类型,一般情况下模型的字段会自动转换成表单字段 
field_classes = { 'name': forms.CharField } 
# 帮助提示信息 
help_texts = { 'name': '' } 
# 自定义错误信息 error_messages = { 
# __all__设置全部错误信息 '
__all__': {'required': '请输入内容', 'invalid': '请检查输入内容'}, 
# 设置某个字段错误信息 
'weight': {'required': '请输入重量数值', 'invalid': '请检查数值是否正确'} } 
# 自定义表单字段weight的数据清洗 
def clean_weight(self): 
# 获取字段weight的值 
data = self.cleaned_data['weight'] return data+'g'
```

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 上述代码中，表单类ProductModelForm可分为三大部分：添加模型外的表单字段、模型与表单设置和自定义表单字段weight的数据清洗函数，说明如下：

添加模型外的表单字段是在模型已有的字段下添加额外的表单字段。 模型与表单设置是将模型的字段转换成表单字段，由类Meta的数值型实现两者的字段转换 自定义表单字段weight的数据清洗函数只适合于字段weight的数据清洗。 模型字段转换成表单字段主要在类Meta中实现，在类Meta中，其属性说明如下：

Model ：必须属性，用于绑定Model对象

Fields ：必须属性，设置模型内哪些字段转换成表单字段。属性为‘all’代表整个模型的字段，若设置一个或多个，使用列表或元组的数据格式表示

Exclude ：可选属性，于fields相反，禁止模型内部哪些字段转换成表单字段，属性值以列表或元组表示。这个属性设置了，那么fields属性可以不设置

Labels ：可选属性，设置表单字段里的参数label。属性值以字典形式表示，键值是模型的字段

Widgets ：可选属性，设置表单字段里的参数widget

filed\_classes ：可选属性，将模型的字段类型重新定义为表单字符类型，默认情况下，模型字段类型会自动转换为表单字段类型

help\_texts ：可选属性，设置表单字段的参数help\_text

error\_messages：可选属性，设置表单字段里的参数error\_messages

4，数据表单的应用 通过定义表单类ProductModelForm将模型Product与表单相互结合起来，本节通过表单类ProductModeForm在网页上生成HTML表单。沿用前面模板data\_form.html，在MyDjango的urls.py和views.py中分别定义新的URL地址和视图函数，代码如下：

## urls.py的URL地址信息

from django.urls import path from . import views urlpatterns = \[

## 首页的URL

path\('', views.index\),

## 数据库表单

path\('.html', views.model\_index\), \]

## views.py的视图函数model\_index

from django.shortcuts import render from django.http import HttpResponse from .form import \* def model\_index\(request, id\): if request.method == 'GET': instance = Product.objects.filter\(id=id\)

```text
    # 判断数据是否存在 
    if instance: 
        product = ProductModelForm(instance=instance[0]) 
    else: 
    product = ProductModelForm() 
return render(request, 'data_form.html', locals()) else:
product = ProductModelForm(request.POST) 
if product.is_valid(): 
# 获取weight的数据，并通过clean_weight进行清洗，转换成Python数据类型           weight = product.cleaned_data['weight'] 
# 直接保存到数据库 # 
product.save() 
# save方法设置
commit=False，
生成数据库对象product_db，然后可对该对象的属性值修改并保存 
product_db = product.save(commit=False) 
product_db.name = '我的iPhone' 
product_db.save() 
# save_m2m()方法是保存ManyToMany的数据模型 
#product.save_m2m() 
return HttpResponse('提交成功!weight清洗后的数据为：'+weight) else:
# 将错误信息输出，error_msg是将错误信息以json格式输出 
error_msg = product.errors.as_json() 
print(error_msg) 
return render(request, 'data_form.html', locals())
```

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 函数model\_index的处理逻辑和函数index大致相同，说明如下：

判断用户请求方式，不同的请求方式执行不同的处理程序。代码分别对GET和POST请求做了不同的响应处理。 如果当前处理为GET请求，函数根据URL传递的变量id来查找模型Product的数据，如果数据存在，模型的数据以参数形式传递给表单ProductModelForm的参数instance，在生成表单时，模型书会填充到对应的元素控件上。 如果当前请求为POST请求，函数首先对表单数据进行验证，若验证失败，则返回失败信息；若验证成功，则使用cleaned\_data方法对字段weight进行清洗。字段weight清洗由自定义的函数cleaned\_weight完成，最后将表单数据保存在数据库。

八，Admin后台系统 Admin后台系统也成为网站后台管理系统，主要用于对网站前台的信息进行管理，如文字、图片、影音和其他日常使用文件的发布、更新、删除等操作，也包括功能信息的统计和管理，如用户信息、订单信息和方可信息等。简单说来，就是对网站数据库和文件的快速操作和管理系统。

1，Admin介绍 当一个网站上线之后，网站管理员时通过网站后台系统对网站进行管理和维护的。Django内置Admin后台系统，在创建Django项目时，可以从配置文件的settings.py中看到默认启动的Admin后台系统，如下：

在INSTALLED\_APPS中设置了Django的Admin后台系统，如果网站不需要Admin后台系统，可以将配置信息删除，这样可以减少程序对系统资源的占用。此外，在根目录的urls.py中可以看到Admin的URL地址信息，我们在浏览器中输入：[http://127.0.0.1:8000/admin](http://127.0.0.1:8000/admin) 就能访问Admin后台系统，如下：

在访问Admin后台系统时，首先需要输入用户的账号和密码才能登录管理界面。创建用户的账号和密码前，必须确保项目的模型在数据库中有相应的数据表，以MyDjango项目为例，项目数据表如下：

如果Admin后台系统以英文形式显示，那么我们还需要在项目settings.py中设置MIDDLEWARE中间件，将后台内容以中文形式显示，添加中间件有先后顺序，如下：

完成上述设置后，下一步时创建用户的账号和密码，创建方法由Django的管理工具manage.py完成，在Pycharm的Terminal下创建命令，代码如下：

创建用户时，用户名和邮箱地址可以为空，如果用户名为空会默认使用计算机用户名，而设置用户密码时，输入的密码不会显示在计算机的屏幕上。完成用户创建后，打开数据库auth\_user可以看到新增一条用户信息，如下：

Admin登录页面上使用刚创建的账号和密码登录，即可进入Admin后台系统，如下：

在Admin后台系统中可以看到，主要功能分为站点管理、认证和授权、用户和组，说明如下：

站点管理时整个网站的App管理界面，主要管理Django的App下所定义的模型。 认证和授权时Django妹纸的认证系统，也是项目中的一个App。 用户和组时认证和授权所定义的模型，分别对应auth\_user和auth\_user\_groups。 在MyDjango中，一再index中定义了模型Product和Type，分别对应数据表index\_product和index\_type。若想将index的定义模型战士在Admin后台系统中，则需要在index的admin.py中添加如下代码：

from django.contrib import admin from .models import \*

## 方法一

## 将模型直接注册到admin后台

admin.site.register\(Product\)

## 方法二

## 自定义ProductAdmin类并继承ModelAdmin

## 注册方法一，使用Python装饰器将ProductAdmin和模型Product绑定并注册到后台 @admin.register\(Product\)

class ProductAdmin\(admin.ModelAdmin\):

## 设置显示的字段

list\_display = \['id', 'name', 'weight', 'size', 'type',\]

## 注册方法二

admin.site.register\(Product, ProductAdmin\) 1 2 3 4 5 6 7 8 9 10 11 12 13 上述代码以两种方法对数据表index\_product注册到Admin后台系统，方法一是最基本的注册方式；方法二是通过类继承方式注册。日常开发都是采用第二种方式实现的，过程如下：

自定义ProductAdmin类，使其继承ModelAdmin。ModelAdmin主要设置模型信息如何展现在Admin后台系统中。 将ProductAdmin类注册到Admin后台系统中有两种方法，两者都是将模型Product和ProductAdmin类绑定并注册到Admin后台系统。 2，Admin的基本设置 我们将数据表index\_product成功展现在站点管理的页面，但对一个不会网站开发的使用者来说，可能无法理解INDEX和Products的含义，而且用英文表示也会影响整个网页的可读性。因此，我们需要将INDEX和Products转换成具体的中文内容。将INDEX和Products设置中文显示需要使用不同的方法实现，因此为INDEX和Products在项目中分别代表不同的意思，前者是一个APP的命名，后者是一个APP中的定义模型。

首先实现INDEX的中文显示，主要由APP的**init**.py文件实现，代码如下：

from django.apps import AppConfig import os

## 修改app在admin后台显示名称

## default\_app\_config的值来自apps.py的类名

default\_app\_config = 'index.IndexConfig'

## 获取当前app的命名

def get\_current\_app\_name\(\_file\): return os.path.split\(os.path.dirname\(\_file\)\)\[-1\]

## 重写类IndexConfig

class IndexConfig\(AppConfig\): name = get_currentappname\(\_file\) verbose\_name = '网站首页' 1 2 3 4 5 6 7 8 9 10 11 12 当项目启动时，程序会从初始化文件\_\_init_获取重写IndexConfig类，类属性verbose\_name用于设置INDEX的中文内容。

​ 然后将Proudcts设置中文显示，在models.py中设置类Meta的类属性verbose\_name\_plural即可实现。值得注意的是，Meta的类属性还有verbose\_name，二者都饿能设置Products的中文内容，但verbose\_name是以复数形式表示的，如将Products设置为“产品信息”，verbose\_name会希纳是为“产品信息s”，代码实现如下：

## Products设置中文，代码编写在models.py文件中

## 创建产品信息表

## 设置字段中文名，用于admin后台显示

class Product\(models.Model\): id = models.AutoField\('序号', primary\_key=True\) name = models.CharField\('名称',max\_length=50\) weight = models.CharField\('重量',max\_length=20\) size = models.CharField\('尺寸',max\_length=20\) type = models.ForeignKey\(Type, on\_delete=models.CASCADE,verbose\_name='产品类型'\)

## 设置返回值

def **str**\(self\): return self.name class Meta:

## 设置verbose\_name，在admin会显示为“产品信息s”

verbose\_name = '产品信息' verbose\_name\_plural = '产品信息' 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 除此之外，我们还可以进一步完善Admin网页标题信息，在App的admin.py下编写如下代码：

## 修改title和header

admin.site.site\_title = 'MyDjango后台管理' admin.site.site\_header = 'MyDjango'

## \# 自定义ProductAdmin类并继承ModelAdmin

@admin.register\(Product\) class ProductAdmin\(admin.ModelAdmin\):

## 设置显示的字段

list_display = \['id', 'name', 'weight', 'size', 'type',\] 1 2 3 4 5 6 7 8 上述例子实现了INDEX、Products的中文设置和Admin网页标题内容的修改，分别由index的初始化文件\_init_、模型文件models.py和admin.py实现，启动项目，如下：

当点击产品信息时，网页会进入模型Product的数据页面，数据以表格形式展示。从表格上可以发现，表头信息代表模型字段，而且表头以中文形式展示。如下：

产品类型数据饰演一个模型Type对象，在Type中定义**str**函数，设置模型的返回值，如下：

## models.py

## 创建产品分类表

## 设置字段中文名，用于admin后台显示

class Type\(models.Model\): id = models.AutoField\('序号', primary\_key=True\) type\_name = models.CharField\('产品类型', max\_length=20\)

## 设置返回值

def **str**\(self\): return self.type\_name 1 2 3 4 5 6 7 8 9 浏览器上刷新当前网页，可以发现蟾皮那类型数据变为Type\_name的数据，如下：

精确查找产品列表需要对admin.py中ProductAdmin进一步优化，如下：

from django.contrib import admin from .models import \* from django.contrib import messages @admin.register\(Product\) class ProductAdmin\(admin.ModelAdmin\):

## 设置显示的字段

list\_display = \['id', 'name', 'weight', 'size', 'type',\]

## 设置搜索字段，如有外键应使用双下划线连接两个模型的字段

search\_fields = \['id', 'name','type\_\_type\_name'\]

## 设置过滤器，如有外键应使用双下划线连接两个模型的字段

list\_filter = \['name','type\_\_type\_name'\]

## 设置排序方式，\['id'\]为升序，降序为\['-id'\]

ordering = \['id'\]

## 设置时间选择器，如字段中有时间格式才可以使用

date\_hierarchy = Field

## 在添加新数据时，设置可添加数据的字段

fields = \['name', 'weight', 'size', 'type'\]

## 设置可读字段,在修改或新增数据时使其无法设置

readonly\_fields = \['name'\] 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 上述代码中，ProductAdmin类分别设置list\_display、search\_fields、list\_filter、ordering、date\_hierarchy、fields和readonly\_fields属性，每个属性的作用在代码上已经做出注释说明。除了date\_hierarchy外，其他属性还可以使用元素格式表示。在ProductAdmin类新增属性都能在页面中生成相应的功能。

如果readonly\_fields和fields属性设置了模型的同意字段，那么新增数据的时候，该模型字段是无法输入数据的。

3，Admin的二次开发 前面讲述了Admin的基本设置，但是每个网站的功能和需求是各不相同的，这里需要通过ModeAdmin的方法实现对Admin的二次开发，以满足多方面的需求。

​ 函数get\_readonly\_fields和属性readonly\_fields的功能相似，不过前者比后者功能更强大。比如使用get\_readonly\_fields实现不同的用户角色来决定字段的可读性，实现代码如下：

## 重写get\_readonly\_fields函数，设置超级用户和普通用户的权限

def get\_readonly\_fields\(self, request, obj=None\): if request.user.is\_superuser: self.readonly\_fields = \[\] else: self.readonly\_fields = \['name'\] return self.readonly\_fields 1 2 3 4 5 6 7 函数get\_readonly\_fields首先判断当前发送请求的用户是否是超级用户，如果符合判断条件，将重新设置readonly\_fields属性，使当前用户具有全部字段的编辑权限。其中函数参数request使当前用户的请求对象，参数obj使模型对象，默认值为None。

在浏览器上通过切换不同的用户登录，可以发现在西南增或者修改数据的时候，不同用户对字段name的操作权限有所不同。

设置字段格式

在Admin预览模型Product数据信息时，数据表的表头是由属性list\_display所定义的，每个表有的数据都来自于数据库，并且，数据以固定的字体格式显示在网页上。若要某些字段的数据进行特殊处理，如字体颜色，以模型Proudct的type字段为例，实现代码如下：

## modelsk.py模型Product

from django.utils.html import format\_html class Product\(models.Model\): id = models.AutoField\('序号', primary\_key=True\) name = models.CharField\('名称',max\_length=50\) weight = models.CharField\('重量',max\_length=20\) size = models.CharField\('尺寸',max\_length=20\) type = models.ForeignKey\(Type, on\_delete=models.CASCADE,verbose\_name='产品类型'\)

## 设置返回值

def **str**\(self\): return self.name class Meta:

## 设置verbose\_name，在admin会显示为“产品信息s”

verbose\_name = '产品信息' verbose\_name\_plural = '产品信息'

## 自定义函数，设置字体颜色

def colored\_type\(self\): if '手机' in self.type.type\_name: color\_code = 'red' elif '平板电脑' in self.type.type\_name: color\_code = 'blue' elif '智能穿戴' in self.type.type\_name: color\_code = 'green' else: color\_code = 'yellow' return format\_html\( '{}', color\_code, self.type, \)

## 设置admin的标题

colored\_type.short\_description = '带颜色的产品类型' 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 从上述代码可以看到，设置字段主要是由models.py和admin.py实现，说明如下：

在models.py的模型Product中定义函数colored\_type，函数名可以自行命名，该函数通过判断模型字段数据内容，从而返回不同的字体颜色。 然后admin.py的类ProductAdmin属性list\_display中添加模型Product的函数colored\_type,使该函数以表头的形式显示在Admin后台的数据信息页面上

函数get\_queryset

函数get\_queryset根据不同的用户角色设置数据的访问权限，该函数可以将一些重要的数据进行过滤。以模型Product模型为例，在admin.py类ProductAdmin中重写函数get\_queryset，代码如下：

## admin.py类ProductAdmin

## 根据当前用户名设置数据访问权限

def get\_queryset\(self, request\): qs = super\(ProductAdmin, self\).get\_queryset\(request\) if request.user.is\_superuser: return qs else: return qs.filter\(id\_\_lt=6\) 1 2 3 4 5 6 7 8 自定义get\_queryset代码说明如下：

首先通过super方法来获取父类ModelAdmin的函数get\_queryset所生成的查询对象，该对象用于查询模型Product的全部数据。 判断当前用户身份，如果使超级用户，返回Product的全部数据，否则只返回模型Product的前五条数据。 函数formfield\_for\_foreignkey

函数formfield\_for\_foreignkey用于在新增或修改数据是，设置外键的可选性，如果模型中将某一字段定义为外键类型，新增数据时，该字段为一个下拉框空间，下拉框中数据来自于字段所指的数据模型。

如果想要对下拉框中的数据实现过滤功能，可以对函数formfield\_for\_foreignkey进行重写，如下：

## admin.py \# 新增或修改数据时，设置外键可选值

def formfield\_for\_foreignkey\(self, db\_field, request, **kwargs\): if db\_field.name == 'type': if not request.user.is\_superuser: kwargs\["queryset"\] = Type.objects.filter\(id\_\_lt=4\) return super\(admin.ModelAdmin, self\).formfield\_for\_foreignkey\(db\_field, request,** kwargs\) 1 2 3 4 5 6 代码是说明如下：

参数db\_field是模型Product的外键对象，一个模型可以定义多个外键，因此函数首先对外键进行判断。 然后判单当前用户是否为超级用户，参数request时当前用户的请求对象。 如果当前用户为普通用户，设置参数kwargs的queryset，参数kwargs是以字典形式作为函数参数，queryset是参数kwargs的健。 最后将设置好的参数kwargs传递给父类函数formfield\_for\_foreignkey重新执行。 函数save\_model

函数save\_model是在新增或修改数据库的时候，点击保存按钮所出发的功能，该函数主要是对输入的数据进行入库和更新处理。若想在这功能中添加一些特殊的功能需秋，可以对函数save\_model进行重写，代码如下：

## admin.py \# 修改保存方法

def save\_model\(self, request, obj, form, change\): if change:

## 获取当前用户名

user = request.user

## 使用模型获取修改数据

name = self.model.objects.get\(pk=obj.pk\).name

## 使用表单获取修改数据

weight = form.cleaned\_data\['weight'\]

## 写入日志文件

f = open\('e://MyDjango\_log.txt', 'a'\) f.write\('产品：'+str\(name\)+'，被用户：'+str\(user\)+'修改'+'\r\n'\) f.close\(\) else: pass

## 使用super可使自定义save\_model既保留父类的已有的功能并添加自定义功能。 super\(ProductAdmin, self\).save\_model\(request, obj, form, change\)

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 功能说明如下：

判断参数change是否为True，若为True，说明操作为数据修改，反之为新增数据。 分别从三个函数参数中获取相关的数据内容。参数request代表当前用户的请求对象，参数obj代表当前数据所对应的数据模型对象，参数form代表Admin的数据修改页面所对应的数据表单。 然后将所获取的数据写入文本，实现简单的日志记录功能。 最后使用super方法重写函数save\_model执行原有函数save\_model的功能，对数据进行入库和更新处理。若将此地阿玛注释，当触发重写函数时，程序不执行日志纪律功能，并不执行数据库入库和变更处理。 自定义模板

Admin后台系统的HTML模板时有Django提供的，我们可以在Django的安装目录下找到Admin模板所在路径（D:\Ananaconda3\Lib\site-packages\django\contrib\admin\templates\admin）。如果想对Admin的模板进行自定义更改，可直接修改Django里面的Admin模板，但一般不提倡这种方法。如果一台计算机同时开发多个Django项目，这样可能会影响其他项目的使用。除了这种方法之外，还可以利用模板继承的方法实现自定义模板开发。我们对MyDjango项目的目录进行调整：

在项目中创建模板文件夹templates，在templates下一次创建文件夹admin和index，说明如下：

文件夹admin代表该文件里的模板用于Admin后台管理系统，而且文件夹必须命名为admin。 文件夹index代表项目的App，文件夹必须与App相同。该文件夹存放模板文件change\_form.html，并且模板文件只是用index的后台数据。 如果模板change\_form.html放在admin文件夹下，说明该文件适用于当前项目所有的App。 在创建为文件夹templates时，切勿忘记在项目settings.py中配置templates的路径信息。最后在模板change\_form.html中编写如下代码：

1 2 3 4 5 6 7 8 9 10 11 12 代码说明如下：

自定义模板change\_form.html首先继承自Admin源模板change\_form.html自定义模板的命名必须与源模版的命名一致。 例如源模板admin/change\_form.html导入了标签

，因此，模板change\_form,html也需要导入改模板标签。 通过使用block标签实现源模板的代码重写。我们查看源模板的代码发现，模板代码以

形式分块处理，将网页上不同功能都一一区分。因此在自定义模板中使用block标签可对某个功能进行自定义开放。

九，Auth认证系统 Django除了强大的Admin管理系统外，还提供了完善的用户挂案例系统。整个用户管理系统分为三部分：用户信息、用户权限和用户组，在数据库中分别对应数据表auth\_user、auth\_permission和auth\_group。

1，内置User实现用户管理 用户管理已经是一个网站必须的功能之一，而Django内置强大的用户管理系统，并且具有灵活的扩展性，可以满足多方面的开发需求。在创建Django项目时，Django已默认使用内置用户管理系统，在settings.py的INSTALLED\_APPS、MIDDLEWARE和AUTH\_PASSWORD\_VALIDATORS中可以看到相关配置信息。

本节使用内置的用户管理系统实现用户的注册、登录、修改密码和注销功能。以MyDjango为例，在项目中创建App，命名为user，并且在settings.py和urls.py中配置App的信息，代码如下：

## settings.py

INSTALLED\_APPS = \[ 'django.contrib.admin', 'django.contrib.auth', 'django.contrib.contenttypes', 'django.contrib.sessions', 'django.contrib.messages', 'django.contrib.staticfiles', 'index', 'user', \]

## MyDjango/urls.py

from django.contrib import admin from django.urls import path,include urlpatterns = \[ path\('admin/', admin.site.urls\), path\('', include\('index.urls'\)\), path\('user/', include\('user.urls'\)\) \] 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 完成user的基本配置后，在App中分别添加urls.py和user.html文件，如下：

App中的urls.py主要用于接收和处理根目录的urls.py的请求信息。在App的urls.py中设定4个不同的URL地址，分别代表用户登录、注册、修改密码和注销，如下：

## user/urls.py

from django.urls import path from . import views urlpatterns = \[ path\('login.html', views.loginView, name='login'\),  
path\('register.html', views.registerView, name='register'\), path\('setpassword.html', views.setpasswordView,name='setpassword'\), path\('logout.html', views.logoutView, name='logout'\), \] 1 2 3 4 5 6 7 8 9 上述URL地址分别对应视图函数loginView、registerView、setpasswordView和logoutView；参数name用于设置URL的命名，可直接在HTML模板上使用并声称相应的URL。HTML模板代码结构：

## user/templates/urls.html

&lt;!DOCTYPE html&gt;

 {{ title }}      MyDjango Auth

用户名: 密 码:

确定 &lt;/form&gt;  [{{ unit\_2\_name }}](%7B%7B%20unit_2%20%7D%7D)  [{{ unit\_1\_name }}](%7B%7B%20unit_1%20%7D%7D) &lt;/div&gt; &lt;/div&gt; &lt;/div&gt; &lt;/div&gt; &lt;/div&gt; &lt;/body&gt; &lt;/html&gt; 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 一个模板分别用于实现用户登录，注册和密码修改，该模板是由两个文本输入框和一个按钮所组成的表单，在该表诞下设置不同的链接，分别指向另外两个URL地址 当用户输入账号和密码后，单机“确定”按钮就会出发一个POST请求，该请求将表单数据发送到当前的URL地址，再由相应的视图函数进行处理，并将处理结果返回到浏览器上生成相应网页。

接下来在views.py中实现用户登录功能，视图函数loginView代码如下：

from django.shortcuts import render,redirect from django.contrib.auth.models import User from django.contrib.auth import login, logout, authenticate

## Create your views here.

## 用户登录

def loginView\(request\):

## 设置标题和另外两个URL链接

title = '登录' unit\_2 = '/user/register.html' unit\_2\_name = '立即注册' unit\_1 = '/user/setpassword.html' unit\_1\_name = '修改密码' if request.method == 'POST': username = request.POST.get\('username', ''\) password = request.POST.get\('password', ''\) if User.objects.filter\(username=username\): user = authenticate\(username=username, password=password\) if user: if user.is\_active: login\(request, user\) return redirect\('/'\) else: tips = '账号密码错误，请重新输入' else: tips = '用户不存在，请注册' return render\(request, 'user.html', locals\(\)\) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 代码分析如下：

设置模板变量title和unit\_2等变量值，在模板中生成相关的URL地址，可以从登陆界面跳转到注册界面或者修改密码的界面 由于提交表单是由当前URL执行处理的，因此函数loginView需要处理不同的请求方式，视图函数首先判断当前请求方式，POST请求-获取表单中两个文本框数据内容，然后对模型User中数据进行验证，验证成功才跳转网页，否则提示错误信息。 如果GET请求，当完成模板变量赋值后就不再处理，直接将模板user.html生成HTML网页返回到浏览器上。 在整个登录过程中，我们没有对模型User进行定义，而函数中使用的函数User来自Django的内置模型，在数据库中对应数据表auth\_user，如下：

打开数据表auth\_user，可以通过表字段的命名了模型字段的含义，如下:

auth\_user数据表的十一个字段：

Id int类型，数据表的主键 Password varchar类型，代表用户密码，默认情况下使用pbkdf2\_sha256方式存储和管理用户的密码 last\_login datetime类型，最后一次登录的时间 is\_superuser tinyint类型，表示该用户是否拥超级权限 Username varchar类型，代表用户账号 first\_name varchar类型，代表用户的名字 last\_name varchar类型，代表用户的姓氏 Email varchar类型，代表用户的邮件 is\_staff 用来判断用户是否可以登录进入Admin系统 is\_active tinyint类型，用来判断该用户的状态是否被激活 date\_joined datetime类型，账号的创建时间 我们结合函数loginView和模型User的字段含义，分析一下loginView的代码功能：

当函数loginView收到Post请求并获取表单数据后，根据表单判断用户是否存在。当用户存在时，对用户账号和密码进行验证处理，由函数authenticate完成验证功能，如果验证成功，函数authenticate返回模型User的数据对象user，否则返回None。 然后从对象user的is\_active字段来判断当前用户的状态是否被激活，1代表激活，可以登录 最后执行用户登录，由内置的函数login完成登录过程。函数login接收两个参数，第一个request对象，来自视图函数参数request；第二个user对象，来自authenticate返回的对象user。 从整个登录过程中可以发现，Django为我们提供了完善的内置函数，可快速实现用户登录功能。为了更好地演示运行结果，在index中分别对模板index.html的

标签和视图函数index进行修改，如下：

## index.html的标签

1

## index/views.py的视图函数index

def index\(request\): username = request.user.username return render\(request, 'index.html', locals\(\)\) 1 2 3 4 在浏览器上访问[http://127.0.0.1:8000/user/login.html在用户登录界面输入用户的账号和密码，然后单击“确定”按钮，输入用户信息提交到视图函数loginView中完成登录过程，如下：](http://127.0.0.1:8000/user/login.html在用户登录界面输入用户的账号和密码，然后单击“确定”按钮，输入用户信息提交到视图函数loginView中完成登录过程，如下：)

上述例子用于实现用户登录，接下来完成用户注册功能，用户注册视图函数为registerView，在user的views.py中编写registerView，代码如下：

## user/views.py

## 用户注册

def registerView\(request\):

## 设置标题和另外两个URL链接

title = '注册' unit\_2 = '/user/login.html' unit\_2\_name = '立即登录' unit\_1 = '/user/setpassword.html' unit\_1\_name = '修改密码' if request.method == 'POST': username = request.POST.get\('username', ''\) password = request.POST.get\('password', ''\) if User.objects.filter\(username=username\): tips = '用户已存在' else: user = User.objects.create\_user\(username=username, password=password\) user.save\(\) tips = '注册成功，请登录' return render\(request, 'user.html', locals\(\)\) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 说明如下：

当用户输入账号和密码并单机“确定”按钮后，程序将表单数据提交到registerView中进行处理。 函数registerView首先获取表单的数据内容，根据获取的数据来判断模型User是否存在相关信息。 如果用户存在，直接返回到注册界面并提示用户已存在。 如果用户不存在，程序使用内置函数create\_user对模型User进行用户创建，函数create\_user是模型User特有的函数，该函数创建并保存一个is\_active=True的User对象。其中，函数参数username不能为空，否则抛出异常ValueError；模型User的其他字段可作为函数create\_user的可选参数，如Email、first\_name、password等。如果使用过程中没有设置函数参数password，User对象的set\_unusable\_password\(\)函数将会被调用，为当前用户创建一个随机密码。 最后在views.py中编写函数setpasswordView，实现修改用户密码功能，代码如下：

## user/views.py \# 修改密码

def setpasswordView\(request\):

## 设置标题和另外两个URL链接

title = '修改密码' unit\_2 = '/user/login.html' unit\_2\_name = '立即登录' unit\_1 = '/user/register.html' unit\_1\_name = '立即注册' new\_password = True if request.method == 'POST': username = request.POST.get\('username', ''\) old\_password = request.POST.get\('password', ''\) new\_password = request.POST.get\('new\_password', ''\) if User.objects.filter\(username=username\): user = authenticate\(username=username,password=old\_password\)

## 判断用户的账号密码是否正确

if user: user.set\_password\(new\_password\) user.save\(\) tips = '密码修改成功' else: tips = '原始密码不正确' else: tips = '用户不存在' return render\(request, 'user.html', locals\(\)\) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 密码修改界面比注册界面多出一个文本输入框，该文本输入框由模板变量new\_password控制显示。当new\_password为True时，文本输入框显示到页面上，如下：

函数setpasswordView处理逻辑如下：

当函数setpasswordView收到表单提交的请求后，程序会获取表单的数据内容，然后根据表单数据查找模型User相应的数据。 如果用户存在，由内置函数authenticate验证用户的用户和密码是否正确，若成功返回user对象，再使用内置函数set\_password修改user的密码，最后保存修改的user对象，从而实现修改密码。 如果用户不存在，直接返回到界面并提示用户不存在。 密码修改主要由内置函数set\_password实现，而函数set\_password时内置函数make\_password的基础上进行封装而来的。默认情况下Django使用pbkdf2\_sha256方式来存储和管理用户密码，而内置函数make\_password主要实现用户密码的加密功能，而且该函数可以脱离Auth认证系统单独使用，比对某些数据进行加密处理。上述例子中，使用函数make\_passwrod实现密码修改，代码如下：

## 使用make\_password实现密码修改

from django.contrib.auth.hashers import make\_password def setpasswordView\_1\(request\): if request.method == 'POST': username = request.POST.get\('username', ''\) old\_password = request.POST.get\('password', ''\) new\_password = request.POST.get\('new\_password', ''\)

## 判断用户是否存在

user = User.objects.filter\(username=username\) if User.objects.filter\(username=username\): user = authenticate\(username=username,password=old\_password\)

## 判断用户的账号密码是否正确

if user:

## 密码加密处理并保存到数据库

dj\_ps = make\_password\(new\_password, None, 'pbkdf2\_sha256'\) user.password = dj\_ps user.save\(\) else: print\('原始密码不正确'\) return render\(request, 'user.html', locals\(\)\) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 除了内置函数make\_password，还有内置函数check\_password，该函数时对加密前与加密后的密码进行验证匹配，判断二者是否为同一密码，在PyCharm的Terminal中开启Django的shell模式，函数make\_password和check\_password的使用方法如下：

python manage.py shell from django.contrib.auth.hashers import make\_password,check\_password ps = '123456' dj\_ps = make\_password\(ps, None, 'pbkdf2\_sha256' dj\_ps ps\_bool = check\_password\(ps, dj\_ps\) ps\_bool 1 2 3 4 用户注销时用户管理系统较为简单的功能，调用内置函数log\_out即可实现。函数logout接收参数request，代表当前用户的请求对象，来自视图函数的参数request。如下：

## 用户注销，退出登录

def logoutView\(request\): logout\(request\) return redirect\('/'\) 1 2 2，发送邮件实现密码找回 Django内置邮箱验证功能，可以实现密码找回。以QQ邮箱为例，首先进行如下设置： 设置-&gt;邮箱设置-&gt;账户-&gt;“POP3/IMAP/SMTP/Exchange/CardDAV/CalDAV服务“， 然后开启POP3/SMTP服务：

开启服务时，QQ邮箱会返回一个客户端授权密码，改密码用于登录第三方邮件客户端，保存授权密码，开发过程中使用。配置settings.py文件，添加QQ邮箱相关配置，如下：

## 邮件配置信息

EMAIL\_USE\_SSL = True

## 邮件服务器，如果是 163 改成 smtp.163.com

EMAIL\_HOST = 'smtp.qq.com'

## 邮件服务器端口

EMAIL\_PORT = 465

## 发送邮件的账号

EMAIL\_HOST\_USER = '185231027@qq.com'

## SMTP服务密码

EMAIL\_HOST\_PASSWORD = 'sgqcmjansmdxcbcc' DEFAULT\_FROM\_EMAIL = EMAIL\_HOST\_USER 1 2 3 4 5 6 7 8 9 10 11 上述配置是邮件发送方的邮件服务器信息，各个配置信息说明如下：

EMAIL\_USE\_SSL：设置Django与邮件服务器的连接方式为SSL。 EMAIL\_HOST： 设置服务器地址，改配置使用SMTP服务器。 EMAIL\_PORT： 设置服务器端口信息，若使用SMTP服务器，则端口号是465或587。 EMAIL\_HOST\_USER： 发送邮件的账号，该账号必须开启POP3/SMTP服务。 EMAIL\_HOST\_PASSWORD：客户端授权密码，即前一步骤保存的授权码。 DEFAULT\_FORM\_EMAIL： 设置默认发送邮件的账号。 完成邮箱相关配置后，我们在user的urls.py、模板user.html和views.py中编写功能实现代码如下：

## urls.py

from django.urls import path from . import views urlpatterns = \[ path\('findPassword.html', views.findPassword, name='findPassword'\), \] 1 2 3 4

## 模板user.html

&lt;!DOCTYPE html&gt;  找回密码      MyDjango Auth

&lt;form class="form" action="

" method="post"&gt;

用户名: 验证码:

 &lt;/form&gt; &lt;/div&gt; &lt;/div&gt; &lt;/div&gt; &lt;/div&gt; &lt;/body&gt; &lt;/html&gt; 1 2

## views.py的视图函数findPassword import random from django.shortcuts import render from django.contrib.auth.models import User from django.contrib.auth.hashers import make\_password \# 找回密码 def findPassword\(request\): button = '获取验证码' new\_password = False if request.method == 'POST': username = request.POST.get\('username', 'root'\) VerificationCode = request.POST.get\('VerificationCode', ''\) password = request.POST.get\('password', ''\) user = User.objects.filter\(username=username\) \# 用户不存在 if not user: tips = '用户' + username + '不存在' else: \# 判断验证码是否已发送 if not request.session.get\('VerificationCode', ''\): \# 发送验证码并将验证码写入session button = '重置密码' tips = '验证码已发送' new\_password = True VerificationCode = str\(random.randint\(1000, 9999\)\) request.session\['VerificationCode'\] = VerificationCode user\[0\].email\_user\('找回密码', VerificationCode\) \# 匹配输入的验证码是否正确 elif VerificationCode == request.session.get\('VerificationCode'\): \# 密码加密处理并保存到数据库 dj\_ps = make\_password\(password, None, 'pbkdf2\_sha256'\) user\[0\].password = dj\_ps user\[0\].save\(\) del request.session\['VerificationCode'\] tips = '密码已重置' \# 输入验证码错误 else: tips = '验证码错误，请重新获取' new\_password = False del request.session\['VerificationCode'\] return render\(request, 'user.html', locals\(\)\)

1 用户第一次访问[http://127.0.0.1:8000/user/findPassword.html的时候，出发了GET请求，视图函数findPassword直接将模板user.html返回，如下：](http://127.0.0.1:8000/user/findPassword.html的时候，出发了GET请求，视图函数findPassword直接将模板user.html返回，如下：)

当输入用户名并单机“获取验证码”按钮的时候，出发了POST请求，视图函数findPassword首先根据用户输入的用户名和模型User里的数据进行查找，判断用户名是否存在，若不存在，则会生成提示信息，如下：

如果用户存在，接着判断会话session的VerficationCode是否存在。若不存在，则视图函数findPassword通过发送邮件的方式将验证码发送到该用户的邮箱，验证码是使用random模块生成的4位数，最后将验证码写入会话session的VerficationCode，其作用与用户输入的验证码进行匹配。邮件发送是由内置函数email\_user实现，该方法是模型User特有的方法直以，只适用于模型User。需要注意的是，用户的邮箱来自模型User的字段email，如果当前用户邮件信息为空，邮件是无法发送出去的，如下：

当用户接收到验证码后，可在网页上输入验证码，然后单机“重置密码”按钮，这时会触发POST请求，函数findPassword获取用户输入的验证码并与会话session的VerficationCode进行对比，如果不符合，说明用户输入错误 ，系统提示验证码错误 ，如下：

如果验证码和会话session的VerificationCode相符合，那么程序会执行密码修改。首先获取用户输入密码，然后使用函数make\_password对密码加密处理并保存在模型User中，最后删除会话session的VerificationCode，否则会话一直存在，在下次获取验证码时，程序不会执行邮件发送功能。运行结果如下：

3，扩展User模型 开发过程中，模型User的字段可能满足不了复杂的开发需求。现在大多数网站的用户信息都有手机号、QQ号码和微信号等一系列个人信息。为了满足各种需求，Django提供了4种模型扩展的方法。

代理模型：这是一种模型继承，这种模型在数据库钟无需创建新的数据表。一般用于改变现有模型的行为方式，如增加新方法函数等，而且不影响现有数据库结构。当不需要数据库钟存储额外信息，而需要增加操作方法或改变模型的查询管理方式时，适合使用dialing模型来展现由User模型。 Profile扩展模型User：当存储信息与模型User相关，而且并不改变模型User原有的认证方法时，可定义新的模型MyUser形成一对一的关联，该方法称为用户配置（User Profile） AbstractBaseUser扩展模型User：当模型User的内置方法并不符合开发需求时，可使用该方法对模型User重新自定义设计，该方法对模型User和数据库框架影响很大。 AbstractUser扩展模型User：如果模型User内置的方法符合开发需求，在不改变这些函数方法的情况下，添加模型User的额外字段，可通过AbstractUser方式实现。使用AbstractUser定义的模型会替换原有模型User。 上述4种方法各有优缺点，一般情况下，建议使用AbstractUser扩展模型User，因为该方法对原有模型User影响较少而且无需额外创建数据表，下面讲解项目AbstractUser扩展模型User。首先在MySQL中找到项目琐事哦那个的数据库，并清除数据库中全部的数据表，在user的models.py文件中定义MyUser，代码如下：

## models.py

from django.db import models from django.contrib.auth.models import AbstractUser class MyUser\(AbstractUser\): qq = models.CharField\('QQ号码', max\_length=16\) weChat = models.CharField\('微信账号', max\_length=100\) mobile = models.CharField\('手机号码', max\_length=11\)

## 设置返回值

def **str**\(self\): return self.username 1 2 3 4 5 6 7 8 9 10 MyUser继承AbstractUser类，AbstractUser类已有内置模型User的字段属性，因此模型MyUser具有模型User的全部属性。在执行数据迁移（创建数据库）之前，必须在项目的settings.py中配置相关信息，如下：

## settings.py AUTH\_USER\_MODEL = 'user.MyUser'

1 配置信息是将内置模型User替换成user定义的模型MyUser，如果没有设置信息，在创建数据表的时候，会分别创建数据表auth\_user和user\_myuser。在PyCharm的Terminal下执行：

python manage.py makemigrations python manage.py migrate 1 完成数据迁移后，打开数据库查看数据表信息，可以发现内置模型User的数据表auth\_user改为数据表user\_myuser，并且数据表user\_myuser的字段除了User字段外，增加了自定义字段。

使用AbstractUser扩展模型User，实现过程为两个步骤：

定义新的模型MyUser，该模型必须继承AbstractUser类，在模型MyUser下定义字段为扩展字段。 在项目的配置文件settings.py中配置AUTH\_USER\_MODEL信息，在数据迁移时，将内置模型User替换成user定义的模型MyUser。 完成User扩展后，接着看模型MyUser与内置模型User在实际开发过程中的差异。首先用python manage.py createsuperuser，创建超级用户并登录Admin后台管理系统。

认证与授权没有用户信息表，因为模型MyUser是在user的 models.py中定义的。将模型MyUser展示在后台系统，则可以在user的admin.py中定义相关数据对象，如下：

## user/admin.py from django.contrib import admin from .models import MyUser from django.contrib.auth.admin import UserAdmin from django.utils.translation import gettext_lazy as_  @admin.register\(MyUser\) class MyUserAdmin\(UserAdmin\): list_display = \['username','email','mobile','qq','weChat'\] \# 新增用户时，在个人信息里添加'mobile','qq','weChat'的信息录入 \# 将源码的UserAdmin.fieldsets转换成列表格式 fieldsets = list\(UserAdmin.fieldsets\) \# 重写UserAdmin的fieldsets，添加'mobile','qq','weChat'的信息录入 fieldsets\[1\] = \(_\('Personal info'\), {'fields': \('first\_name', 'last\_name', 'email', 'mobile', 'qq', 'weChat'\)}\)

1

## user/**init**.py from django.apps import AppConfig import os \# 修改app在admin后台显示名称 \# default_appconfig的值来自apps.py的类名 default\_app\_config = 'user.IndexConfig' \# 获取当前app的命名 def get\_current\_app\_name\(\_file\): return os.path.split\(os.path.dirname\(\_file\)\)\[-1\] \# 重写类IndexConfig class IndexConfig\(AppConfig\): name = get\_current\_app\_name\(\_\_file_\) verbose\_name = '用户管理'

1 重启MyDjango项目并进入Admin后台管理系统，可以在界面上看到MyUser所生成的用户信息列表，如下：

进入用户信息的修改界面，可以看到新增字段，这是由MyUserAdmin类中重写属性fieldsets实现的，界面展示如下

4，设置用户权限 十一，缓存 1，缓存 缓存\(Cache\)对于创建一个高性能的网站和提升用户体验来说是非常重要的，缓存是一类可以更快的读取数据的介质统称，也指其它可以加快数据读取的存储方式。一般用来存储临时数据，常用介质的是读取速度很快的内存。一般来说从数据库多次把所需要的数据提取出来，要比从内存或者硬盘等一次读出来付出的成本大很多。对于中大型网站而言，使用缓存减少对数据库的访问次数是提升网站性能的关键之一

某些数据访问很频繁的场景下，通过缓存的方式，可以减少对于数据库的连接、查询请求、带宽等多个方面；同时也要注意的是缓存的占用空间，缓存的失效时间

2，Django 缓存应用场景 在Django中，当用户发送一个请求后，通过路由到达视图函数views，当视图函数需要通过template做数据渲染，那么就会通过ORM进行数据库查询操作来获取数据，然后再把数据返回给客户端，也就是页面。

如果每个用户每次请求都需要完整的走一遍数据渲染的过程，将会极大的降低性能，不仅仅数据库压力大，并且客户端也无法及时得到响应内容。

那么这个时候，缓存的使用场景就出现了，在Django中使用场景后，会是这样

当用户发送一个请求后，如果有缓存数据，则直接将缓存数据返回给用户，如果没有，才会查询数据库，并且将查询到的数据写入到缓存，然后将请求返回给客户端，并且客户端下次请求的时候会先访问缓存我数据

3，Django 缓存的五种配置 0、通用配置（以redis为例） CACHES = { “default”: { “BACKEND”: “django\_redis.cache.RedisCache”, “LOCATION”: “redis://10.55.75.20:6379/0”, ‘TIMEOUT’: 1800, \# 缓存超时时间（默认300，None表示永不过期，0表示立即过期） “OPTIONS”: { “MAX\_ENTRIES”: 300, \# 最大缓存个数（默认300） “CULL\_FREQUENCY”: 3, \# 缓存到达最大个数之后，剔除缓存个数的比例，即：1/CULL\_FREQUENCY（默认3） “CLIENT\_CLASS”: “django\_redis.client.DefaultClient”, \# redis客户端 “CONNECTION\_POOL\_KWARGS”: {“max\_connections”: 1}, \# redis最大连接池配置 “PASSWORD”: “password”, \# redis密码 }, ‘KEY\_PREFIX’: ‘Cache’, \# 缓存key的前缀（默认空） ‘VERSION’: 2, \# 缓存key的版本（默认1） }, }

1、开发调试 CACHES = { ‘default1’: { ‘BACKEND’: ‘django.core.cache.backends.dummy.DummyCache’, }, }

2、将缓存信息保存至文件 CACHES = { ‘default2’: { ‘BACKEND’: ‘django.core.cache.backends.filebased.FileBasedCache’, ‘LOCATION’: os.path.join\(BASE\_DIR, ‘cache’\), }, }

3、将缓存信息保存至内存 CACHES = { ‘default3’: { ‘BACKEND’: ‘django.core.cache.backends.locmem.LocMemCache’, ‘LOCATION’: ‘unique-snowflake’, }, }

4、将缓存信息保存至数据库 （注：需执行创建表命令 python3 manage.py createcachetable））

CACHES = { ‘default4’: { ‘BACKEND’: ‘django.core.cache.backends.db.DatabaseCache’, ‘LOCATION’: ‘my\_cache\_table’, \# 数据库表 }, }

5、将缓存信息保存至memcache（python-memcached模块）

​

CACHES = { 'default5': { 'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache', 'LOCATION': '127.0.0.1:11211', }, 'default6': { 'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache', 'LOCATION': 'unix:/tmp/memcached.sock', },

'default7': { 'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache', 'LOCATION': \[ '172.19.26.240:11211', '172.19.26.242:11211', \] },

## 我们也可以给缓存机器加权重，权重高的承担更多的请求，如下

'default8': { 'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache', 'LOCATION': \[ \('172.19.26.240:11211',5\), \('172.19.26.242:11211',10\), \] }, } 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 6、将缓存信息保存至memcache（pylibmc模块） CACHES = { ‘default9’: { ‘BACKEND’: ‘django.core.cache.backends.memcached.PyLibMCCache’, ‘LOCATION’: ‘127.0.0.1:11211’, }, ‘default10’: { ‘BACKEND’: ‘django.core.cache.backends.memcached.PyLibMCCache’, ‘LOCATION’: ‘/tmp/memcached.sock’, }, ‘default11’: { ‘BACKEND’: ‘django.core.cache.backends.memcached.PyLibMCCache’, ‘LOCATION’: \[ ‘172.19.26.240:11211’, ‘172.19.26.242:11211’, \] }, ‘default12’: { ‘BACKEND’: ‘django.core.cache.backends.memcached.PyLibMCCache’, ‘LOCATION’: \[ \(‘172.19.26.240:11211’,10\), \(‘172.19.26.242:11211’,10\), \] }, }

7、将缓存信息保存至redis（django-redis模块） CACHES = { “default”: { “BACKEND”: “django\_redis.cache.RedisCache”, “LOCATION”: “redis://10.55.75.20:6379/0”, “OPTIONS”: { “MAX\_ENTRIES”: 300, “CULL\_FREQUENCY”: 3, “CLIENT\_CLASS”: “django\_redis.client.DefaultClient”, “CONNECTION\_POOL\_KWARGS”: {“max\_connections”: 1}, “PASSWORD”: “password”, }, }, }

4，Django 缓存的三种应用 1、 全站使用 全站使用需要以下两个中间件 中间件最上层：django.middleware.cache.UpdateMiddleware 中间件最下层：django.middleware.cache.FetchFormCacheMiddleware

使用中间件，经过一系列的认证等操作，如果内容在缓存中存在，则使用FetchFromCacheMiddleware获取内容并返回给用户，当返回给用户之前，判断缓存中是否已经存在，如果不存在则UpdateCacheMiddleware会将缓存保存至缓存，从而实现全站缓存

在Django官方中指出：“更新”中间件必须是列表中的第一个，“获取”中间件必须是最后一个。细节有点模糊，但如果你想要完整的故事，请参阅下面的MIDDLEWARE。

MIDDLEWARE = \[ ‘django.middleware.cache.UpdateCacheMiddleware’, ‘django.middleware.security.SecurityMiddleware’, ‘django.contrib.sessions.middleware.SessionMiddleware’, ‘django.middleware.common.CommonMiddleware’, ‘django.middleware.csrf.CsrfViewMiddleware’, ‘django.contrib.auth.middleware.AuthenticationMiddleware’, ‘django.contrib.messages.middleware.MessageMiddleware’, ‘django.middleware.clickjacking.XFrameOptionsMiddleware’, ‘django.middleware.cache.FetchFromCacheMiddleware’, \]

middleware关于缓存的全局变量

配置文件位于当前项目中的路径：venv/lib/python3.7/site-packages/django/conf/global\_settings.py，建议通过settings.py 定义进行覆盖

CACHE\_MIDDLEWARE\_KEY\_PREFIX = ‘champzee’ \# 表示全局缓存的prefix CACHE\_MIDDLEWARE\_SECONDS = 600 \# 表示全局缓存的过期时间 CACHE\_MIDDLEWARE\_ALIAS = ‘default’ \# 表示全局缓存使用的CACHES配置

CACHES配置配置文件位于当前项目中的路径：venv/lib/python3.7/site-packages/django/conf/global\_settings.py，建议通过settings.py 定义进行覆盖CACHES = { “default”: { “BACKEND”: “django\_redis.cache.RedisCache”, “LOCATION”: “redis://10.55.75.20:6379/0”, ‘TIMEOUT’: 1800, “OPTIONS”: { “MAX\_ENTRIES”: 300, “CULL\_FREQUENCY”: 3, “CLIENT\_CLASS”: “django\_redis.client.DefaultClient”, “CONNECTION\_POOL\_KWARGS”: {“max\_connections”: 100}, “PASSWORD”: “password”, }, ‘KEY\_PREFIX’: ‘Cache’, ‘VERSION’: 2, }, }

视图函数

def cache1\(request\): import time ctime = time.strftime\("%Y-%m-%d %H:%M:%S"\) return render\(request, ‘cache1.html’, {‘ctime’: ctime}\)

def cache2\(request\): import time ctime = time.strftime\("%Y-%m-%d %H:%M:%S"\) return render\(request, ‘cache2.html’, {‘ctime’: ctime}\)

template模版（cache1和cache2模版）

   
    
  1 2 3 当我配置好了全局缓存中间件，CACHES redis配置，视图函数，模板之后；下面我发起一个请求，我们通过请求来查看redis是否生成缓存，并且查看缓存有效期是否为600秒

curl [http://127.0.0.1:8000/wanglei/cache1](http://127.0.0.1:8000/wanglei/cache1) curl [http://127.0.0.1:8000/wanglei/cache2](http://127.0.0.1:8000/wanglei/cache2)

可以看到如下内容，接着我们查看redis

2、视图使用 在视图使用中，我们就不需要配置middleware了，并且关于应用场景优先级第五小节会提到

CACHES配置

CACHES = { “default”: { “BACKEND”: “django\_redis.cache.RedisCache”, “LOCATION”: “redis://10.55.75.20:6379/0”, ‘TIMEOUT’: 1800, “OPTIONS”: { “MAX\_ENTRIES”: 300, “CULL\_FREQUENCY”: 3, “CLIENT\_CLASS”: “django\_redis.client.DefaultClient”, “CONNECTION\_POOL\_KWARGS”: {“max\_connections”: 100}, “PASSWORD”: “password”, }, ‘KEY\_PREFIX’: ‘Cache’, ‘VERSION’: 2, }, }

视图函数

在视图函数中，cache\_page需要引入，并且支持的三个参数为：缓存的失效时间，使用的CACHES的配置，key的前缀

from django.views.decorators.cache import cache\_page

@cache\_page\(timeout=120, cache=‘default’, key\_prefix=‘page3’\) def cache3\(request\): import time ctime = time.strftime\("%Y-%m-%d %H:%M:%S"\) return render\(request, ‘cache3.html’, {‘ctime’: ctime}\)

@cache\_page\(timeout=240, cache=‘default’, key\_prefix=‘page4’\) def cache4\(request\): import time ctime = time.strftime\("%Y-%m-%d %H:%M:%S"\) return render\(request, ‘cache4.html’, {‘ctime’: ctime}\)

template模版（cache3和cache4）

   
    
  1 2 3 当我配置好了 CACHES redis配置，视图函数，模板之后；下面我发起一个请求，我们通过请求来查看redis是否生成缓存，并且查看缓存有效期是否为120和240秒（这里说明一下，我把全局步骤中的部分session缓存进行了清理，所以生成的只有我们局部视图中的缓存）

curl [http://127.0.0.1:8000/wanglei/cache3](http://127.0.0.1:8000/wanglei/cache3) curl [http://127.0.0.1:8000/wanglei/cache4](http://127.0.0.1:8000/wanglei/cache4)

3、局部使用 只配置template即可（cache），需要先load cache

   
 1 2 3 4 5 6 7 8 9 下面我将访问局部cache curl [http://127.0.0.1:8000/wanglei/cache](http://127.0.0.1:8000/wanglei/cache)

我们可以看到，间隔一秒的访问，第一次三个ctime都是一样的；而当我第二次访问的时候，前两个数据使用局部缓存，而第三个数据获取到的是当前的时间

在redis中，我们可以看到用户访问生成了两条缓存，第一条key\_prefix为ctime\_key1，并且ttl时间为600；第二条key\_prefix为ctime\_key2，并且ttl时间为300秒

5，Django 缓存结论 1、关于优先级 全站配置 &gt; 视图配置 &gt; 局部配置

2、关于缓存名称的生成规则 缓存文件的名称生成方法位于当前项目下源码配置 venv/lib/python3.7/site-packages/django/core/cache/backends/base.py 中，里面定义了default\_key\_func方法；其实我们定义的全局变量就是传给它，然后它会生成一个统一的cache名称，存入CACHE配置中的缓存地址中

def default\_key\_func\(key, key\_prefix, version\): “”" Default function to generate keys.

Construct the key used by all other methods. By default, prepend the \`key\_prefix'. KEY\_FUNCTION can be used to specify an alternate function with custom key making behavior. """ print\('%s:%s:%s' % \(key\_prefix, version, key\)\)

## return '%s:%s' % \(key\_prefix, version\)

return '%s:%s:%s' % \(key\_prefix, version, key\) 1 2 3 4 5 6 7 3、关于django 缓存官方文档

[https://docs.djangoproject.com/en/2.1/topics/cache/](https://docs.djangoproject.com/en/2.1/topics/cache/)

十二，分布式任务队列Celery 1，简介 Celery是由Python开发、简单、灵活、可靠的分布式任务队列，其本质是生产者消费者模型，生产者发送任务到消息队列，消费者负责处理任务。Celery侧重于实时操作，但对调度支持也很好，其每分钟可以处理数以百万计的任务。特点：

简单：熟悉celery的工作流程后，配置使用简单 高可用：当任务执行失败或执行过程中发生连接中断，celery会自动尝试重新执行任务 快速：一个单进程的celery每分钟可处理上百万个任务 灵活：几乎celery的各个组件都可以被扩展及自定制 应用场景举例：

1.web应用：当用户在网站进行某个操作需要很长时间完成时，我们可以将这种操作交给Celery执行，直接返回给用户，等到Celery执行完成以后通知用户，大大提好网站的并发以及用户的体验感。

2.任务场景：比如在运维场景下需要批量在几百台机器执行某些命令或者任务，此时Celery可以轻松搞定。

3.定时任务：向定时导数据报表、定时发送通知类似场景，虽然Linux的计划任务可以帮我实现，但是非常不利于管理，而Celery可以提供管理接口和丰富的API。

2，架构&工作原理 Celery由以下三部分构成：消息中间件\(Broker\)、任务执行单元Worker、结果存储\(Backend\)，如下图：

工作原理：

任务模块Task包含异步任务和定时任务。其中，异步任务通常在业务逻辑中被触发并发往消息队列，而定时任务由Celery Beat进程周期性地将任务发往消息队列； 任务执行单元Worker实时监视消息队列获取队列中的任务执行； Woker执行完任务后将结果保存在Backend中; 消息中间件Broker

消息中间件Broker官方提供了很多备选方案，支持RabbitMQ、Redis、Amazon SQS、MongoDB、Memcached 等，官方推荐RabbitMQ。

任务执行单元Worker

Worker是任务执行单元，负责从消息队列中取出任务执行，它可以启动一个或者多个，也可以启动在不同的机器节点，这就是其实现分布式的核心。

结果存储Backend

Backend结果存储官方也提供了诸多的存储方式支持：RabbitMQ、 Redis、Memcached,SQLAlchemy, Django ORM、Apache Cassandra、Elasticsearch。

3，安装使用 这里我使用的redis作为消息中间件，redis安装可以参考[https://www.cnblogs.com/wdliu/p/9360286.html。](https://www.cnblogs.com/wdliu/p/9360286.html。)

Celery安装：

pip install celery 1 简单使用

目录结构：

project/ ├── **init**.py  
├── config.py └── tasks.py 1 2 3 4 各目录文件说明：

init.py:初始化Celery以及加载配置文件

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from celery import Celery app = Celery\('project'\) \# 创建 Celery 实例 app.config\_from\_object\('project.config'\) \# 加载配置模块 1 2 3 4 5 6 config.py: Celery相关配置文件，更多配置参考：[http://docs.celeryproject.org/en/latest/userguide/configuration.html](http://docs.celeryproject.org/en/latest/userguide/configuration.html)

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

BROKER\_URL = 'redis://10.1.210.69:6379/0' \# Broker配置，使用Redis作为消息中间件

CELERY\_RESULT\_BACKEND = 'redis://10.1.210.69:6379/0' \# BACKEND配置，这里使用redis

CELERY\_RESULT\_SERIALIZER = 'json' \# 结果序列化方案

CELERY\_TASK\_RESULT\_EXPIRES = 60  _60_  24 \# 任务过期时间

CELERY\_TIMEZONE='Asia/Shanghai' \# 时区配置

CELERY\_IMPORTS = \( \# 指定导入的任务模块,可以指定多个 'project.tasks', \) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 \[\]\(javascript:void\(0\)?

tasks.py ：任务定义文件

\[\]\(javascript:void\(0\)?

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from project import app @app.task def show\_name\(name\): return name 1 2 3 4 5 6 7 8 \[\]\(javascript:void\(0\)?

启动Worker：

celery worker -A project -l debug 1 各个参数含义：

worker: 代表第启动的角色是work当然还有beat等其他角色；

-A ：项目路径，这里我的目录是project

-l：启动的日志级别，更多参数使用celery --help查看

查看日志输出，会发现我们定义的任务，以及相关配置：

虽然启动了worker，但是我们还需要通过delay或apply\_async来将任务添加到worker中，这里我们通过交互式方法添加任务，并返回AsyncResult对象，通过AsyncResult对象获取结果：

AsyncResult除了get方法用于常用获取结果方法外还提以下常用方法或属性：

state: 返回任务状态； task\_id: 返回任务id； result: 返回任务结果，同get\(\)方法； ready\(\): 判断任务是否以及有结果，有结果为True，否则False； info\(\): 获取任务信息，默认为结果； wait\(t\): 等待t秒后获取结果，若任务执行完毕，则不等待直接获取结果，若任务在执行中，则wait期间一直阻塞，直到超时报错； successfu\(\): 判断任务是否成功，成功为True，否则为False； rabbitmq简单使用

1，在工程settings.py中INSTALLED\_APPS中加入djcelery,如下图所示

2，在工程settings.py加入broker相关配置，默认是以本机的mq服务作为broker。如果你需要配置成远程的mq，需要填写完整的BROKER\_URL = amqp://userid:password@hostname:port/virtual\_host，本文以远程broker为例

import djcelery djcelery.setup\_loader\(\)

## 数据库调度

CELERYBEAT\_SCHEDULER = ‘djcelery.schedulers.DatabaseScheduler’ BROKER\_URL= ‘amqp://myuser:password@服务器ip:5672/vhost’

3在工程设置目录下加入celery.py\(与settings.py同级\)

coding:utf8

from future import absolute\_import

import os

from celery import Celery from django.conf import settings

set the default Django settings module for the ‘celery’ program.

yourprojectname代表你工程的名字，在下面替换掉

os.environ.setdefault\(‘DJANGO\_SETTINGS\_MODULE’, ‘yourprojectname.settings’\)

app = Celery\(‘proj’\)

Using a string here means the worker will not have to

pickle the object when using Windows.

app.config\_from\_object\(‘django.conf:settings’\) app.autodiscover\_tasks\(lambda: settings.INSTALLED\_APPS\)

@app.task\(bind=True\) def debug\_task\(self\): print\(‘Request: {0!r}’.format\(self.request\)\)

4修改工程目录里面的init.py

from future import absolute\_import from .celery import app as celery\_app

5在应用目录下新建tasks.py，假设加入以下两个计算任务

from future import absolute\_import from celery import shared\_task

@shared\_task def add\(x, y\): return x + y

@shared\_task def mul\(x, y\): return x \* y

最后，运行celery worker python manage.py celery worker --loglevel=info

补充 Celery提供了一个工具flower，将各个任务的执行情况、各个worker的健康状态进行监控并以可视化的方式展现

安装flower: pip install flower 1

1. 启动flower（默认会启动一个webserver，端口为5555）:

python manage.py celery flower 1 3. 进入[http://localhost:5555即可查看。](http://localhost:5555即可查看。)

4，进阶使用 对于普通的任务来说可能满足不了我们的任务需求，所以还需要了解一些进阶用法，Celery提供了诸多调度方式，例如任务编排、根据任务状态执行不同的操作、重试机制等，以下会对常用高阶用法进行讲述。

定时任务&计划任务

Celery的提供的定时任务主要靠schedules来完成，通过beat组件周期性将任务发送给woker执行。在示例中，新建文件period\_task.py，并添加任务到配置文件中：

period\_task.py：

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from project import app from celery.schedules import crontab

@app.on\_after\_configure.connect def setup\_periodic\_tasks\(sender, \*\*kwargs\): sender.add\_periodic\_task\(10.0, add.s\(1,3\), name='1+3='\) \# 每10秒执行add sender.add\_periodic\_task\( crontab\(hour=16, minute=56, day\_of\_week=1\), \#每周一下午四点五十六执行sayhai sayhi.s\('wd'\),name='say\_hi' \)

@app.task def add\(x,y\): print\(x+y\) return x+y

@app.task def sayhi\(name\): return 'hello %s' % name 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 config.py

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

BROKER\_URL = 'redis://10.1.210.69:6379/0' \# Broker配置，使用Redis作为消息中间件

CELERY\_RESULT\_BACKEND = 'redis://10.1.210.69:6379/0' \# BACKEND配置，这里使用redis

CELERY\_RESULT\_SERIALIZER = 'json' \# 结果序列化方案

CELERY\_TASK\_RESULT\_EXPIRES = 60  _60_  24 \# 任务过期时间

CELERY\_TIMEZONE='Asia/Shanghai' \# 时区配置

CELERY\_IMPORTS = \( \# 指定导入的任务模块,可以指定多个 'project.tasks', 'project.period\_task', \#定时任务 \) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 启动worker和beat：

celery worker -A project -l debug \#启动work celery beat -A project.period\_task -l debug \#启动beat，注意此时对应的文件路径 1 2 我们可以观察worker日志：

还可以通过配置文件方式指定定时和计划任务,此时的配置文件如下：

\[\]\(javascript:void\(0\)?

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from project import app from celery.schedules import crontab

BROKER\_URL = 'redis://10.1.210.69:6379/0' \# Broker配置，使用Redis作为消息中间件

CELERY\_RESULT\_BACKEND = 'redis://10.1.210.69:6379/0' \# BACKEND配置，这里使用redis

CELERY\_RESULT\_SERIALIZER = 'json' \# 结果序列化方案

CELERY\_TASK\_RESULT\_EXPIRES = 60  _60_  24 \# 任务过期时间

CELERY\_TIMEZONE='Asia/Shanghai' \# 时区配置

CELERY\_IMPORTS = \( \# 指定导入的任务模块,可以指定多个 'project.tasks', 'project.period\_task', \)

app.conf.beat\_schedule = { 'period\_add\_task': { \# 计划任务 'task': 'project.period\_task.add', \#任务路径 'schedule': crontab\(hour=18, minute=16, day\_of\_week=1\), 'args': \(3, 4\), }, 'add-every-30-seconds': { \# 每10秒执行 'task': 'project.period\_task.sayhi', \#任务路径 'schedule': 10.0, 'args': \('wd',\) }, } 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 \[\]\(javascript:void\(0\)?

此时的period\_task.py只需要注册到woker中就行了，如下：

\[\]\(javascript:void\(0\)?

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from project import app

@app.task def add\(x,y\): print\(x+y\) return x+y

@app.task def sayhi\(name\): return 'hello %s' % name 1 2 3 4 5 6 7 8 9 10 11 12 13 14 \[\]\(javascript:void\(0\)?

同样启动worker和beat结果和第一种方式一样。更多详细的内容请参考：[http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html\#crontab-schedules](http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html#crontab-schedules)

任务绑定

Celery可通过任务绑定到实例获取到任务的上下文，这样我们可以在任务运行时候获取到任务的状态，记录相关日志等。

修改任务中的period\_task.py,如下：

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from project import app from celery.utils.log import get\_task\_logger

logger = get_tasklogger\(**name**\) @app.task\(bind=True\) \# 绑定任务 def add\(self,x,y\): logger.info\(self.request.\_\_dict_\) \#打印日志 try: a=\[\] a\[10\]==1 except Exception as e: raise self.retry\(exc=e, countdown=5, max\_retries=3\) \# 出错每5秒尝试一次，总共尝试3次 return x+y 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 在以上代码中，通过bind参数将任务绑定，self指任务的上下文，通过self获取任务状态，同时在任务出错时进行任务重试，我们观察日志：

内置钩子函数

Celery在执行任务时候，提供了钩子方法用于在任务执行完成时候进行对应的操作，在Task源码中提供了很多状态钩子函数如：on\_success\(成功后执行\)、on\_failure\(失败时候执行\)、on\_retry\(任务重试时候执行\)、after\_return\(任务返回时候执行\),在进行使用是我们只需要重写这些方法，完成相应的操作即可。

在以下示例中，我们继续修改period\_task.py，分别定义三个任务来演示任务失败、重试、任务成功后执行的操作：

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from project import app from celery.utils.log import get\_task\_logger from celery import Task

logger = get_tasklogger\(\_\_name_\)

class demotask\(Task\):

```text
def on_success(self, retval, task_id, args, kwargs):   # 任务成功执行
    logger.info('task id:{} , arg:{} , successful !'.format(task_id,args))



def on_failure(self, exc, task_id, args, kwargs, einfo):  #任务失败执行
    logger.info('task id:{} , arg:{} , failed ! erros : {}' .format(task_id,args,exc))


def on_retry(self, exc, task_id, args, kwargs, einfo):    #任务重试执行
    logger.info('task id:{} , arg:{} , retry !  einfo: {}'.format(task_id, args, exc))
```

@app.task\(base=demotask,bind=True\) def add\(self,x,y\): try: a=\[\] a\[10\]==1 except Exception as e: raise self.retry\(exc=e, countdown=5, max\_retries=1\) \# 出错每5秒尝试一次，总共尝试1次 return x+y

@app.task\(base=demotask\) def sayhi\(name\): a=\[\] a\[10\]==1 return 'hi {}'.format\(name\)

@app.task\(base=demotask\) def sum\(a,b\): return 'a+b={} '.format\(a+b\) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 此时的配置文件config.py：

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from project import app from celery.schedules import crontab

BROKER\_URL = 'redis://10.1.210.69:6379/0' \# Broker配置，使用Redis作为消息中间件

CELERY\_RESULT\_BACKEND = 'redis://10.1.210.69:6379/0' \# BACKEND配置，这里使用redis

CELERY\_RESULT\_SERIALIZER = 'json' \# 结果序列化方案

CELERY\_TASK\_RESULT\_EXPIRES = 60  _60_  24 \# 任务过期时间

CELERY\_TIMEZONE='Asia/Shanghai' \# 时区配置

CELERY\_IMPORTS = \( \# 指定导入的任务模块,可以指定多个 'project.tasks', 'project.period\_task', \)

app.conf.beat\_schedule = { 'add': { \# 每10秒执行 'task': 'project.period\_task.add', \#任务路径 'schedule': 10.0, 'args': \(10,12\), }, 'sayhi': { \# 每10秒执行 'task': 'project.period\_task.sayhi', \#任务路径 'schedule': 10.0, 'args': \('wd',\), }, 'sum': { \# 每10秒执行 'task': 'project.period\_task.sum', \#任务路径 'schedule': 10.0, 'args': \(1,3\), }, } 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 然后重启worker和beat，查看日志：

任务编排

在很多情况下，一个任务需要由多个子任务或者一个任务需要很多步骤才能完成，Celery同样也能实现这样的任务，完成这类型的任务通过以下模块完成：

group: 并行调度任务 chain: 链式任务调度 chord: 类似group，但分header和body2个部分，header可以是一个group任务，执行完成后调用body的任务 map: 映射调度，通过输入多个入参来多次调度同一个任务 starmap: 类似map，入参类似＊args chunks: 将任务按照一定数量进行分组 修改tasks.py：

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from project import app

@app.task def add\(x,y\): return x+y

@app.task def mul\(x,y\): return x\*y

@app.task def sum\(data\_list\): res=0 for i in data\_list: res+=i return res 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 group: 组任务，组内每个任务并行执行

和project同级目录新建consumer.py如下：

from celery import group from project.tasks import add,mul,sum res = group\(add.s\(1,2\),add.s\(1,2\)\)\(\) \# 任务 \[1+2,1+2\] while True: if res.ready\(\): print\('res:{}'.format\(res.get\(\)\)\) break 1 2 3 4 5 6 7 结果：

chain：链式任务

链式任务中，默认上一个任务的返回结果作为参数传递给子任务

from celery import chain from project.tasks import add,mul,sum res = chain\(add.s\(1,2\),add.s\(3\),mul.s\(3\)\)\(\) \# 任务\(\(1+2\)+3\)\*3 while True: if res.ready\(\): print\('res:{}'.format\(res.get\(\)\)\) break

## 结果

## res：18

1 2 3 4 5 6 7 8 9 还可以使用\|表示链式任务，上面任务也可以表示为：

res = \(add.s\(1,2\) \| add.s\(3\) \| \(mul.s\(3\)\)\)\(\) res.get\(\) 1 2 chord：任务分割，分为header和body两部分，hearder任务执行完在执行body,其中hearder返回结果作为参数传递给body

from celery import chord from project.tasks import add,mul,sum res = chord\(header=\[add.s\(1,2\),mul.s\(3,4\)\],body=sum.s\(\)\)\(\) \# 任务\(1+2\)+\(3\*4\) while True: if res.ready\(\): print\('res:{}'.format\(res.get\(\)\)\) break

## 结果：

## res:15

1 2 3 4 5 6 7 8 9 10 chunks：任务分组，按照任务的个数分组

from project.tasks import add,mul,sum res = add.chunks\(zip\(range\(5\),range\(5\)\),4\)\(\) \# 4 代表每组的任务的个数 while True: if res.ready\(\): print\('res:{}'.format\(res.get\(\)\)\) break 1 2 3 4 5 6 结果：

delay &apply\_async

对于delay和apply\_async都可以用来进行任务的调度，本质上是delay对apply\_async进行了再一次封装（或者可以说是快捷方式），两者都返回AsyncResult对象，以下是两个方法源码。

```text
def delay(self, *args, **kwargs):
    """Star argument version of :meth:`apply_async`.

    Does not support the extra options enabled by :meth:`apply_async`.

    Arguments:
        *args (Any): Positional arguments passed on to the task.
        **kwargs (Any): Keyword arguments passed on to the task.
    Returns:
        celery.result.AsyncResult: Future promise.
    """
    return self.apply_async(args, kwargs)
```

1 2 3 4 5 6 7 8 9 10 11 12 def apply\_async\(self, args=None, kwargs=None, task\_id=None, producer=None, link=None, link\_error=None, shadow=None, \*\*options\): """Apply tasks asynchronously by sending a message.

```text
    Arguments:
        args (Tuple): The positional arguments to pass on to the task.

        kwargs (Dict): The keyword arguments to pass on to the task.

        countdown (float): Number of seconds into the future that the
            task should execute.  Defaults to immediate execution.

        eta (~datetime.datetime): Absolute time and date of when the task
            should be executed.  May not be specified if `countdown`
            is also supplied.

        expires (float, ~datetime.datetime): Datetime or
            seconds in the future for the task should expire.
            The task won't be executed after the expiration time.

        shadow (str): Override task name used in logs/monitoring.
            Default is retrieved from :meth:`shadow_name`.

        connection (kombu.Connection): Re-use existing broker connection
            instead of acquiring one from the connection pool.

        retry (bool): If enabled sending of the task message will be
            retried in the event of connection loss or failure.
            Default is taken from the :setting:`task_publish_retry`
            setting.  Note that you need to handle the
            producer/connection manually for this to work.

        retry_policy (Mapping): Override the retry policy used.
            See the :setting:`task_publish_retry_policy` setting.

        queue (str, kombu.Queue): The queue to route the task to.
            This must be a key present in :setting:`task_queues`, or
            :setting:`task_create_missing_queues` must be
            enabled.  See :ref:`guide-routing` for more
            information.

        exchange (str, kombu.Exchange): Named custom exchange to send the
            task to.  Usually not used in combination with the ``queue``
            argument.

        routing_key (str): Custom routing key used to route the task to a
            worker server.  If in combination with a ``queue`` argument
            only used to specify custom routing keys to topic exchanges.

        priority (int): The task priority, a number between 0 and 9.
            Defaults to the :attr:`priority` attribute.

        serializer (str): Serialization method to use.
            Can be `pickle`, `json`, `yaml`, `msgpack` or any custom
            serialization method that's been registered
            with :mod:`kombu.serialization.registry`.
            Defaults to the :attr:`serializer` attribute.

        compression (str): Optional compression method
            to use.  Can be one of ``zlib``, ``bzip2``,
            or any custom compression methods registered with
            :func:`kombu.compression.register`.
            Defaults to the :setting:`task_compression` setting.

        link (Signature): A single, or a list of tasks signatures
            to apply if the task returns successfully.

        link_error (Signature): A single, or a list of task signatures
            to apply if an error occurs while executing the task.

        producer (kombu.Producer): custom producer to use when publishing
            the task.

        add_to_parent (bool): If set to True (default) and the task
            is applied while executing another task, then the result
            will be appended to the parent tasks ``request.children``
            attribute.  Trailing can also be disabled by default using the
            :attr:`trail` attribute

        publisher (kombu.Producer): Deprecated alias to ``producer``.

        headers (Dict): Message headers to be included in the message.

    Returns:
        celery.result.AsyncResult: Promise of future evaluation.

    Raises:
        TypeError: If not enough arguments are passed, or too many
            arguments are passed.  Note that signature checks may
            be disabled by specifying ``@task(typing=False)``.
        kombu.exceptions.OperationalError: If a connection to the
           transport cannot be made, or if the connection is lost.

    Note:
        Also supports all keyword arguments supported by
        :meth:`kombu.Producer.publish`.
    """
    if self.typing:
        try:
            check_arguments = self.__header__
        except AttributeError:  # pragma: no cover
            pass
        else:
            check_arguments(*(args or ()), **(kwargs or {}))

    app = self._get_app()
    if app.conf.task_always_eager:
        with denied_join_result():
            return self.apply(args, kwargs, task_id=task_id or uuid(),
                              link=link, link_error=link_error, **options)

    if self.__v2_compat__:
        shadow = shadow or self.shadow_name(self(), args, kwargs, options)
    else:
        shadow = shadow or self.shadow_name(args, kwargs, options)

    preopts = self._get_exec_options()
    options = dict(preopts, **options) if options else preopts

    options.setdefault('ignore_result', self.ignore_result)

    return app.send_task(
        self.name, args, kwargs, task_id=task_id, producer=producer,
        link=link, link_error=link_error, result_cls=self.AsyncResult,
        shadow=shadow, task_type=self,
        **options
    )
```

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99 100 101 102 103 104 105 106 107 108 109 110 111 112 113 114 115 116 117 118 119 120 121 122 123 124 125 126 127 对于其使用，apply\_async支持常用参数：

eta：指定任务执行时间，类型为datetime时间类型； countdown：倒计时,单位秒，浮点类型； expires：任务过期时间，如果任务在超过过期时间还未执行则回收任务，浮点类型获取datetime类型； retry：任务执行失败时候是否尝试，布尔类型。； serializer：序列化方案，支持pickle、json、yaml、msgpack； priority：任务优先级，有0～9优先级可设置，int类型； retry\_policy：任务重试机制，其中包含几个重试参数，类型是dict如下： max\_retries:最大重试次数

interval\_start:重试等待时间

interval\_step:每次重试叠加时长，假设第一重试等待1s，第二次等待1＋n秒

interval\_max:最大等待时间

**示例**

add.apply\_async\(\(1, 3\), retry=True, retry\_policy={ 'max\_retries': 1, 'interval\_start': 0, 'interval\_step': 0.8, 'interval\_max': 5, }\) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 更多参数参考：[http://docs.celeryproject.org/en/latest/reference/celery.app.task.html\#celery.app.task.Task.apply\_async](http://docs.celeryproject.org/en/latest/reference/celery.app.task.html#celery.app.task.Task.apply_async)

5，管理与监控 Celery管理和监控功能是通过flower组件实现的，flower组件不仅仅提供监控功能，还提供HTTP API可实现对woker和task的管理。

安装使用

pip3 install flower 1 启动

flower -A project --port=5555

## -A ：项目目录

## --port 指定端口

1 2 3 访问http:ip:5555

api使用，例如获取woker信息：

curl [http://127.0.0.1:5555/api/workers](http://127.0.0.1:5555/api/workers) 1 结果：

更多api参考：[https://flower.readthedocs.io/en/latest/api.html](https://flower.readthedocs.io/en/latest/api.html)

十三，Django中使用Celery 1，前言 Celery是一个基于python开发的分布式任务队列。而做python WEB开发最为流行的框架莫属Django，但是Django的请求处理过程都是同步的无法实现异步任务，若要实现异步任务处理需要通过其他方式（前端的一般解决方案是ajax操作），而后台Celery就是不错的选择。倘若一个用户在执行某些操作需要等待很久才返回，这大大降低了网站的吞吐量。下面将描述Django的请求处理大致流程（图片来源于网络）：

请求过程简单说明：浏览器发起请求–&gt;请求处理–&gt;请求经过中间件–&gt;路由映射–&gt;视图处理业务逻辑–&gt;响应请求\(template或response\)

2，配置使用 celery很容易集成到Django框架中，当然如果想要实现定时任务的话还需要安装django-celery-beta插件，后面会说明。需要注意的是Celery4.0只支持Django版本&gt;=1.8的，如果是小于1.8版本需要使用Celery3.1。

配置

新建立项目taskproj,目录结构（每个app下多了个tasks文件，用于定义任务）：

taskproj ├── app01 │ ├── **init**.py │ ├── apps.py │ ├── migrations │ │ └── **init**.py │ ├── models.py │ ├── tasks.py │ └── views.py ├── manage.py ├── taskproj │ ├── **init**.py │ ├── settings.py │ ├── urls.py │ └── wsgi.py └── templates 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 在项目目录taskproj/taskproj/目录下新建celery.py:

## !/usr/bin/env python3

## -_- coding:utf-8 -_-

## Author:wd

from **future** import absolute\_import, unicode\_literals import os from celery import Celery

os.environ.setdefault\('DJANGO\_SETTINGS\_MODULE', 'taskproj.settings'\) \# 设置django环境

app = Celery\('taskproj'\)

app.config_from\_object\('django.conf:settings', namespace='CELERY'\) \# 使用CELERY_ 作为前缀，在settings中写配置

app.autodiscover\_tasks\(\) \# 发现任务文件每个app下的task.py 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 taskproj/taskproj/init.py：

from **future** import absolute_import, unicodeliterals from .celery import app as celery\_app \_\_all_ = \['celery\_app'\] 1 2 3 taskproj/taskproj/settings.py

CELERY\_BROKER\_URL = 'redis://10.1.210.69:6379/0' \# Broker配置，使用Redis作为消息中间件

CELERY\_RESULT\_BACKEND = 'redis://10.1.210.69:6379/0' \# BACKEND配置，这里使用redis

CELERY\_RESULT\_SERIALIZER = 'json' \# 结果序列化方案 1 2 3 4 5 进入项目的taskproj目录启动worker：

celery worker -A taskproj -l debug 1 定义与触发任务

任务定义在每个tasks文件中，app01／tasks.py：

from **future** import absolute\_import, unicode\_literals from celery import shared\_task

@shared\_task def add\(x, y\): return x + y

@shared\_task def mul\(x, y\): return x \* y 1 2 3 4 5 6 7 8 9 10 11 12 视图中触发任务

from django.http import JsonResponse from app01 import tasks

## Create your views here.

def index\(request,_args,\*_kwargs\): res=tasks.add.delay\(1,3\)

```text
#任务逻辑
return JsonResponse({'status':'successful','task_id':res.task_id})
```

1 2 3 4 5 6 7 8 9 访问[http://127.0.0.1:8000/index](http://127.0.0.1:8000/index)

若想获取任务结果，可以通过task\_id使用AsyncResult获取结果,还可以直接通过backend获取：

扩展

除了redis、rabbitmq能做结果存储外，还可以使用Django的orm作为结果存储，当然需要安装依赖插件，这样的好处在于我们可以直接通过django的数据查看到任务状态，同时为可以制定更多的操作，下面介绍如何使用orm作为结果存储。

1.安装

pip install django-celery-results 1 2.配置settings.py，注册app

INSTALLED\_APPS = \( ..., 'django\_celery\_results', \) 1 2 3 4 4.修改backend配置，将redis改为django-db

## CELERY\_RESULT\_BACKEND = 'redis://10.1.210.69:6379/0' \# BACKEND配置，这里使用redis

CELERY\_RESULT\_BACKEND = 'django-db' \#使用django orm 作为结果存储 1 2 3 5.修改数据库

python3 manage.py migrate django\_celery\_results 1 此时会看到数据库会多创建：

当然你有时候需要对task表进行操作，以下源码的表结构定义：

class TaskResult\(models.Model\): """Task result/status."""

```text
task_id = models.CharField(_('task id'), max_length=255, unique=True)
task_name = models.CharField(_('task name'), null=True, max_length=255)
task_args = models.TextField(_('task arguments'), null=True)
task_kwargs = models.TextField(_('task kwargs'), null=True)
status = models.CharField(_('state'), max_length=50,
                          default=states.PENDING,
                          choices=TASK_STATE_CHOICES
                          )
content_type = models.CharField(_('content type'), max_length=128)
content_encoding = models.CharField(_('content encoding'), max_length=64)
result = models.TextField(null=True, default=None, editable=False)
date_done = models.DateTimeField(_('done at'), auto_now=True)
traceback = models.TextField(_('traceback'), blank=True, null=True)
hidden = models.BooleanField(editable=False, default=False, db_index=True)
meta = models.TextField(null=True, default=None, editable=False)

objects = managers.TaskResultManager()

class Meta:
    """Table information."""

    ordering = ['-date_done']

    verbose_name = _('task result')
    verbose_name_plural = _('task results')

def as_dict(self):
    return {
        'task_id': self.task_id,
        'task_name': self.task_name,
        'task_args': self.task_args,
        'task_kwargs': self.task_kwargs,
        'status': self.status,
        'result': self.result,
        'date_done': self.date_done,
        'traceback': self.traceback,
        'meta': self.meta,
    }

def __str__(self):
    return '<Task: {0.task_id} ({0.status})>'.format(self)
```

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 \[\]\(javascript:void\(0\)?

3，Django中使用定时任务 如果想要在django中使用定时任务功能同样是靠beat完成任务发送功能，当在Django中使用定时任务时，需要安装django-celery-beat插件。以下将介绍使用过程。

1，安装配置

1.beat插件安装

pip3 install django-celery-beat 1 2.注册APP

INSTALLED\_APPS = \[ ....  
'django\_celery\_beat', \] 1 2 3 4 3.数据库变更

python3 manage.py migrate django\_celery\_beat 1 4.分别启动woker和beta

celery -A proj beat -l info --scheduler django\_celery\_beat.schedulers:DatabaseScheduler \#启动beta 调度器使用数据库

celery worker -A taskproj -l info \#启动woker 1 2 3 5.配置admin

urls.py

## urls.py

from django.conf.urls import url from django.contrib import admin

urlpatterns = \[ url\(r'^admin/', admin.site.urls\), \] 1 2 3 4 5 6 7 6.创建用户

python3 manage.py createsuperuser 1 7.登录admin进行管理（地址[http://127.0.0.1:8000/admin）并且还可以看到我们上次使用orm作为结果存储的表。](http://127.0.0.1:8000/admin）并且还可以看到我们上次使用orm作为结果存储的表。)

[http://127.0.0.1:8000/admin/login/?next=/admin/](http://127.0.0.1:8000/admin/login/?next=/admin/)

使用示例：

查看结果：

二次开发

django-celery-beat插件本质上是对数据库表变化检查，一旦有数据库表改变，调度器重新读取任务进行调度，所以如果想自己定制的任务页面，只需要操作beat插件的四张表就可以了。当然你还可以自己定义调度器，django-celery-beat插件已经内置了model，只需要进行导入便可进行orm操作，以下我用django reset api进行示例：

settings.py

\[\]\(javascript:void\(0\)?

INSTALLED\_APPS = \[ 'django.contrib.admin', 'django.contrib.auth', 'django.contrib.contenttypes', 'django.contrib.sessions', 'django.contrib.messages', 'django.contrib.staticfiles', 'app01.apps.App01Config', 'django\_celery\_results', 'django\_celery\_beat', 'rest\_framework', \] 1 2 3 4 5 6 7 8 9 10 11 12 urls.py

urlpatterns = \[ url\(r'^admin/', admin.site.urls\), url\(r'^index$', views.index\), url\(r'^res$', views.get\_res\), url\(r'^tasks$', views.TaskView.as\_view\({'get':'list'}\)\), \] 1 2 3 4 5 6 views.py

from django_celerybeat.models import PeriodicTask \#倒入插件model from rest\_framework import serializers from rest\_framework import pagination from rest\_framework.viewsets import ModelViewSet class Userserializer\(serializers.ModelSerializer\): class Meta: model = PeriodicTask fields = '\_\_all_'

class Mypagination\(pagination.PageNumberPagination\): """自定义分页""" page\_size=2 page\_query\_param = 'p' page\_size\_query\_param='size' max\_page\_size=4

class TaskView\(ModelViewSet\): queryset = PeriodicTask.objects.all\(\) serializer\_class = Userserializer permission\_classes = \[\] pagination\_class = Mypagination 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 访问[http://127.0.0.1:8000/tasks如下：](http://127.0.0.1:8000/tasks如下：)

十四，django 全文搜索引擎 1.安装全文检索包

## 全文检索框架

pip install django-haystack

## 全文检索引擎

pip install whoosh

## 中文分词框架

pip install jieba 1 2 3 4 5 6 heystack一些配置都是固定写好的,需要注意下

2.配置全文检索 1.安装haystack应用

INSTALLED\_APPS = \( ... 'haystack', \) 1 2 3 4 2.在settings.py文件中配置搜索引擎

## 配置搜索引擎后端

HAYSTACK\_CONNECTIONS = { 'default': {

## 使用whoosh引擎：提示，如果不需要使用jieba框架实现分词，就使用whoosh\_backend

```text
  'ENGINE': 'haystack.backends.whoosh_cn_backend.WhooshEngine',
  # 索引文件路径
  'PATH': os.path.join(BASE_DIR, 'whoosh_index'),  # 在项目目录下创建文件夹 whoosh_index
```

} }

## 当添加、修改、删除数据时，自动生成索引

HAYSTACK\_SIGNAL\_PROCESSOR = 'haystack.signals.RealtimeSignalProcessor' 1 2 3 4 5 6 7 8 9 10 11 3.在要建立索引的表对应的应用下，创建search\_indexes.py文件 定义商品索引类GoodsSKUIndex\(\)，继承自indexes.SearchIndex和indexes.Indexable

from haystack import indexes from .models import GoodsSKU class GoodsSKUIndex\(indexes.SearchIndex, indexes.Indexable\): \# 定义字符类型的属性，名称固定为text \# document=True表示建立的索引数据存储到文件中 \# use\_template=True表示通过模板指定表中的字段，用于查询 text = indexes.CharField\(document=True, use\_template=True\)

\# 针对哪张表进行查询 def get\_model\(self\): return GoodsSKU

\# 针对哪些行进行查询 def index\_queryset\(self, using=None\): return self.get\_model\(\).objects.filter\(isDelete=False\) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 4.指定要建立索引的字段 在templates下面新建目录search/indexes/应用名 比如goods应用中的GoodsSKU模型类中的字段要建立索引文件夹：search/indexes/goods 在新建目录下，创建goodssku\_text.txt,并编辑要建立索引的字段，如下图 templates/search/indexes/goods/goodssku\_text\_txt

5.生成索引文件

## 在终端运行, 提示是否要删除原有信息, 输入y

python manage.py rebuild\_index  
1 2 搜索表单处理 搜索地址：/search/ 搜索方法：get \`\`接收关键字：q action="/search/" method=“get” 文本框的name= “q” 为固定写法

配置搜索地址正则

import haystack.urls url\(r'^search/', include\(haystack.urls\)\), 1 2 测试搜索效果，接收结果

全文检索结果： 搜索出结果后，haystack会把搜索出的结果传递给templates/search目录下的search.html 对于search.html，我们需要自己建立该html文件，并定义自己的搜索结果页面 传递的上下文包括： query：搜索关键字 page：当前页的page对象 paginator：分页paginator对象 提示： settings.py文件中设置HAYSTACK\_SEARCH\_RESULTS\_PER\_PAGE 通过HAYSTACK\_SEARCH\_RESULTS\_PER\_PAGE可以控制每页显示数量 每页显示一条数据：HAYSTACK\_SEARCH\_RESULTS\_PER\_PAGE = 1 自己定义调度器，django-celery-beat插件已经内置了model，只需要进行导入便可进行orm操作，以下我用django reset api进行示例： settings.py

\[\[外链图片转存中…\(img-kenmVhjY-1567407811293\)\]\]\(javascript:void\(0\)?

INSTALLED\_APPS = \[ 'django.contrib.admin', 'django.contrib.auth', 'django.contrib.contenttypes', 'django.contrib.sessions', 'django.contrib.messages', 'django.contrib.staticfiles', 'app01.apps.App01Config', 'django\_celery\_results', 'django\_celery\_beat', 'rest\_framework', \] 1 2 3 4 5 6 7 8 9 10 11 12 urls.py

urlpatterns = \[ url\(r'^admin/', admin.site.urls\), url\(r'^index$', views.index\), url\(r'^res$', views.get\_res\), url\(r'^tasks$', views.TaskView.as\_view\({'get':'list'}\)\), \] 1 2 3 4 5 6 views.py

from django_celerybeat.models import PeriodicTask \#倒入插件model from rest\_framework import serializers from rest\_framework import pagination from rest\_framework.viewsets import ModelViewSet class Userserializer\(serializers.ModelSerializer\): class Meta: model = PeriodicTask fields = '\_\_all_'

class Mypagination\(pagination.PageNumberPagination\): """自定义分页""" page\_size=2 page\_query\_param = 'p' page\_size\_query\_param='size' max\_page\_size=4

class TaskView\(ModelViewSet\): queryset = PeriodicTask.objects.all\(\) serializer\_class = Userserializer permission\_classes = \[\] pagination\_class = Mypagination 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 访问[http://127.0.0.1:8000/tasks如下：](http://127.0.0.1:8000/tasks如下：)

\[外链图片转存中…\(img-cJVRNU7m-1567407811294\)\]

十四，django 全文搜索引擎 1.安装全文检索包

## 全文检索框架

pip install django-haystack

## 全文检索引擎

pip install whoosh

## 中文分词框架

pip install jieba 1 2 3 4 5 6 heystack一些配置都是固定写好的,需要注意下

2.配置全文检索 1.安装haystack应用

INSTALLED\_APPS = \( ... 'haystack', \) 1 2 3 4 2.在settings.py文件中配置搜索引擎

## 配置搜索引擎后端

HAYSTACK\_CONNECTIONS = { 'default': {

## 使用whoosh引擎：提示，如果不需要使用jieba框架实现分词，就使用whoosh\_backend

```text
  'ENGINE': 'haystack.backends.whoosh_cn_backend.WhooshEngine',
  # 索引文件路径
  'PATH': os.path.join(BASE_DIR, 'whoosh_index'),  # 在项目目录下创建文件夹 whoosh_index
```

} }

## 当添加、修改、删除数据时，自动生成索引

HAYSTACK\_SIGNAL\_PROCESSOR = 'haystack.signals.RealtimeSignalProcessor' 1 2 3 4 5 6 7 8 9 10 11 3.在要建立索引的表对应的应用下，创建search\_indexes.py文件 定义商品索引类GoodsSKUIndex\(\)，继承自indexes.SearchIndex和indexes.Indexable

from haystack import indexes from .models import GoodsSKU class GoodsSKUIndex\(indexes.SearchIndex, indexes.Indexable\): \# 定义字符类型的属性，名称固定为text \# document=True表示建立的索引数据存储到文件中 \# use\_template=True表示通过模板指定表中的字段，用于查询 text = indexes.CharField\(document=True, use\_template=True\)

\# 针对哪张表进行查询 def get\_model\(self\): return GoodsSKU

\# 针对哪些行进行查询 def index\_queryset\(self, using=None\): return self.get\_model\(\).objects.filter\(isDelete=False\) 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 4.指定要建立索引的字段 在templates下面新建目录search/indexes/应用名 比如goods应用中的GoodsSKU模型类中的字段要建立索引文件夹：search/indexes/goods 在新建目录下，创建goodssku\_text.txt,并编辑要建立索引的字段，如下图 templates/search/indexes/goods/goodssku\_text\_txt \[外链图片转存中…\(img-2BiSM3it-1567407811295\)\]

5.生成索引文件

## 在终端运行, 提示是否要删除原有信息, 输入y

python manage.py rebuild\_index  
1 2 搜索表单处理 搜索地址：/search/ 搜索方法：get \`\`接收关键字：q action="/search/" method=“get” 文本框的name= “q” 为固定写法 \[外链图片转存中…\(img-oQVxrUVn-1567407811296\)\] 配置搜索地址正则

import haystack.urls url\(r'^search/', include\(haystack.urls\)\), 1 2 测试搜索效果，接收结果

全文检索结果： 搜索出结果后，haystack会把搜索出的结果传递给templates/search目录下的search.html 对于search.html，我们需要自己建立该html文件，并定义自己的搜索结果页面 传递的上下文包括： query：搜索关键字 page：当前页的page对象 paginator：分页paginator对象 提示： settings.py文件中设置HAYSTACK\_SEARCH\_RESULTS\_PER\_PAGE 通过HAYSTACK\_SEARCH\_RESULTS\_PER\_PAGE可以控制每页显示数量 每页显示一条数据：HAYSTACK\_SEARCH\_RESULTS\_PER\_PAGE = 1 search.html编写，类似商品列表页面 ———————————————— 版权声明：本文为CSDN博主「aqiu12316」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。 原文链接：[https://blog.csdn.net/sumaoyan1787/article/details/100284886](https://blog.csdn.net/sumaoyan1787/article/details/100284886)

