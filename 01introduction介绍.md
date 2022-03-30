## 介绍

web2py [ [web2py](http://web2py.com/books/default/reference/29/web2py) ] 是一个免费的开源 Web 框架，用于敏捷开发安全的数据库驱动的 Web 应用程序；它是用 Python [ [python](http://web2py.com/books/default/reference/29/python) ]编写的，并且可以用 Python 编程。web2py 是一个全栈框架，这意味着它包含构建功能齐全的 Web 应用程序所需的所有组件。

web2py 旨在指导 Web 开发人员遵循良好的软件工程实践，例如使用模型视图控制器 (MVC) 模式。web2py 将数据表示（模型）与数据表示（视图）以及应用程序逻辑和工作流程（控制器）分开。web2py 提供库来帮助开发人员分别设计、实现和测试这三个部分中的每一个，并使它们协同工作。

web2py 是为安全而构建的。这意味着它可以通过遵循完善的实践自动解决许多可能导致安全漏洞的问题。例如，它验证所有输入（以防止注入），转义所有输出（以防止跨站点脚本），重命名上传的文件（以防止目录遍历攻击）。web2py 负责主要的安全问题，因此开发人员引入漏洞的机会较少。

web2py 包含一个数据库抽象层 (DAL)，它动态地编写 SQL [ [sql-w](http://web2py.com/books/default/reference/29/sql-w) ] ，这样开发人员就不必这样做了。DAL 知道如何为 SQLite [ [sqlite](http://web2py.com/books/default/reference/29/sqlite) ]、MySQL [ [mysql](http://web2py.com/books/default/reference/29/mysql) ]、PostgreSQL [ [postgres](http://web2py.com/books/default/reference/29/postgres) ]、MSSQL [ [mssql](http://web2py.com/books/default/reference/29/mssql) ]、FireBird [ [firebird](http://web2py.com/books/default/reference/29/firebird) ]、Oracle [ [oracle](http://web2py.com/books/default/reference/29/oracle) ]、IBM DB2 [ [db2](http://web2py.com/books/default/reference/29/db2) ]、Informix [ [informix](http://web2py.com/books/default/reference/29/informix) ]和安格尔[ [ingresdb](http://web2py.com/books/default/reference/29/ingresdb) ].

在 Google App Engine (GAE) [ [gae](http://web2py.com/books/default/reference/29/gae) ]上运行时，DAL 还可以为 Google Datastore 生成函数调用。在实验上，我们支持更多的数据库，并且不断添加新的数据库。请查看 web2py 网站和邮件列表以获取最新的适配器。一旦定义了一个或多个数据库表，web2py 会自动生成一个功能齐全的基于 Web 的数据库管理界面来访问数据库和表。

web2py 与其他 Web 框架的不同之处在于它是唯一完全接受 Web 2.0 范式的框架，其中 Web 是计算机。事实上，web2py 不需要安装或配置；它运行在任何可以运行 Python（Windows、Windows CE、Mac OS X、iOS 和 Unix/Linux）的架构上，并且应用程序的开发、部署和维护阶段可以通过本地或远程 Web 界面完成。web2py 在 Python 2.7 和 Python 3 上与 CPython（C 实现）和 PyPy（用 Python 编写的 Python）一起运行。

web2py 为错误事件提供了一个票证系统。如果发生错误，则会向用户发出票证，并为管理员记录错误。

web2py 是开源的，在 LGPL 版本 3 许可下发布。

web2py 的另一个重要特性是我们，它的开发人员，承诺在未来的版本中保持向后兼容性。从 2007 年 10 月 web2py 的第一个版本开始，我们就这样做了。添加了新功能并修复了错误，但是如果一个程序与 web2py 1.0 一起工作，那么该程序今天会更好地工作。

下面是一些 web2py 语句的例子，它们说明了它的强大和简单。以下代码：

```
db.define_table('person', Field('name'), Field('image', 'upload'))
```

创建一个名为“person”的数据库表，其中包含两个字段：“name”，一个字符串；和“图像”，需要上传的东西（实际图像）。如果表已存在但与此定义不匹配，则对其进行适当更改。

给定上面定义的表，下面的代码：

```
form = SQLFORM(db.person).process()
```

为该表创建一个可插入表单，允许用户上传图像。它还验证提交的表单，以安全的方式重命名上传的图像，将图像存储在文件中，在数据库中插入相应的记录，防止重复提交，并最终通过添加错误消息来修改表单本身，如果提交的数据是用户未通过验证。

此代码嵌入了一个完整的 wiki，其中包含标签、搜索、标签云、权限、媒体附件和 oembed 支持：

```
def index(): return auth.wiki()
```

改为以下代码：

```
@auth.requires_permission ( 'read' ,  'person' ) 
def  f ():  ....
```

阻止访问者访问该函数`f`，除非访问者是一个组的成员，该组的成员有权“读取”表“person”的记录。如果访问者未登录，则访问者将被定向到登录页面（默认情况下由 web2py 提供）。

web2py 还支持组件，比如可以在视图中加载并通过 Ajax 与访问者交互而无需重新加载整个页面的操作。这是通过`LOAD`允许非常模块化的应用程序设计的助手完成的；它在第 3 章中的 wiki 上下文中进行了讨论，更详细地，在本书的最后一章中进行了讨论。

这本书的第 6 版描述了`web2py`2.4.1 和更高版本。



### 原则

Python 编程通常遵循以下基本原则：

- 不要重复自己。
- 做事应该只有一种方式。
- 显式优于隐式。

web2py 通过强制开发人员使用不鼓励重复代码的可靠软件工程实践来完全接受前两个原则。web2py 指导开发人员完成 Web 应用程序开发中几乎所有常见的任务（创建和处理表单、管理会话、cookie、错误等）。

web2py 与其他框架的第三个原则不同，有时会与其他两个发生冲突。特别是，web2py 不会导入用户应用程序，而是在预定义的上下文中执行它们。此上下文公开了 Python 关键字以及 web2py 关键字。

对某些人来说，这可能看起来像魔术，但它不应该。简单地说，在实践中，一些模块已经在你没有这样做的情况下被导入。web2py 试图避免其他框架的烦人特性，即迫使开发人员在每个模型和控制器的顶部导入相同的模块。

web2py通过导入自己的模块，既节省时间又防止错误，从而遵循“不要重复自己”和“应该只有一种做事方式”的精神。

如果开发人员希望使用其他 Python 模块或第三方模块，则必须显式导入这些模块，就像在任何其他 Python 程序中一样。



### 网络框架





在最基本的层面上，Web 应用程序由一组程序（或函数）组成，这些程序（或函数）在访问相应的 URL 时执行。程序的输出返回给访问者并由浏览器呈现。

Web 框架的目的是让开发人员能够快速、轻松且不出错地构建新应用程序。这是通过提供可减少和简化所需编码量的 API 和工具来实现的。

开发 Web 应用程序的两种经典方法是：

- 以编程方式生成 HTML 。
- 将代码嵌入 HTML 页面。

第一个方法例如，早期的 CGI 脚本。第二种方法，例如，PHP [ [php](http://web2py.com/books/default/reference/29/php) ]（代码是 PHP，一种类似 C 的语言）、ASP（代码是 Visual Basic）和 JSP（代码是 Java） .

下面是一个 PHP 程序示例，该程序在执行时从数据库中检索数据并返回一个显示所选记录的 HTML 页面：

```
<html><body><h1>Records</h1><?
  mysql_connect(localhost,username,password);
  @mysql_select_db(database) or die( "Unable to select database");
  $query="SELECT * FROM contacts";
  $result=mysql_query($query);
  mysql_close();
  $i=0;
  while ($i < mysql_numrows($result)) {
    $name=mysql_result($result,$i,"name");
    $phone=mysql_result($result,$i,"phone");
    echo "<b>$name</b><br>Phone:$phone<br /><br /><hr /><br />";
    $i++;
  }
?></body></html>
```

这种方法的问题在于代码被嵌入到 HTML 中，但是同样的代码还需要生成额外的 HTML 并生成 SQL 语句来查询数据库，从而纠缠了应用程序的多个层并且难以阅读和维护。Ajax 应用程序的情况更糟，而且复杂性随着组成应用程序的页面（文件）的数量而增加。

上面例子的功能可以用 web2py 用两行 Python 代码来表达：

```
def index():
    return HTML(BODY(H1('Records'), db().select(db.contacts.ALL)))
```

在这个简单的示例中，HTML 页面结构以编程方式由`HTML`、`BODY`和`H1`对象表示；通过命令`db`查询数据库；`select`最后，一切都被序列化为 HTML。请注意，这`db`不是关键字，而是用户定义的变量。我们将一致地使用此名称来指代数据库连接以避免混淆。

Web 框架分为以下两种类型：1 “粘合”框架是通过组装（粘合在一起）几个第三方组件来构建的。2通过创建专门设计用于紧密集成和协同工作的组件来构建“全栈”框架。

web2py 是一个全栈框架。它的几乎所有组件都是从头开始构建的，并且旨在协同工作，但它们在完整的 web2py 框架之外也能正常工作。例如，数据库抽象层 (DAL) 或模板语言可以通过导入`gluon.dal`或`gluon.template`导入您自己的 Python 应用程序而独立于 web2py 框架使用。`gluon`是包含系统库的 web2py 模块的名称。一些 web2py 库，例如从数据库表构建和处理表单，依赖于 web2py 的其他部分。web2py 也可以与第三方 Python 库一起使用，包括其他模板语言和 DAL，但它们不会像原始组件那样紧密集成。



### 模型-视图-控制器

web2py 鼓励开发人员将模型、视图、控制器分开。让我们再次考虑前面的例子，看看如何围绕它构建一个 web2py 应用程序。下面是一个 web2py MVC 编辑界面的例子：



![图片](http://web2py.com/books/default/image/29/en0.png)



下图描述了 web2py 中请求的典型工作流程：



![图片](http://web2py.com/books/default/image/29/en100.png)



在图中：

- 服务器可以是 web2py 内置的 web 服务器，也可以是第三方服务器，例如 Apache。服务器处理多线程。
- “main”是主要的 WSGI 应用程序。它执行所有常见任务并包装用户应用程序。它处理 cookie、会话、事务、URL 路由和反向路由以及调度。

如果 Web 服务器还没有这样做，它可以提供和流式传输静态文件。

- 模型、视图和控制器组件构成了用户应用程序。
- 多个应用程序可以托管在同一个 web2py 实例中。
- 虚线箭头表示与数据库引擎的通信。数据库查询可以用原始 SQL 编写（不鼓励）或使用 web2py 数据库抽象层（推荐），这样 web2py 应用程序代码不依赖于特定的数据库引擎。
- 调度程序将请求的 URL 映射到控制器中的函数调用。函数的输出可以是字符串或符号字典（哈希表）。字典中的数据由视图呈现。如果访问者请求 HTML 页面（默认设置），则字典将呈现为 HTML 页面。如果访问者在 XML 中请求相同的页面，web2py 会尝试找到可以在 XML 中呈现字典的视图。开发人员可以创建视图以使用任何已支持的协议（HTML、XML、JSON、RSS、CSV 和 RTF）或其他自定义协议呈现页面。
- 所有调用都被包装到一个事务中，任何未捕获的异常都会导致事务回滚。如果请求成功，则提交事务。
- web2py 还自动处理会话和会话 cookie，并且当事务提交时，会话也被存储，除非另有说明。
- 可以注册循环任务（通过 cron）以在预定时间和/或在完成某些操作后运行。通过这种方式，可以在后台运行长时间且计算密集型任务，而不会减慢导航速度。

这是一个最小且完整的 MVC 应用程序，由三个文件组成：

**“db.py”是模型：**一个非常简单的 web2py 应用程序将定义一个数据库连接，如下所示：

```
db = DAL('sqlite://storage.sqlite')
db.define_table('contact',
   Field('name'),
   Field('phone'))
```

它连接到数据库（在本例中是存储在`storage.sqlite`文件中的 SQLite 数据库）并定义了一个名为`contact`. 如果该表不存在，web2py 会创建它，并在后台透明地为所使用的特定数据库引擎以适当的 SQL 方言生成 SQL 代码。如果将默认为 SQLite 的数据库后端替换为 MySQL、PostgreSQL、MSSQL、FireBird、Oracle、DB2、Informix、Interbase、Ingres 和Google App Engine（SQL 和 NoSQL）。

请注意，现代版本的 web2py 将特定的数据库连接留给存储在应用程序私有目录中的配置文本文件。这使得将应用程序从开发环境部署到生产服务器变得更加容易，因为几乎可以肯定存在不同的数据库连接。所以，而不是

```
db  =  DAL ( 'sqlite://storage.sqlite' )
```

你会看到这样的代码：

```
## app configuration made easy. Look inside private/appconfig.ini
from gluon.contrib.appconfig import AppConfig
## once in production, remove reload=True to gain full speed
myconf = AppConfig(reload=True)
...
db = DAL(myconf.take('db.uri'), pool_size=myconf.take('db.pool_size', cast=int), check_reserved=['all'])

     
```

如果您查看配置文本文件`private/appconfig.ini`，您会发现它仍然默认为 sqlite。

定义和创建表后，web2py 还会生成一个功能齐全的基于 Web 的数据库管理界面，称为**appadmin**，以访问数据库和表。

**“default.py”是控制器：**

```
def contacts():
    grid=SQLFORM.grid(db.contact, user_signature=False)
    return locals()
     
```

在 web2py 中，URL 被映射到 Python 模块和函数调用。在这种情况下，控制器包含一个名为`contacts`. 一个动作可能会返回一个字符串（返回的网页）或一个 Python 字典（一组`key:value`对）或一组局部变量（如本例所示）。如果函数返回一个字典，它会被传递给与控制器/函数同名的视图，然后再渲染页面。在此示例中，该函数`contacts`为表生成一个选择/搜索/创建/更新/删除网格`db.contact`并将该网格返回到视图。

**“default/contacts.html”是视图：**

```
{{extend 'layout.html'}}
<h1>Manage My Contacts</h1>
{{=grid}}
```

在执行关联的控制器功能（动作）后，此视图由 web2py 自动调用。此视图的目的是将返回的字典（在我们的例子中`grid`）中的变量呈现为 HTML。视图文件是用 HTML 编写的，但它嵌入了由特殊`{{`和`}}`分隔符分隔的 Python 代码。这与 PHP 代码示例完全不同，因为嵌入到 HTML 中的唯一代码是“表示层”代码。视图顶部引用的“layout.html”文件由 web2py 提供，构成所有 web2py 应用程序的基本布局。布局文件可以轻松修改或替换。



### 为什么选择 web2py

web2py 是众多 Web 应用程序框架之一，但它具有引人注目的独特功能。web2py 最初是作为一种教学工具开发的，主要动机如下：

- 用户可以轻松学习服务器端 Web 开发，而不会影响功能。出于这个原因，web2py 不需要安装和配置，没有依赖项（除了源代码分发，它需要 Python 2.7 或 3.5+ 及其标准库模块），并且通过 Web 界面公开其大部分功能，包括带有调试器和数据库接口的集成开发环境。
- web2py 从一开始就很稳定，因为它遵循自上而下的设计；即，它的 API 是在实现之前设计的。即使添加了新功能，web2py 也从未破坏向后兼容性，并且在将来添加其他功能时也不会破坏兼容性。
- web2py 主动解决了困扰许多现代 Web 应用程序的最重要的安全问题，这些问题由下面的 OWASP [ [owasp](http://web2py.com/books/default/reference/29/owasp) ]确定 。
- web2py 是轻量级的。它的核心库，包括数据库抽象层、模板语言和所有帮助程序，大小为 1.4MB。包括示例应用程序和图像在内的整个源代码为 10.4MB。
- web2py 占用空间小，速度非常快。 它使用Timothy Farrell 开发的 Rocket [ [rocket](http://web2py.com/books/default/reference/29/rocket) ] WSGI Web 服务器。它与带有 mod_wsgi 的 Apache 一样快，并且支持 SSL 和 IPv6。
- web2py 对模型、控制器和视图使用 Python 语法，但不导入模型和控制器（就像所有其他 Python 框架一样） - 而是执行它们。这意味着无需重新启动 Web 服务器（即使在生产中）也可以安装、卸载和修改应用程序，并且不同的应用程序可以共存而不会相互干扰。
- web2py 使用数据库抽象层 (DAL) 而不是对象关系映射器 (ORM)。从概念上看，这意味着不同的数据库表映射到一个`Table`类的不同实例而不是不同的类，而记录映射到一个`Row`类的实例，而不是对应表类的实例。从实际的角度来看，这意味着 SQL 语法几乎一对一地映射到 DAL 语法，并且没有像流行的 ORM 那样在底层进行复杂的元类编程，这会增加延迟。

WSGI [ [wsgi-w](http://web2py.com/books/default/reference/29/wsgi-w) ] [ [wsgi-o](http://web2py.com/books/default/reference/29/wsgi-o) ]（Web 服务器网关接口）是一种新兴的 Python 标准，用于在 Web 服务器和 Python 应用程序之间进行通信。

这是主 web2py**管理**界面的屏幕截图：



![图片](http://web2py.com/books/default/image/29/en200.png)





### 安全

开放 Web 应用程序安全项目[ [owasp](http://web2py.com/books/default/reference/29/owasp) ] (OWASP) 是一个免费且开放的全球社区，专注于提高应用程序软件的安全性。

OWASP 列出了使 Web 应用程序面临风险的十大安全问题。该列表在此处复制，并描述了 web2py 如何解决每个问题：

- “跨站脚本 (XSS)：每当应用程序获取用户提供的数据并将其发送到 Web 浏览器而不首先验证或编码该内容时，就会发生 XSS 缺陷。XSS 允许攻击者在受害者的浏览器中执行脚本，从而劫持用户会话、破坏网站，可能会引入蠕虫等。” *web2py 默认情况下会转义视图中呈现的所有变量，从而防止 XSS。*
- “注入缺陷：注入缺陷，尤其是 SQL 注入，在 Web 应用程序中很常见。当用户提供的数据作为命令或查询的一部分发送到解释器时，就会发生注入。攻击者的恶意数据会诱使解释器执行意外命令或更改数据。” *web2py 包含一个数据库抽象层，它使 SQL 注入成为不可能。通常，SQL 语句不是由开发人员编写的。相反，SQL 由 DAL 动态生成，确保所有插入的数据都被正确转义。*
- “恶意文件执行：易受远程文件包含 (RFI) 影响的代码允许攻击者包含恶意代码和数据，从而导致破坏性攻击，例如整个服务器受损。” *web2py 只允许执行暴露的函数，防止恶意文件执行。导入的函数永远不会暴露；只暴露动作。web2py 使用基于 Web 的管理界面，这使得跟踪公开的内容和未公开的内容变得非常容易。*
- “不安全的直接对象引用：当开发人员公开对内部实现对象（例如文件、目录、数据库记录或密钥）的引用作为 URL 或表单参数时，就会发生直接对象引用。攻击者可以操纵这些引用来访问未经授权的其他物品。” *web2py 不暴露任何内部对象；此外，web2py 会验证所有 URL，从而防止目录遍历攻击。web2py 还提供了一种简单的机制来创建自动验证所有输入值的表单。*
- “跨站请求伪造 (CSRF)：CSRF 攻击迫使登录的受害者的浏览器向易受攻击的 Web 应用程序发送预认证请求，然后迫使受害者的浏览器执行恶意操作以使攻击者受益。 CSRF 可以像它所攻击的 Web 应用程序一样强大。” *web2py 通过为每个表单分配一次性随机令牌来防止 CSRF 以及意外重复提交表单。此外，web2py 使用 UUID 作为会话 cookie。*
- “信息泄漏和错误处理不当：应用程序可能会无意中泄漏有关其配置、内部工作的信息，或通过各种应用程序问题侵犯隐私。攻击者利用此弱点窃取敏感数据，或进行更严重的攻击。” *web2py 包括一个票务系统。任何错误都不会导致代码暴露给用户。所有错误都会被记录下来，并向用户发出允许错误跟踪的票证。但是只有管理员才能访问错误和源代码。*
- “损坏的身份验证和会话管理：帐户凭据和会话令牌通常没有得到适当的保护。攻击者会破坏密码、密钥或身份验证令牌来冒充其他用户的身份。” *web2py 为管理员身份验证提供了内置机制，它为每个应用程序独立管理会话。当客户端不是“localhost”时，管理界面还强制使用安全会话 cookie。对于应用程序，它包括一个强大的基于角色的访问控制 API。*
- “不安全的加密存储：Web 应用程序很少正确使用加密功能来保护数据和凭据。攻击者使用受保护较弱的数据来进行身份盗窃和其他犯罪，例如信用卡欺诈。” *web2py 使用 MD5 或 HMAC+SHA-512 哈希算法来保护存储的密码。其他算法也可用。*
- “不安全的通信：当需要保护敏感通信时，应用程序经常无法加密网络流量。” *web2py 包括启用 SSL 的[ [ssl](http://web2py.com/books/default/reference/29/ssl) ] Rocket WSGI 服务器，但它也可以使用 Apache 或 Lighttpd 和 mod_ssl 来提供 SSL 通信加密。*
- “未能限制 URL 访问：通常，应用程序仅通过阻止向未经授权的用户显示链接或 URL 来保护敏感功能。攻击者可以利用此弱点通过直接访问这些 URL 来访问和执行未经授权的操作。”*web2py 将 URL 请求映射到 Python 模块和函数。web2py 提供了一种机制来声明哪些功能是公共的，哪些需要身份验证和授权。包含的基于角色的访问控制 API 允许开发人员根据登录、组成员资格或基于组的权限限制对任何功能的访问。权限非常精细，可以与数据库过滤器结合使用，例如，授予对特定表和/或记录的访问权限。web2py 还允许对 URL 进行数字签名，并提供 API 来对 Ajax 回调进行数字签名。*

web2py 已经过安全审查，您可以在参考文献中找到审查结。



### 开箱即用

您可以从官网下载 web2py：

```
http://www.web2py.com
```

web2py 由以下组件组成：

- **库**：提供 web2py 的核心功能并且可以通过编程方式访问。
- **网络服务器**：Rocket WSGI 网络服务器。
- **admin**应用程序：用于创建、设计和管理其他 web2py 应用程序。**admin**为构建 web2py 应用程序提供了一个完整的基于 Web 的集成开发环境 (IDE)。它还包括其他功能，例如基于 Web 的测试和基于 Web 的 shell。
- **示例**应用程序：包含文档和交互式示例。**示例**是官方 web2py.com 网站的克隆，包括 epydoc 文档。
- **欢迎**应用程序：任何其他应用程序的基本脚手架模板。默认情况下，它包括一个纯 CSS 级联菜单和用户身份验证（在第 9 章中讨论）。

web2py 以源代码和二进制形式分发，用于 Microsoft Windows 和 Mac OS X。

源代码分发可用于任何运行 Python 并包含上述组件的平台。要运行源代码，您需要在系统上预装 Python 2.7 或 Python 3.5+。您还需要安装支持的数据库引擎之一。对于测试和轻需求应用程序，您可以使用 Python 附带的 SQLite 数据库。

web2py 的二进制版本（仅适用于 Windows 和 Mac OS X）包括 Python 2.7 解释器和 SQLite 数据库。从技术上讲，这两个不是 web2py 的组件。将它们包含在二进制发行版中使您能够开箱即用地运行 web2py。

下图描述了整个 web2py 结构：



![图片](http://web2py.com/books/default/image/29/en300.png)



在底部我们找到解释器。向上移动，我们会找到 Web 服务器（rocket）、库和应用程序。每个应用程序都包含自己的 MVC 设计（模型、控制器、视图、模块、语言、数据库和静态文件）。每个应用程序都包含自己的数据库管理代码 (appadmin)。每个 web2py 实例都附带三个应用程序：welcome（脚手架应用程序）、admin（基于 Web 的 IDE）和示例（网站和示例的副本）。



### 使用 Python 2 与 Python 3 运行 web2py

web2py 诞生时，只有 Python 2.5 版本可用（实际上，Python 3.0 是在 2008 年 12 月发布的）。这花了将近 10 年的时间，但在 2017 年 web2py 终于兼容 Python 2.7 和 Python 3.5+。较旧的 Python 2.6 已弃用，并且最近的版本不再支持。

Pyhon 3 的兼容性确实是一个巨大的里程碑（出于许多原因），但它需要仔细检查和重写大部分框架。长期以来，所有必需的外部库的兼容性需求都是一个缺失的要求。

对于新项目，我们建议您以源代码形式使用最新版本的 web2py，并使用 Pyton 3 运行它。

对于现有项目，您应该仔细评估要做什么：继续使用 Python 2 或开始将应用程序转换为 Python 3。注意：使用 Python 2 创建的 web2py 应用程序需要在 Python 2 下运行 web2py，使用 Python 3 创建的应用程序需要web2py 运行 Python 3。它们不能混合使用。



### 关于本书

本书除引言外，还包括以下章节：

- [第 2 章](http://web2py.com/books/default/chapter/29/02)是对 Python 的极简介绍。它假定您了解过程和面向对象的编程概念，例如循环、条件、函数调用和类，并涵盖基本的 Python 语法。它还涵盖了整本书中使用的 Python 模块的示例。如果你已经了解 Python，你可以跳过第 2 章。
- [第 3 章](http://web2py.com/books/default/chapter/29/03)展示了如何启动 web2py，讨论了管理界面，并通过各种复杂性不断增加的示例引导读者：一个返回字符串的应用程序、一个计数器应用程序、一个图像博客，以及一个允许图像上传和评论，提供身份验证、授权、Web 服务和 RSS 提要。阅读本章时，您可能需要参考第 2 章了解通用 Python 语法，并参考以下章节了解有关所使用函数的更详细参考。
- [第 4 章](http://web2py.com/books/default/chapter/29/04)更系统地介绍了核心结构和库：URL 映射、请求、响应、会话、缓存、调度程序、cron、国际化和一般工作流。
- [第 5 章](http://web2py.com/books/default/chapter/29/05)是用于构建视图的模板语言的参考。它展示了如何将 Python 代码嵌入 HTML，并演示了帮助程序（可以生成 HTML 的对象）的使用。
- [第 6 章](http://web2py.com/books/default/chapter/29/06)介绍了数据库抽象层或 DAL。DAL 的语法通过一系列示例进行介绍。
- [第 7 章](http://web2py.com/books/default/chapter/29/07)涵盖表单、表单验证和表单处理。FORM 是表单构建的低级助手。SQLFORM 是高级表单构建器。在第 7 章中，我们还将讨论创建/读取/更新/删除 (CRUD) API。
- [第 8 章](http://web2py.com/books/default/chapter/29/08)介绍了检索和发送电子邮件和 SMS 等通信功能。
- [第 9 章](http://web2py.com/books/default/chapter/29/09)介绍了 web2py 中可用的身份验证、授权和可扩展的基于角色的访问控制机制。此处还讨论了邮件配置和验证码，因为它们用于身份验证。在本书的第三版中，我们增加了与第三方身份验证机制（如 OpenID、OAuth、Google、Facebook、LinkedIn 等）集成的广泛介绍。
- [第 10 章](http://web2py.com/books/default/chapter/29/10)是关于在 web2py 中创建 Web 服务。我们提供了通过 Pyjamas 与 Google Web Toolkit 集成以及通过 PyAMF 与 Adobe Flash 集成的示例。
- [第 11 章](http://web2py.com/books/default/chapter/29/11)是关于 web2py 和 jQuery 配方的。web2py 主要是为服务器端编程而设计的，但它包含 jQuery，因为我们发现它是可用于效果和 Ajax 的最佳开源 JavaScript 库。在本章中，我们将讨论如何在 web2py 中有效地使用 jQuery。
- [第 12 章](http://web2py.com/books/default/chapter/29/12)讨论 web2py 组件和插件作为构建模块化应用程序的一种方式。我们提供了一个插件示例，该插件实现了许多常用功能，例如图表、评论和标记。
- [第 13 章](http://web2py.com/books/default/chapter/29/13)是关于 web2py 应用程序的生产部署。我们专门讨论了 LAMP Web 服务器上的部署（我们认为这是主要的部署替代方案）。我们讨论替代 Web 服务器和 PostgreSQL 数据库的配置。我们讨论了在 Microsoft Windows 环境中作为服务运行，以及在某些特定平台上的部署，包括 Google Applications Engine、Heroku 和 PythonAnywhere。在本章中，我们还将讨论安全性和可扩展性问题。
- [第 14 章](http://web2py.com/books/default/chapter/29/14)包含解决特定任务的各种其他方法，包括升级、地理编码、分页、Twitter API 等。
- [第 15 章](http://web2py.com/books/default/chapter/29/15)包含有关帮助和为项目做出贡献的信息，主题包括制作错误报告和对代码进行更改。

本书仅涵盖基本的 web2py 功能和 web2py 附带的 API。本书不涉及 web2py 应用程序（即现成的应用程序）。

您可以从相应的网站[[appliance](http://web2py.com/books/default/reference/29/appliances)]下载 web2py 应用。

您可以在用户组[ [usergroup](http://web2py.com/books/default/reference/29/usergroup) ]上找到讨论的其他主题。还有 AlterEgo [ [alterego](http://web2py.com/books/default/reference/29/alterego) ]，旧的 web2py 博客和常见问题解答。

本书使用 MARKMIN 语法（见[第 5 章](http://web2py.com/books/default/chapter/29/05#markmin_syntax)）编写，并自动转换为 HTML、LaTeX 和 PDF。



### 支持

主要支持渠道是用户组[ [usergroup](http://web2py.com/books/default/reference/29/usergroup) ]，每天有几十个帖子。即使您是新手，也不要犹豫——我们很乐意为您提供帮助。https://github.com/web2py/web2py/issues上还有一个正式的问题跟踪系统。最后但并非最不重要的一点是，您可以获得专业支持（有关详细信息，请参阅网站）。



### 贡献

非常感谢任何帮助。您可以帮助用户组中的其他用户，或者直接提交程序上的补丁（在 GitHub 站点https://github.com/web2py/web2py）。即使您在本书中发现错字或对其进行了改进，最好的帮助方法是修补本书本身（位于[https://github.com/web2py/web2py存储库的源文件夹下-书](https://github.com/web2py/web2py-book)）。有关贡献的更多信息，请参阅[第 15 章](http://web2py.com/books/default/chapter/29/15)。





### 风格元素

PEP8 [ [style](http://web2py.com/books/default/reference/29/style) ] 包含使用 Python 编程时的良好风格实践。你会发现 web2py 并不总是遵循这些规则。这不是因为疏忽或疏忽；我们相信 web2py 的用户应该遵守这些规则，我们鼓励这样做。在定义 web2py 辅助对象时，我们选择不遵循其中的一些规则，以尽量减少与用户定义的对象发生名称冲突的可能性。

例如，表示 a 的类`<div>`被称为`DIV`，而根据 Python 样式引用，它应该被称为`Div`。我们认为，对于这个特定示例，使用全大写“DIV”是更自然的选择。此外，这种方法使程序员可以自由地创建一个名为“Div”的类，如果他们选择这样做的话。我们的语法也自然地映射到大多数浏览器（例如，Firefox）的 DOM 符号中。

根据 Python 风格指南，全大写字符串应该用于常量而不是变量。继续我们的示例，即使考虑到这`DIV`是一个类，它也是一个特殊的类，用户永远不应修改它，因为这样做会破坏其他 web2py 应用程序。因此，我们认为这将`DIV`类限定为应该被视为常量的东西，进一步证明了我们选择符号的合理性。

总之，遵循以下约定：

- 由于上述原因，HTML 帮助器和验证器都是大写的（例如`DIV`, `A`, `FORM`, `URL`）。
- 翻译器对象`T`是大写的，尽管它是类的实例而不是类本身。从逻辑上讲，翻译器对象执行类似于 HTML 助手的操作，它会影响呈现部分的呈现。此外，`T`需要在代码中易于定位，并且必须有一个短名称。
- DAL 类遵循 Python 样式指南（首字母大写），例如`Table`, `Field`, `Query`, `Row`,`Rows`等。

在所有其他情况下，我们相信我们已尽可能遵循 Python 样式指南 (PEP8)。例如，所有实例对象都是小写的（请求、响应、会话、缓存），并且所有内部类都是大写的。

在本书的所有示例中，web2py 关键字以粗体显示，而字符串和注释以斜体显示。



### 开源协议

web2py 在 LGPL 版本 3 许可下获得许可。许可证的全文可在参考文献中找到。[ [lgpl3](http://web2py.com/books/default/reference/29/lgpl3) ]。

根据 LGPL，您可以：

- 使用您的应用程序重新分发 web2py（包括官方 web2py 二进制版本）
- 在您希望的任何许可下发布使用官方 web2py 库的应用程序

然而你必须：

- 在文档中明确说明您的应用程序使用 web2py
- 在 LGPLv3 许可下发布对 web2py 库的任何修改

许可证包括通常的免责声明：

在适用法律允许的范围内，本程序不提供任何保证。除非另有说明，否则版权所有者和/或其他方按“原样”提供程序，不提供任何形式的明示或默示保证，包括但不限于对适销性和特定用途适用性的默示保证. 有关程序质量和性能的全部风险由您承担。如果该程序被证明有缺陷，您将承担所有必要的服务、修理或更正的费用。

在任何情况下，除非适用法律要求或书面同意，任何版权持有人或任何其他按照上述许可修改和/或传达程序的方对您造成的损害（包括任何一般性、特殊性、偶然性或后果性）概不负责因使用或无法使用本程序而造成的损害（包括但不限于数据丢失或数据不准确或由您或第三方造成的丢失，或本程序无法与任何其他程序一起运行），即使在以下情况下此类持有人或其他方已被告知此类损害的可能性。

**早期版本**

web2py 的早期版本 1.0.*-1.90.* 是在 GPL2 许可下发布的，加上一个商业例外，出于实际目的，它与当前的 LGPLv3 非常相似。

**与 web2py 一起分发的第三方软件**

web2py 包含 gluon/contrib/ 文件夹下的第三方软件和各种 JavaScript 和 CSS 文件。如文件中所述，这些文件在其原始许可证下与 web2py 一起分发。



### 致谢

web2py 最初由 Massimo Di Pierro 开发并拥有版权。第一个版本（1.0）于 2007 年 10 月发布。从那时起，它被许多用户采用，其中一些用户还贡献了本书的错误报告、测试、调试、补丁和校对。

一些主要的开发人员和贡献者按名字的字母顺序排列：

戴恩·赖特、丹尼·摩根、丹尼尔·冈兹、丹尼尔·哈格、丹尼尔·林、戴夫·斯托尔、大卫·阿德利、大卫·哈里森、大卫·林、大卫·马尔科、大卫·瓦格纳、丹尼斯·伦吉尔、迪亚兹·路易斯、德克·克劳斯、多米尼克·科尼格、道格·沃伦、道格拉斯·菲利普斯, Douglas Soares de Andrade, Douglas and Alan, Dustin Bensing, Elcio Ferreira, Eric Vicenti, Erwin Olario, Falko Krause, Farsheed Ashouri, Felipe Meirelles, Flavien Scheurer, Fran Boon, Francisco Gama, Fred Yanowski, Friedrich Weber, Gabriele Alberti, Gergely Kontra, Gergely Peli, Gerley Kontra, Gilson Filho, Glenn Caltech, Graham Dumpleton, Gregory Benjamin, Gustavo Di Pietro, Gyuris Szabolcs, Hamdy Abdel-Badeea, Hans C. v. Stockhausen, Hans Donner, Hans Murx, Haiyu Wang, Ian Reinhart Geiser, Iceberg, Igor Gassko, Igor Moskvitin, Ismael Serratos, Jan Beilicke, Jay Kelkar, Jeff Bauer, Jesus Matrinez, Jim Karsten,

我确定我忘记了某人，所以我道歉。

我特别感谢 Anthony、Simone、Richard、Jonathan、Mariano、Bruno、Vladyslav、Martin、Nathan、Thadeus、Tim、Iceberg、Denes、Hans、Christian、Fran 和 Patrick 对 web2py 和 Anthony、Alvaro、Brian、Bruno 的重大贡献, Denes, Dane Denny, Erwin, Felipe, Graham, Jonathan, Hans, Kyle, Mark, Margaret, Michele, Nico, Richard, Roberto, Robin, Roman, Scott, Shane, Sharriff, Sriram, Sterling, Stuart, Thadeus, Wen (和其他）用于校对本书的各种版本。他们的贡献是无价的。如果您在本书中发现任何错误，那完全是我的错，可能是在最后一刻编辑时引入的。我还要感谢 Wiley Custom Learning Solutions 的 Ryan Steffen 帮助我出版了本书的第一版。

web2py 包含来自以下作者的代码，我要感谢他们：

Guido van Rossum for Python [ [python](http://web2py.com/books/default/reference/29/python) ]，Peter Hunt，Richard Gordon，Timothy Farrell for the Rocket [ [rocket](http://web2py.com/books/default/reference/29/rocket) ] web server，Christopher Dolivet for EditArea [ [editarea](http://web2py.com/books/default/reference/29/editarea) ]，Bob Ippolito for simplejson [ [simplejson](http://web2py.com/books/default/reference/29/simplejson) ]，Simon Cusack 和 Grant Edwards for pyRTF [ [pyrtf](http://web2py.com/books/default/reference/29/pyrtf) ] , Dalke Scientific Software for pyRSS2Gen [ [pyrss2gen](http://web2py.com/books/default/reference/29/pyrss2gen) ] , Mark Pilgrim for feedparser [ [feedparser](http://web2py.com/books/default/reference/29/feedparser) ] , Trent Mick for markdown2 [ [markdown2](http://web2py.com/books/default/reference/29/markdown2) ], Allan Saddi for fcgi.py, Evan Martin for the Python memcache module [ [memcache](http://web2py.com/books/default/reference/29/memcache) ] , John Resig for jQuery [ [jquery](http://web2py.com/books/default/reference/29/jquery) ]。

我感谢 Helmut Epp（德保罗大学教务长）、David Miller（德保罗大学计算与数字媒体学院院长）和 Estia Eichten（MetaCryption LLC 成员）一直以来的信任和支持。

最后，我要感谢我的妻子 Claudia 和儿子 Marco，感谢他们在我开发 web2py、与用户和合作者交换电子邮件以及编写本书的许多时间里对我的支持。这本书是献给他们的。