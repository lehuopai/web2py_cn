## Overview[¶](http://web2py.com/books/default/chapter/29/03/overview#Overview)



### 启动





web2py 提供适用于 Windows 和 Mac OS X 的二进制包。它们包含 Python 2.7 解释器，因此您无需预先安装它。还有一个可以在 Windows、Mac、Linux 和其他 Unix 系统上运行的源代码版本。源代码包假定计算机上已经安装了 Python 2.7 或 3.5（甚至更高版本）。

web2py 不需要安装。首先，解压缩下载的适用于您的特定操作系统的 zip 文件并执行相应的`web2py`文件。

在 Unix 和 Linux（源代码分发）上，运行：

```python
python web2py.py
```

在 OS X（二进制发行版）上，运行：

```
open web2py.app
```

在 Windows（二进制 web2py 发行版）上，运行：

```
web2py.exe
```

在 Windows（源 web2py 发行版）上，运行：

```
c:\Python27\python.exe web2py.py
```

> 注意，要在 Windows 上从源代码运行 web2py，您必须首先从`http://sourceforge.net/projects/pywin32/`.

web2py 程序接受稍后讨论的各种命令行选项。

默认情况下，在启动时，web2py 会显示一个启动窗口，然后显示一个 GUI 小部件，要求您选择一次性管理员密码、用于 Web 服务器的网络接口的 IP 地址以及端口号。服务请求。默认情况下，web2py 在 127.0.0.1:8000（本地主机上的端口 8000）上运行其 Web 服务器，但您可以在任何可用的 IP 地址和端口上运行它。`ipconfig`您可以通过打开命令行并在 Windows 或`ifconfig`OS X 和 Linux 上键入来查询网络接口的 IP 地址。从现在开始，我们假设 web2py 在 localhost (127.0.0.1:8000) 上运行。使用 0.0.0.0:80 在您的任何网络接口上公开运行 web2py。



![图片](http://www.web2py.com/books/default/image/29/en400.png)



如果您不提供管理员密码，管理界面将被禁用。这是一种防止公开暴露管理界面的安全措施。

管理界面**admin**只能从 localhost 访问，除非您使用 mod_proxy 在 Apache 后面运行 web2py。如果**管理员**检测到代理，会话 cookie 设置为安全，**管理员**登录不起作用，除非客户端和代理之间的**通信通过 HTTPS**；这是一项安全措施。**客户端和管理员**之间的所有通信必须始终是本地的或加密的；否则攻击者将能够执行中间人攻击或重放攻击并在服务器上执行任意代码。

设置管理密码后，web2py 在页面启动 Web 浏览器：

```
http://127.0.0.1:8000/
```

如果计算机没有默认浏览器，请打开 Web 浏览器并输入 URL。



![图片](http://www.web2py.com/books/default/image/29/en500.png)



单击“管理界面”将带您进入管理界面的登录页面。



![图片](http://www.web2py.com/books/default/image/29/en600.png)



管理员密码是您在启动时设置的密码。请注意，只有一个管理员，因此只有一个管理员密码。出于安全原因，除非指定了 <recycle> 选项，否则每次 web2py 启动时都会要求开发人员选择一个新密码。这与 web2py 应用程序中的身份验证机制不同。

管理员登录 web2py 后，浏览器被重定向到“站点”页面。



![图片](http://www.web2py.com/books/default/image/29/en700.png)



此页面列出了所有已安装的 web2py 应用程序并允许管理员对其进行管理。web2py 带有三个应用程序：



- 一个**管理**应用程序，您现在正在使用的那个。
- 一个**示例**应用程序，带有在线交互文档和 web2py 官方网站的副本。
- 一个**欢迎**的应用程序。这是任何其他 web2py 应用程序的基本模板。它被称为*脚手架应用程序*。这也是在启动时欢迎用户的应用程序。

即用型 web2py 应用程序称为 web2py的*appliances*。您可以从[[appliances](http://web2py.com/books/default/reference/29/appliances)]下载许多免费可用的appliances。鼓励 web2py 用户以开源或闭源（编译和打包）形式提交新appliances。

从**管理**应用程序的*站点*页面，您可以执行以下操作：

- 通过填写页面右下角的表格来**安装应用程序。**为应用命名，选择包含打包应用的文件或应用所在的URL，点击“提交”。
- **通过单击相应的按钮卸载**应用程序。有一个确认页面。
- 通过选择一个名称并单击“创建”来**创建一个新应用程序。**
- 通过单击相应的按钮**打包应用程序以进行分发。**下载的应用程序是一个 tar 文件，其中包含所有内容，包括数据库。你不应该解压这个文件；**使用admin**安装时，它会被 web2py 自动解包。
- **清理**应用程序的临时文件，例如会话、错误和缓存文件。
- **启用/禁用**每个应用程序。当应用程序被禁用时，它不能被远程调用，但它不会从 localhost 被禁用。这意味着仍然可以通过代理访问禁用的应用程序。通过在应用程序文件夹中创建一个名为“DISABLED”的文件来禁用应用程序。尝试访问已禁用应用程序的用户将收到 503 HTTP 错误。您可以使用 routes_onerror 来自定义错误页面。
- **编辑**应用程序。

> 当您使用**admin**创建一个新应用程序时，它作为“welcome”脚手架应用程序的克隆启动，该应用程序带有一个“models/db.py”，它创建一个 SQLite 数据库，连接到它，实例化 Auth、Crud 和 Service，并配置他们。它还提供了一个“controller/default.py”，它公开了操作“index”、“download”、“user”用于用户管理和“call”用于服务。在下文中，我们假设这些文件已被删除；我们将从头开始创建应用程序。

web2py 还带有一个**向导**，本章稍后将介绍，它可以根据网络上可用的布局和插件以及模型的高级描述为您编写替代的脚手架代码。



### 简单的例子



#### Say hello

在这里，作为示例，我们创建一个简单的 Web 应用程序，向用户显示消息“Hello from MyApp”。我们将此应用程序称为“myapp”。我们还将添加一个计数器，用于计算同一用户访问该页面的次数。

**您只需在admin****站点**页面右上角的表单中输入其名称即可创建新应用程序。



![图片](http://www.web2py.com/books/default/image/29/en800.png)



按 [create] 后，应用程序将创建为内置欢迎应用程序的副本。



![图片](http://www.web2py.com/books/default/image/29/en900.png)



要运行新应用程序，请访问：

```
http://127.0.0.1:8000/myapp
```

现在您有了一份欢迎应用程序的副本。

要编辑应用程序，请单击新创建的应用程序的*编辑*按钮。

**编辑**页面告诉您应用程序内部的内容。每个 web2py 应用程序都包含某些文件，其中大部分属于以下六类之一：

- **模型**：描述数据表示。
- **控制器**：描述应用程序逻辑和工作流程。
- **视图**：描述数据呈现。
- **语言**：描述如何将应用程序演示文稿翻译成其他语言。
- **modules**：属于应用程序的 Python 模块。
- **静态文件**：静态图片、CSS 文件[ [css-w,css-o,css-school](http://web2py.com/books/default/reference/29/css-w%2Ccss-o%2Ccss-school) ]、JavaScript 文件[ [js-w,js-b](http://web2py.com/books/default/reference/29/js-w%2Cjs-b) ]等。
- **plugins**：设计用于协同工作的文件组。

一切都按照模型-视图-控制器设计模式整齐地组织起来。*编辑*页面中的每个部分对应于应用程序文件夹中的一个子文件夹。

请注意，单击部分标题将切换其内容。静态文件下的文件夹名称也是可折叠的。

> 本节中列出的每个文件对应于物理上位于子文件夹中的文件。**通过管理**界面对文件执行的任何操作（创建、编辑、删除）都可以使用您喜欢的编辑器直接从 shell 执行。

应用程序包含其他类型的文件（数据库、会话文件、错误文件等），但由于不是管理员创建或修改的，因此未在*编辑页面上列出；*它们由应用程序本身创建和修改。

控制器包含应用程序的逻辑和工作流程。每个 URL 都被映射到对控制器（动作）中的一个函数的调用。有两个默认控制器：“appadmin.py”和“default.py”。**appadmin**提供数据库管理界面；我们现在不需要它。“default.py”是您需要编辑的控制器，当 URL 中没有指定控制器时，默认调用该控制器。编辑“索引”函数如下：

```python
def index():
    return "Hello from MyApp"
```

这是在线编辑器的外观：



![图片](http://www.web2py.com/books/default/image/29/en1000.png)



保存并返回*编辑*页面。单击索引链接以访问新创建的页面。

当您访问网址时

```
http://127.0.0.1:8000/myapp/default/index
```

调用 myapp 应用程序的默认控制器中的 index 操作。它返回浏览器为我们显示的字符串。它应该如下所示：



![图片](http://www.web2py.com/books/default/image/29/en1100.png)



现在，编辑“索引”函数如下：

```python
def index():
    return dict(message="Hello from MyApp")
```

同样在*编辑*页面中，编辑视图“default/index.html”（与操作关联的视图文件）并将该文件的现有内容完全替换为以下内容：

```html
<html>
   <head></head>
   <body>
      <h1>{{=message}}</h1>
   </body>
</html>
```

现在该操作返回一个字典`message`。当一个动作返回一个字典时，web2py 会寻找一个名称为

```
[controller]/[function].[extension]
```

并执行它。这`[extension]`是请求的扩展名。如果未指定扩展名，则默认为“html”，这就是我们将在这里假设的内容。在这种假设下，视图是一个 HTML 文件，它使用特殊的 {{ }} 标签嵌入了 Python 代码。特别是，在示例中，`{{=message}}`指示 web2py 将标记代码替换为`message`操作返回的值。请注意，`message`这里不是 web2py 关键字，而是在操作中定义的。到目前为止，我们还没有使用任何 web2py 关键字。

如果 web2py 没有找到请求的视图，它会使用每个应用程序附带的“generic.html”视图。

> 如果指定了“html”以外的扩展名（例如“json”），并且没有找到视图文件“[controller]/[function].json”，web2py 会查找视图“generic.json”。web2py 附带 generic.html、generic.json、generic.jsonp、generic.xml、generic.rss、generic.ics（用于 Mac 邮件日历）、generic.map（用于嵌入 Google 地图）和 generic.pdf（基于fpdf)。这些通用视图可以为每个应用程序单独修改，并且可以轻松添加其他视图。

> 通用视图是一种开发工具。在生产中，每个动作都应该有自己的观点。事实上，默认情况下，通用视图只能从 localhost 启用。

> 您还可以指定一个视图`response.view = 'default/something.html'`

在第 10 章中阅读有关此主题的更多信息。

如果您返回“编辑”并单击索引，您现在将看到以下 HTML 页面：



![图片](http://www.web2py.com/books/default/image/29/en1200.png)





#### 调试工具栏

出于调试目的，您可以插入

```html
{{=response.toolbar()}}
```

到视图中的代码，它将向您显示一些有用的信息，包括请求、响应和会话对象，并列出所有数据库查询及其时间。



#### 算一算

现在让我们向该页面添加一个计数器，该计数器将计算同一访问者显示该页面的次数。

web2py 使用sessions and cookies 自动且透明地跟踪访问者。对于每个新访问者，它都会创建一个会话并分配一个唯一的“session_id”。会话是存储在服务器端的变量的容器。唯一 ID 通过 cookie 发送到浏览器。当访问者从同一个应用程序请求另一个页面时，浏览器将 cookie 发回，由 web2py 检索，并恢复相应的会话。

要使用会话，请修改defult控制器：

```python
def index():
    if not session.counter:
        session.counter = 1
    else:
        session.counter += 1
    return dict(message="Hello from MyApp", counter=session.counter)
```

请注意，这`counter`不是 web2py 关键字，而是`session`。我们要求 web2py 检查会话中是否存在计数器变量，如果没有，则创建一个并将其设置为 1。如果计数器存在，我们要求 web2py 将计数器增加 1。最后我们传递值对视图的柜台。

编写相同函数的更紧凑的方法是：

```python 
def index():
    session.counter = (session.counter or 0) + 1
    return dict(message="Hello from MyApp", counter=session.counter)
```

现在修改视图以添加一行显示计数器的值：

```html
<html>
   <head></head>
   <body>
      <h1>{{=message}}</h1>
      <h2>Number of visits: {{=counter}}</h2>
   </body>
</html>
```

当您再次（一次又一次）访问索引页面时，您应该得到以下 HTML 页面：



![图片](http://www.web2py.com/books/default/image/29/en1300.png)



计数器与每个访问者相关联，并在每次访问者重新加载页面时递增。不同的访客看到不同的柜台。



#### 说我的名字





现在创建两个页面（first和second），第一个页面创建一个表单，询问访问者的姓名，然后重定向到第二个页面，该页面按名称问候访问者。



![yUML图](http://www.web2py.com/books/default/image/29/en1400.png)



在默认控制器中编写相应的动作：

```python
def first():
    return dict()

def second():
    return dict()
```

然后为第一个动作创建一个视图“default/first.html”，并输入：

```html
{{extend 'layout.html'}}
<h1>What is your name?</h1>
<form action="{{=URL('second')}}">
  <input name="visitor_name" />
  <input type="submit" />
</form>
```

最后，为第二个动作创建一个视图“default/second.html”：

```html
{{extend 'layout.html'}}
<h1>Hello {{=request.vars.visitor_name}}</h1>
```

在这两个视图中，我们都扩展了 web2py 附带的基本“layout.html”视图。布局视图使两个页面的外观和感觉保持一致。布局文件可以很容易地编辑和替换，因为它主要包含 HTML 代码。

如果您现在访问第一页，请输入您的姓名：



![图片](http://www.web2py.com/books/default/image/29/en1500.png)



并提交表格，您将收到问候：



![图片](http://www.web2py.com/books/default/image/29/en1600.png)





#### 回发





我们之前使用的表单提交机制很常见，但这并不是一个好的编程习惯。所有输入都应该被验证，在上面的例子中，验证的负担将落在第二个动作上。因此，执行验证的操作与生成表单的操作不同。这往往会导致代码冗余。

表单提交的更好模式是将表单提交给生成它们的相同操作，在我们的示例中为“第一个”。“第一个”操作应该接收变量、处理它们、将它们存储在服务器端，并将访问者重定向到检索变量的“第二个”页面。这种机制称为*回*发。



![yUML图](http://www.web2py.com/books/default/image/29/en1700.png)



修改默认控制器实现自提交：

```python
def first():
    if request.vars.visitor_name:
        session.visitor_name = request.vars.visitor_name
        redirect(URL('second'))
    return dict()

def second():
    return dict()
```

然后修改“default/first.html”视图：

```html
{{extend 'layout.html'}}
What is your name?
<form>
  <input name="visitor_name" />
  <input type="submit" />
</form>
```

并且“default/second.html”视图需要从而`session`不是从以下位置检索数据`request.vars`：

```html
{{extend 'layout.html'}}
<h1>Hello {{=session.visitor_name or "anonymous"}}</h1>
```

从访问者的角度来看，自提交的行为与之前的实现完全相同。我们还没有添加验证，但现在很明显验证应该由第一个操作执行。

这种方法更好，因为访问者的名称保留在会话中，并且可以通过应用程序中的所有操作和视图访问，而无需显式传递。

请注意，如果在设置访问者名称之前调用了“第二个”操作，它将显示“你好匿名”，因为 `session.visitor_name`返回`None`。或者，我们可以在控制器中（`second`函数内部）添加以下代码：

```python
if request.function != 'first' and not session.visitor_name:
    redirect(URL('first'))
```

这是一种*特殊* 机制，您可以使用它来强制对控制器进行授权，但请参阅第 9 章了解更强大的方法。





使用 web2py，我们可以更进一步，让 web2py 为我们生成表单，包括验证。web2py 提供了与等效的 HTML 标记同名的帮助程序helpers（FORM、INPUT、TEXTAREA 和 SELECT/OPTION）。它们可用于在控制器或视图中构建表单。

例如，这是重写第一个操作的一种可能方法：

```python
def first():
    form = FORM(INPUT(_name='visitor_name', requires=IS_NOT_EMPTY()),
                INPUT(_type='submit'))
    if form.process().accepted:
        session.visitor_name = form.vars.visitor_name
        redirect(URL('second'))
    return dict(form=form)
```

我们说 FORM 标签包含两个 INPUT 标签。输入标签的属性由以下划线开头的命名参数指定。该`requires`参数不是标签属性（因为它不以下划线开头），但它为visitor_name 的值设置了一个验证器。

这是创建相同表单的另一种更好的方法：

```python
def first():
    form = SQLFORM.factory(Field('visitor_name',
                                 label='what is your name?',
                                 requires=IS_NOT_EMPTY()))
    if form.process().accepted:
        session.visitor_name = form.vars.visitor_name
        redirect(URL('second'))
    return dict(form=form)
```

通过将`form`对象嵌入到“default/first.html”视图中，可以轻松地在 HTML 中序列化该对象。

```html
{{extend 'layout.html'}}
{{=form}}
```

该`form.process()`方法应用验证器并返回表单本身。如果表单已处理并通过验证，则该`form.accepted`变量设置为 True。如果自提交的表单通过验证，它将变量存储在会话中并像以前一样重定向。如果表单未通过验证，则会将错误消息插入表单并显示给用户，如下所示：



![图片](http://www.web2py.com/books/default/image/29/en1800.png)



在下一节中，我们将展示如何从模型中自动生成表单。

在我们所有的示例中，我们都使用会话将用户名从第一个操作传递到第二个操作。我们可以使用不同的机制并将数据作为重定向 URL 的一部分传递：

```python
def first():
    form = SQLFORM.factory(Field('visitor_name', requires=IS_NOT_EMPTY()))
    if form.process().accepted:
        name = form.vars.visitor_name
        redirect(URL('second', vars=dict(name=name)))
    return dict(form=form)

def second():
    name = request.vars.name or redirect(URL('first'))
    return dict(name=name)
```

然后修改“default/second.html”视图：

```html
{{extend 'layout.html'}}
<h1>Hello {{=name}}</h1>
```

请注意，通常使用 URL 将数据从一个操作传递到另一个操作并不是一个好主意。它使保护应用程序变得更加困难。**将数据存储在会话中更安全。**



#### 国际化

您的代码可能包含硬编码字符串，例如“你叫什么名字？”。您应该能够在不编辑代码的情况下自定义字符串，尤其是为这些字符串插入不同语言的翻译。这样，如果访问者将浏览器的语言首选项设置为“意大利语”，web2py 将使用意大利语翻译的字符串（如果可用）。web2py 的这个特性被称为“国际化”，下一章会详细介绍。

在这里，我们只是观察到，为了使用此功能，您应该标记需要翻译的字符串。这是通过在代码中包装带引号的字符串来完成的，例如

```
"What is your name?"
```

用`T`  函数：

```
T("What is your name?")
```

您还可以标记视图中硬编码的翻译字符串。例如

```
<h1>What is your name?</h1>
```

变成

```
<h1>{{=T("What is your name?")}}</h1>
```

对代码中的每个字符串（字段标签、Flash 消息等）执行此操作是一种很好的做法，表和字段名称除外。

一旦字符串被识别和标记，web2py 会处理几乎所有其他事情。管理界面还提供了一个页面，您可以在其中将每个字符串翻译成您希望支持的语言。

> web2py 包含一个强大的多语言引擎，将在下一章中介绍。它与国际化引擎和 markmin 渲染器集成在一起。



### 图片博客

在这里，作为另一个示例，我们希望创建一个 Web 应用程序，该应用程序允许管理员发布图像并为其命名，并允许网站的访问者查看命名的图像并提交评论（帖子）。

和以前一样，从**admin的****站点**页面创建一个名为 的新应用程序，然后导航到*编辑*页面：`images`



![图片](http://www.web2py.com/books/default/image/29/en1900.png)



我们首先创建一个模型，即应用程序中持久数据的表示（要上传的图像、它们的名称和评论）。首先，您需要创建/编辑一个模型文件，由于缺乏想象力，我们称之为“db.py”。我们假设下面的代码将替换“db.py”中的任何现有代码。模型和控制器必须具有`.py`扩展名，因为它们是 Python 代码。如果未提供扩展名，则由 web2py 附加。相反，视图具有`.html`扩展名，因为它们主要包含 HTML 代码。

删除模型“menu.py”。

单击相应的“编辑”按钮编辑“db.py”文件：



![图片](http://www.web2py.com/books/default/image/29/en2000.png)



并输入以下内容：

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1<br />2<br />3<br />4<br />5<br />6<br />7<br />8<br />9<br />10<br />11<br />12<br />13<br />14<br />15<br />16<br />17<br />18<br />19<br />20 | db = DAL("sqlite://storage.sqlite")<br/><br/>db.define_table('image',<br/>                Field('title', unique=True),<br/>                Field('file', 'upload'),<br/>                format = '%(title)s')<br/><br/>db.define_table('post',<br/>                Field('image_id', 'reference image'),<br/>                Field('author'),<br/>                Field('email'),<br/>                Field('body', 'text'))<br/><br/>db.image.title.requires = IS_NOT_IN_DB(db, db.image.title)<br/>db.post.image_id.requires = IS_IN_DB(db, db.image.id, '%(title)s')<br/>db.post.author.requires = IS_NOT_EMPTY()<br/>db.post.email.requires = IS_EMAIL()<br/>db.post.body.requires = IS_NOT_EMPTY()<br/><br/>db.post.image_id.writable = db.post.image_id.readable = False |

让我们逐行分析。

第 1 行定义了一个全局变量，称为`db`表示数据库连接。在这种情况下，它是与存储在文件“applications/images/databases/storage.sqlite”中的 SQLite 数据库的连接。使用 SQLite 时，如果数据库文件不存在，则创建它。您可以更改文件的名称，以及全局变量的名称`db`，但方便的是给它们起相同的名称，以便于记忆。

第 3-6 行定义了一个表格“图像”。`define_table`是`db`对象的方法。第一个参数“image”是我们正在定义的表的名称。其他参数是属于该表的字段。该表有一个名为“title”的字段，一个名为“file”的字段，以及一个名为“id”的字段，用作表的主键（“id”未明确声明，因为默认情况下所有表都有一个 id 字段）。字段“title”是一个字符串，字段“file”是“upload”类型。“上传”是 web2py 数据抽象层 (DAL) 用来存储上传文件名称的一种特殊类型的字段。web2py 知道如何上传文件（如果文件很大，则通过流传输）、安全地重命名和存储它们。

定义表后，web2py 会采取以下几种可能的操作之一：

- 如果表不存在，则创建表；
- 如果表存在且不符合定义，则表会相应更改，如果字段类型不同，web2py 会尝试转换其内容；
- 如果表存在并且对应于定义，web2py 什么也不做。

**这种行为称为“迁移”。在 web2py 中，迁移是自动的，但可以通过`migrate=False`作为`define_table`.**

第 6 行定义了表格的格式字符串。它确定记录应如何表示为字符串。请注意，`format`参数也可以是一个接受记录并返回字符串的函数。例如：

```
format=lambda row: row.title
```

第 8-12 行定义了另一个名为“post”的表。一个帖子有一个“作者”、一个“电子邮件”（我们打算存储帖子作者的电子邮件地址）、一个“文本”类型的“正文”（我们打算用它来存储由作者），以及通过“id”字段指向的类型引用的“image_id”`db.image`字段。

在第 14 行，`db.image.title`表示表“图像”的字段“标题”。该属性`requires`允许您设置将由 web2py 表单强制执行的要求/约束。这里我们要求“title”是唯一的：

```
IS_NOT_IN_DB(db, db.image.title)
```

*请注意，这是可选的，因为它是自动设置的`Field('title', unique=True)`*。

代表这些约束的对象称为验证器。多个验证器可以分组在一个列表中。验证器按照它们出现的顺序执行。`IS_NOT_IN_DB(a, b)`是一个特殊的验证器，用于检查`b`新记录的字段值是否不在`a`.

第 15 行要求表“post”的字段“image_id”在`db.image.id`. 就数据库而言，我们在定义表“post”时已经声明了这一点。现在我们明确地告诉模型，当发布新评论时，web2py 也应该在表单处理级别强制执行此条件，以便无效值不会从输入表单传播到数据库。我们还要求“image_id”由`'%(title)s'`相应记录的“title”表示。

第 20 行表示表“post”的字段“image_id”不应显示在表单中，`writable=False`甚至不应显示在只读表单中，`readable=False`.

第 17-18 行中验证器的含义应该很明显。

请注意，验证器

```python
db.post.image_id.requires = IS_IN_DB(db, db.image.id, '%(title)s')
```

如果我们为引用表指定格式，则可以省略（并且是自动的）：

```python
db.define_table('image', ..., format='%(title)s')
```

其中格式可以是字符串或接受记录并返回字符串的函数。

一旦定义了模型，如果没有错误，web2py 就会创建一个应用程序管理界面来管理数据库。您可以通过*编辑*页面中的“数据库管理”链接或直接访问它：

```
http://127.0.0.1:8000/images/appadmin
```

这是**appadmin**界面的截图：



![图片](http://www.web2py.com/books/default/image/29/en2100.png)



这个接口被编码在名为“appadmin.py”的控制器和相应的视图“appadmin.html”中。从现在开始，我们将此接口简称为**appadmin**。它允许管理员插入新的数据库记录、编辑和删除现有记录、浏览表和执行数据库连接。

第一次访问**appadmin**时，会执行模型并创建表。web2py DAL 将 Python 代码转换为特定于所选数据库后端（本示例中为 SQLite）的 SQL 语句。您可以通过单击“models”下的“sql.log”链接从*编辑*页面查看生成的 SQL 。请注意，在创建表之前链接不存在。



![图片](http://www.web2py.com/books/default/image/29/en2200.png)



如果您要编辑模型并再次访问**appadmin**，web2py 将生成 SQL 来更改现有表。生成的 SQL 记录到“sql.log”中。

现在回到**appadmin**并尝试插入新的图像记录：



![图片](http://www.web2py.com/books/default/image/29/en2300.png)



web2py 已将`db.image.file`“上传”字段翻译为文件的上传表单。提交表单并上传图像文件时，文件会以保留扩展名的安全方式重命名，并以新名称保存在应用程序“uploads”文件夹下，新名称存储在`db.image.file`字段中。此过程旨在防止目录遍历攻击。

*请注意，每个字段类型都由一个小部件*呈现。可以覆盖默认小部件。

**当您在appadmin**中单击表名时，web2py 会选择当前表上的所有记录，由 DAL 查询标识

```
db.image.id > 0
```

并渲染结果。



![图片](http://www.web2py.com/books/default/image/29/en2400.png)



您可以通过编辑 DAL 查询并按 [提交] 来选择不同的记录集。

要编辑或删除单个记录，请单击记录 ID 号。

由于`IS_IN_DB`验证器，引用字段“image_id”由下拉菜单呈现。下拉列表中的项目存储为键 ( `db.image.id`)，但由它们的 表示`db.image.title`，由验证器指定。

验证器是强大的对象，它知道如何表示字段、过滤字段值、生成错误以及格式化从字段中提取的值。

下图显示了当您提交未通过验证的表单时会发生什么：



![图片](http://www.web2py.com/books/default/image/29/en2500.png)



**由appadmin**自动生成的相同表单也可以通过`SQLFORM`帮助程序以编程方式生成并嵌入到用户应用程序中。这些表单是 CSS 友好的，并且可以自定义。

每个应用程序都有自己的**appadmin**；因此，可以修改**appadmin本身，而不会影响其他应用程序。**

到目前为止，应用程序知道如何存储数据，并且我们已经了解了如何通过**appadmin**访问数据库。**对appadmin**的访问仅限于管理员，并且不打算用作应用程序的生产 Web 界面；因此，本演练的下一部分。具体来说，我们要创建：

- 一个“索引”页面，列出了按标题排序的所有可用图像，并链接到图像的详细页面。
- 一个“显示/[id]”页面，向访问者显示请求的图像并允许访问者查看和发表评论。
- 用于下载上传图像的“下载/[名称]”操作。

这在此处示意性地表示：



![yUML图](http://www.web2py.com/books/default/image/29/en2600.png)



返回*编辑*页面并编辑“default.py”控制器，将其内容替换为以下内容：

```python
def index():
    images = db().select(db.image.ALL, orderby=db.image.title)
    return dict(images=images)
```

此操作返回一个字典。字典中项目的键被解释为传递给与操作关联的视图的变量。开发时，如果没有视图，则操作由每个 web2py 应用程序提供的“generic.html”视图呈现。

index 操作`db.image.ALL`从表图像中选择所有字段 ( )，按 排序`db.image.title`。选择的结果是一个`Rows`对象包含记录。`images`将其分配给由操作返回到视图的局部变量。`images`是可迭代的，其元素是选定的行。对于每一行，列可以作为字典访问：`images[0]['title']`或等效为`images[0].title`.

如果您不编写视图，则字典由“views/generic.html”呈现，并且对 index 操作的调用如下所示：



![图片](http://www.web2py.com/books/default/image/29/en2700.png)



您尚未为此操作创建视图，因此 web2py 以纯表格形式呈现记录集。

继续为索引操作创建视图。返回管理员，编辑“default/index.html”并将其内容替换为以下内容：

```python
{{extend 'layout.html'}}
<h1>Current Images</h1>
<ul>
{{for image in images:}}
{{=LI(A(image.title, _href=URL("show", args=image.id)))}}
{{pass}}
</ul>
```



首先要注意的是视图是带有特殊 {{...}} 标签的纯 HTML。{{...}} 中嵌入的代码是纯 Python 代码，但需要注意一点：缩进无关紧要。代码块以冒号 (:) 结尾的行开头，以关键字开头的行结尾`pass`。在某些情况下，块的结尾从上下文中是显而易见的，并且`pass`不需要使用。

第 5-7 行循环遍历图像行并为每一行图像显示：

```
LI(A(image.title, _href=URL('show', args=image.id))
```

这是一个`<li>...</li>`包含标签的标签，该`<a href="...">...</a>`标签包含`image.title`. 超文本引用（href 属性）的值为：

```
URL('show', args=image.id)
```

即，与调用“show”函数的当前请求在同一应用程序和控制器中的URL，将单个参数传递给函数，`args=image.id`. `LI`,`A`等是映射到相应 HTML 标记的 web2py 助手。它们的未命名参数被解释为要序列化并插入到标签的 innerHTML 中的对象。以下划线（例如`_href`）开头的命名参数被解释为标签属性，但没有下划线。例如`_href`是`href`属性，`_class`是`class`属性等。

例如，以下语句：

```
{{=LI(A('something', _href=URL('show', args=123))}}
```

呈现为：

```
<li><a href="/images/default/show/123">something</a></li>
```

少数助手（`INPUT`、`TEXTAREA`和）还支持一些不以下划线（、 和`OPTION`）开头的特殊命名属性。它们对于构建自定义表单很重要，稍后将讨论。`SELECT``value``requires`

回到*编辑*页面。它现在表明“default.py 公开索引”。通过点击“索引”，您可以访问新创建的页面：

```
http://127.0.0.1:8000/images/default/index
```

看起来像：



![图片](http://www.web2py.com/books/default/image/29/en2800.png)



如果您单击图像名称链接，您将被定向到：

```
http://127.0.0.1:8000/images/default/show/1
```

这会导致错误，因为您尚未在控制器“default.py”中创建名为“show”的操作。

让我们编辑“default.py”控制器并将其内容替换为：





```python
def index():
    images = db().select(db.image.ALL, orderby=db.image.title)
    return dict(images=images)

def show():
    image = db.image(request.args(0, cast=int)) or redirect(URL('index'))
    db.post.image_id.default = image.id
    form = SQLFORM(db.post)
    if form.process().accepted:
        response.flash = 'your comment is posted'
    comments = db(db.post.image_id == image.id).select(orderby=db.post.id)
    return dict(image=image, comments=comments, form=form)

def download():
    return response.download(request, db)
```

控制器包含两个新动作：“显示”和“下载”。"show" 动作选择`id`从请求参数中解析的图像以及与图像相关的所有注释。“show”然后将所有内容传递给视图“default/show.html”。

引用的图像 id：

```
URL('show', args=image.id)
```

在“default/index.html”中，可以通过以下方式访问：

```
request.args(0, cast=int)
```

来自“show”的动作。该`cast=int`参数是可选的，但非常重要。它尝试将 PATH_INFO 中传递的字符串值转换为 int。失败时，它会引发适当的异常，而不是导致票证。如果投射失败，还可以指定重定向：

```
request.args(0, cast=int, otherwise=URL('error'))
```

此外`db.image(...)`是一个快捷方式

```
db(db.image.id == ...).select().first()  
```

“下载”操作需要一个 in 的文件名`request.args(0)`，构建一个指向该文件应该所在位置的路径，并将其发送回客户端。如果文件太大，它会流式传输文件而不会产生任何内存开销。

请注意以下陈述：

- `db.post`第 7 行设置参考字段的值，由于表格模型，它不是输入表单的一部分。
- 第 8 行为表创建了一个插入表单 SQLFORM `db.post`。
- 第 9 行处理当前会话中提交的表单（提交的表单变量在 中`request.vars`）（该会话用于防止重复提交，并强制导航）。如果提交的表单变量被验证，新的评论被插入到`db.post`表格中；否则修改表单以包含错误消息（例如，如果作者的电子邮件地址无效）。这一切都在第 9 行完成！
- 第 10 行仅在表格被接受时才执行，在记录插入数据库表之后。`response.flash`是一个 web2py 变量，显示在视图中，用于通知访问者发生了什么事。
- 第 11 行选择引用当前图像的所有评论，`.select(orderby=db.post.id)`保持评论历史排序。

> “下载”操作已经在脚手架应用程序的“default.py”控制器中定义。

“下载”操作不返回字典，因此不需要视图。不过，“show”操作应该有一个视图，所以返回到**admin**并创建一个名为“default/show.html”的新视图。

编辑这个新文件并将其内容替换为以下内容：

```html
{{extend 'layout.html'}}
<h1>Image: {{=image.title}}</h1>
<div style="text-align:center">
<img width="200px"
     src="{{=URL('download', args=image.file)}}" />
</div>
{{if len(comments):}}
  <h2>Comments</h2><br /><p>
  {{for post in comments:}}
    <p>{{=post.author}} says <i>{{=post.body}}</i></p>
  {{pass}}</p>
{{else:}}
  <h2>No comments posted yet</h2>
{{pass}}
<h2>Post a comment</h2>
{{=form}}
```

此视图通过调用标签内的“下载”操作来显示**image.file**`<img ... />`。如果有评论，它会遍历它们并显示每一条。

以下是所有内容对访问者的显示方式。



![图片](http://www.web2py.com/books/default/image/29/en2900.png)



当访问者通过此页面提交评论时，评论将存储在数据库中并附加在页面底部。



#### 添加身份验证

用于基于角色的访问控制的 web2py API 非常复杂，但现在我们将限制自己对经过身份验证的用户对 show 操作的访问，将更详细的讨论推迟到第 9 章。

要限制对经过身份验证的用户的访问，我们需要完成三个步骤。在模型中，例如“db.py”，我们需要添加：

```python
from gluon.tools import Auth
auth = Auth(db)
auth.define_tables(username=True)
```

在我们的控制器中，我们需要添加一个动作：

```python
def user():
    return dict(form=auth())
```

这足以启用登录、注册、注销等页面。默认布局还将在右上角显示相应页面的选项。



![图片](http://www.web2py.com/books/default/image/29/en3000.png)



我们现在可以装饰我们想要限制的功能，例如：

```python
@auth.requires_login()
def show():
    ...
```

任何访问尝试

```
http://127.0.0.1:8000/images/default/show/[image_id]
```

将需要登录。如果用户没有登录，用户将被重定向到

```
http://127.0.0.1:8000/images/default/user/login
```



![图片](http://www.web2py.com/books/default/image/29/en3100.png)



该`user`函数还公开了以下操作：

```
http://127.0.0.1:8000/images/default/user/logout
http://127.0.0.1:8000/images/default/user/register
http://127.0.0.1:8000/images/default/user/profile
http://127.0.0.1:8000/images/default/user/change_password
http://127.0.0.1:8000/images/default/user/request_reset_password
http://127.0.0.1:8000/images/default/user/retrieve_username
http://127.0.0.1:8000/images/default/user/retrieve_password
http://127.0.0.1:8000/images/default/user/verify_email
http://127.0.0.1:8000/images/default/user/impersonate
http://127.0.0.1:8000/images/default/user/not_authorized
```

现在，第一次使用的用户需要注册才能登录并阅读或发表评论。

> 对象`auth`和`user`函数都已在脚手架应用程序中定义。该`auth`对象是高度可定制的，可以通过插件处理电子邮件验证、注册批准、验证码和备用登录方法。



#### 添加grid

我们可以使用`SQLFORM.grid`和`SQLFORM.smartgrid`这2个工具进一步改进，为我们的应用程序创建一个管理界面：

```python
@auth.requires_membership('manager')
def manage():
    grid = SQLFORM.smartgrid(db.image, linked_tables=['post'])
    return dict(grid=grid)
```

与关联的“views/default/manage.html”

```html
{{extend 'layout.html'}}
<h2>Management Interface</h2>
{{=grid}}
```

使用 appadmin 创建一个组“经理”并使一些用户成为该组的成员。他们将能够访问

```
http://127.0.0.1:8000/images/default/manage
```

并浏览、搜索：



![图片](http://www.web2py.com/books/default/image/29/en3200.png)



创建、更新和删除图像及其评论：



![图片](http://www.web2py.com/books/default/image/29/en3300.png)





#### 配置布局

您可以通过编辑“views/layout.html”来配置默认布局，但您也可以在不编辑 HTML 的情况下对其进行配置。事实上，“static/css/web2py.css”样式表在第 5 章中有详细的文档和描述。您可以更改颜色、列、大小、边框和背景，而无需编辑 HTML。如果要编辑菜单、标题或副标题，可以在任何模型文件中进行。脚手架应用程序在文件“models/menu.py”中设置这些参数的默认值：

```python
response.title = request.application
response.subtitle = 'customize me!'
response.meta.author = 'you'
response.meta.description = 'describe your app'
response.meta.keywords = 'bla bla bla'
response.menu = [ [ 'Index', False, URL('index') ] ]
```



### 一个简单的维基





在本节中，我们仅使用基本的 API 从头开始构建一个简单的 wiki（相对，使用下一节中演示的 web2py 的内置 wiki 功能更高级）。访问者将能够创建页面、搜索它们（按标题）和编辑它们。访问者还可以发表评论（与之前的应用程序完全相同），也可以发表文档（作为页面的附件）并从页面链接它们。作为惯例，我们在 wiki 语法中采用 Markmin 语法。我们还将使用 Ajax 实现搜索页面、页面的 RSS 提要以及通过 XML-RPC [ [xmlrpc](http://web2py.com/books/default/reference/29/xmlrpc) ]搜索页面的处理程序。下图列出了我们需要实施的操作以及我们打算在它们之间建立的链接。



![yUML图](http://www.web2py.com/books/default/image/29/en3400.png)



首先创建一个新的脚手架应用程序，将其命名为“mywiki”。

该模型必须包含三个表：页面、评论和文档。评论和文档参考页面都属于页面。一个文档包含一个上传类型的文件字段，就像之前的图像应用程序一样。

这是完整的模型：

```python
db = DAL('sqlite://storage.sqlite')

from gluon.tools import *
auth = Auth(db)
auth.define_tables()
crud = Crud(db)

db.define_table('page',
                Field('title'),
                Field('body', 'text'),
                Field('created_on', 'datetime', default=request.now),
                Field('created_by', 'reference auth_user', default=auth.user_id),
                format='%(title)s')

db.define_table('post',
                Field('page_id', 'reference page'),
                Field('body', 'text'),
                Field('created_on', 'datetime', default=request.now),
                Field('created_by', 'reference auth_user', default=auth.user_id))

db.define_table('document',
                Field('page_id', 'reference page'),
                Field('name'),
                Field('file', 'upload'),
                Field('created_on', 'datetime', default=request.now),
                Field('created_by', 'reference auth_user', default=auth.user_id),
                format='%(name)s')

db.page.title.requires = IS_NOT_IN_DB(db, 'page.title')
db.page.body.requires = IS_NOT_EMPTY()
db.page.created_by.readable = db.page.created_by.writable = False
db.page.created_on.readable = db.page.created_on.writable = False

db.post.body.requires = IS_NOT_EMPTY()
db.post.page_id.readable = db.post.page_id.writable = False
db.post.created_by.readable = db.post.created_by.writable = False
db.post.created_on.readable = db.post.created_on.writable = False

db.document.name.requires = IS_NOT_IN_DB(db, 'document.name')
db.document.page_id.readable = db.document.page_id.writable = False
db.document.created_by.readable = db.document.created_by.writable = False
db.document.created_on.readable = db.document.created_on.writable = False
```

编辑控制器“default.py”并创建以下操作：

- index：列出所有 wiki 页面
- create：添加一个新的 wiki 页面
- show：显示 wiki 页面及其评论，并添加新评论
- edit：编辑现有页面
- documents：管理附加到页面的文档
- download：下载文档（如图像示例中）
- search：显示一个搜索框，并通过 Ajax 回调返回所有匹配的标题作为访问者类型
- callback：Ajax 回调函数。它返回在访问者键入时嵌入到搜索页面中的 HTML。

这是“default.py”控制器：

```python

 def index():
    """ this controller returns a dictionary rendered by the view
        it lists all wiki pages
    >>> index().has_key('pages')
    True
    """
    pages = db().select(db.page.id, db.page.title, orderby=db.page.title)
    return dict(pages=pages)

@auth.requires_login()
def create():
    """creates a new empty wiki page"""
    form = SQLFORM(db.page).process(next=URL('index'))
    return dict(form=form)

def show():
    """shows a wiki page"""
    this_page = db.page(request.args(0, cast=int)) or redirect(URL('index'))
    db.post.page_id.default = this_page.id
    form = SQLFORM(db.post).process() if auth.user else None
    pagecomments = db(db.post.page_id == this_page.id).select(orderby=db.post.id)
    return dict(page=this_page, comments=pagecomments, form=form)

@auth.requires_login()
def edit():
    """edit an existing wiki page"""
    this_page = db.page(request.args(0, cast=int)) or redirect(URL('index'))
    form = SQLFORM(db.page, this_page).process(
        next = URL('show', args=request.args))
    return dict(form=form)

@auth.requires_login()
def documents():
    """browser, edit all documents attached to a certain page"""
    page = db.page(request.args(0, cast=int)) or redirect(URL('index'))
    db.document.page_id.default = page.id
    grid = SQLFORM.grid(db.document.page_id == page.id, args=[page.id])
    return dict(page=page, grid=grid)

def user():
    return dict(form=auth())

def download():
    """allows downloading of documents"""
    return response.download(request, db)

def search():
    """an ajax wiki search page"""
    return dict(form=FORM(INPUT(_id='keyword',
                                _name='keyword',
                                _onkeyup="ajax('callback', ['keyword'], 'target');")),
                target_div=DIV(_id='target'))

def callback():
    """an ajax callback that returns a <ul> of links to wiki pages"""
    query = db.page.title.contains(request.vars.keyword)
    pages = db(query).select(orderby=db.page.title)
    links = [A(p.title, _href=URL('show', args=p.id)) for p in pages]
    return UL(*links)

```

第 2-6 行构成索引操作的注释。注释中的第 4-5 行被 python 解释为测试代码（doctest）。可以通过管理界面运行测试。在这种情况下，测试验证索引操作是否运行没有错误。

第 18、27 和 35 行尝试获取`page`id 为 in 的记录`request.args(0)`。

第 13、20 行定义和处理为新页面和新评论创建表单，

第 28 行定义并处理 wiki 页面的更新表单。

第 37 行创建一个`grid`允许查看、添加和更新链接到页面的评论的对象。

第 51 行发生了一些神奇的事情。`onkeyup`设置了 INPUT 标记“关键字”的属性。每次访问者释放一个键时，`onkeyup`属性内的 JavaScript 代码都会在客户端执行。这是 JavaScript 代码：

```
ajax('callback', ['keyword'], 'target');
```

`ajax`是在文件“web2py.js”中定义的 JavaScript 函数，该文件包含在默认的“layout.html”中。它需要三个参数：执行同步回调的操作的 URL、要发送到回调的变量 ID 列表（["keyword"]）以及必须插入响应的 ID（"target ”）。

只要您在搜索框中键入内容并释放一个键，客户端就会调用服务器并发送“关键字”字段的内容，并且当服务器响应时，响应会作为内部 HTML 嵌入到页面本身中“目标”标签。

“目标”标签是第 52 行中定义的 DIV。它也可以在视图中定义。

这是视图“default/create.html”的代码：

```html
{{extend 'layout.html'}}
<h1>Create new wiki page</h1>
{{=form}}
```

假设您已注册并登录，如果您访问**创建**页面，您会看到以下内容：



![图片](http://www.web2py.com/books/default/image/29/en3500.png)



这是视图“default/index.html”的代码：

```html
{{extend 'layout.html'}}
<h1>Available wiki pages</h1>
[ {{=A('search', _href=URL('search'))}} ]<br />
<ul>{{for page in pages:}}
     {{=LI(A(page.title, _href=URL('show', args=page.id)))}}
{{pass}}</ul>
[ {{=A('create page', _href=URL('create'))}} ]
```

它生成以下页面：



![图片](http://www.web2py.com/books/default/image/29/en3600.png)



这是视图“default/show.html”的代码：





```html
{{extend 'layout.html'}}
<h1>{{=page.title}}</h1>
[ {{=A('edit', _href=URL('edit', args=request.args))}} ]<br />
{{=MARKMIN(page.body)}}
<h2>Comments</h2>
{{for post in comments:}}
  <p>{{=db.auth_user[post.created_by].first_name}} on {{=post.created_on}}
     says <i>{{=post.body}}</i></p>
{{pass}}
<h2>Post a comment</h2>
{{=form}}
```

如果您希望使用 markdown 语法而不是 markmin 语法：

```
from gluon.contrib.markdown import WIKI as MARKDOWN
```

并使用`MARKDOWN`而不是`MARKMIN`助手。或者，您可以选择接受原始 HTML 而不是 markmin 语法。在这种情况下，您将替换：

```
{{=MARKMIN(page.body)}}
```

和：

```
{{=XML(page.body)}}
```

（这样 XML 就不会被转义，出于安全原因，默认情况下 web2py 通常会这样做）。

这可以通过以下方式更好地完成：

```
{{=XML(page.body, sanitize=True)}}
```

通过设置`sanitize=True`，您告诉 web2py 转义不安全的 XML 标记，例如“<script>”，从而防止 XSS 漏洞。

现在，如果从索引页面单击页面标题，您可以看到您创建的页面：



![图片](http://www.web2py.com/books/default/image/29/en3700.png)



这是视图“default/edit.html”的代码：

```html
{{extend 'layout.html'}}
<h1>Edit wiki page</h1>
[ {{=A('show', _href=URL('show', args=request.args))}}
| {{=A('documents', _href=URL('documents', args=request.args))}} ]<br />
{{=form}}
```

它会生成一个看起来与创建页面几乎相同的页面。

这是视图“default/documents.html”的代码：

```html
{{extend 'layout.html'}}
<h1>Documents for page: {{=page.title}}</h1>
[ {{=A('show', _href=URL('show', args=request.args))}} ]<br />
<h2>Documents</h2>
{{=grid}}
```

如果从“显示”页面单击文档，您现在可以管理附加到页面的文档。



![图片](http://www.web2py.com/books/default/image/29/en3800.png)



最后是视图“default/search.html”的代码：

```
{{extend 'layout.html'}}
<h1>Search wiki pages</h1>
[ {{=A('listall', _href=URL('index'))}}]<br />
{{=form}}<br />{{=target_div}}
```

它生成以下 Ajax 搜索表单：



![图片](http://www.web2py.com/books/default/image/29/en3900.png)



您也可以尝试通过访问直接调用回调操作，例如以下 URL：

```
http://127.0.0.1:8000/mywiki/default/callback?keyword=main
```

如果您查看页面源代码，您会看到回调返回的 HTML：

```
<ul><li><a href="/mywiki/default/show/4">My Main Page</a></li></ul>
```

使用 web2py 生成 wiki 页面的 RSS 提要很容易，因为 web2py 包含`gluon.contrib.rss2`. 只需将以下操作附加到默认控制器：

```python
def news():
    """generates rss feed from the wiki pages"""
    response.generic_patterns = ['.rss']
    pages = db().select(db.page.ALL, orderby=db.page.title)
    return dict(title='mywiki rss feed',
                link='http://127.0.0.1:8000/mywiki/default/index',
                description='mywiki news',
                created_on=request.now,
                items=[dict(title=row.title,
                            link=URL('show', args=row.id, scheme=True, host=True, extension=False),
                            description=MARKMIN(row.body).xml(),
                            created_on=row.created_on) for row in pages])
```

当您访问该页面时

```
http://127.0.0.1:8000/mywiki/default/news.rss
```

您会看到提要（确切的输出取决于提要阅读器）。请注意，由于 URL 中的 .rss 扩展名，dict 会自动转换为 RSS。



![图片](http://www.web2py.com/books/default/image/29/en4000.png)



web2py 还包括 feedparser 来读取第三方提要。

请注意，该行：

```
response.generic_patterns = ['.rss']
```

当 URL 以 glob 模式“.rss”结尾时，指示 web2py 使用通用视图（在我们的例子中为“views/generic.rss”）。默认情况下，通用视图仅允许来自本地主机用于开发目的。

最后，让我们添加一个允许以编程方式搜索 wiki 的 XML-RPC 处理程序：

```python
service = Service()

@service.xmlrpc
def find_by(keyword):
    """finds pages that contain keyword for XML-RPC"""
    return db(db.page.title.contains(keyword)).select().as_list()

def call():
    """exposes all registered services, including XML-RPC"""
    return service()
```

在这里，处理程序操作只是发布（通过 XML-RPC），由`@ervice.xmlrpc`. 在这种情况下，`find_by`。`find_by`不是一个动作（因为它需要一个参数）。它查询数据库，`.select()`然后将记录提取为列表`.as_list()`并返回列表。

下面是一个如何从外部 Python 程序访问 XML-RPC 处理程序的示例。

```python
import xmlrpclib
server = xmlrpclib.ServerProxy('http://127.0.0.1:8000/mywiki/default/call/xmlrpc')
for item in server.find_by('main'):
    print item['created_on'], item['title']
      
```

可以从许多其他理解 XML-RPC 的编程语言访问处理程序，包括 C、C++、C# 和 Java。



#### `date`,`datetime`和`time`格式_

每种字段类型都有三种不同的表示`date`形式`datetime`和`time`：

- 数据库表示
- 内部 web2py 表示
- 表格和表格中的字符串表示

数据库表示是一个内部问题，不会影响代码。在内部，在 web2py 级别，它们分别存储为`datetime.date`,`datetime.datetime`和`datetime.time`object 并且可以这样操作：

```python
for page in db(db.page).select():
    print page.title, page.created_on.day, page.created_on.month, page.created_on.year 
        
```

当日期以形式转换为字符串时，它们将使用 ISO 表示形式进行转换

```
%Y-%m-%d %H:%M:%S
```

但是这种表示是国际化的，您可以使用管理翻译页面将格式更改为另一种格式。例如：

```
%m/%d/%Y %H:%M:%S
```

请注意，默认情况下不会翻译英语，因为 web2py 假定应用程序是用英语编写的。如果您希望国际化适用于英语，您需要创建翻译文件（使用管理员），并且您需要声明应用程序的当前语言不是英语，例如：

```python
T.current_languages = ['null']
```



### 内置的 web2py wiki

现在您可以忘记我们在上一节中内置的代码（不是您学到的有关 web2py API 的内容，只是具体示例的代码），因为我们将提供内置 web2py wiki 的示例。

事实上，web2py 带有 wiki 功能，包括媒体附件、标签、标签云、页面权限，以及对 oembed [ [oembed](http://web2py.com/books/default/reference/29/oembed) ]和组件的支持（第 14 章）。这个 wiki 可以与任何 web2py 应用程序一起使用。

> 请注意，内置 wiki 的 API 仍被认为是实验性的，并且仍然可以进行小的更改。

在这里，我们假设我们从一个名为“wikidemo”的“欢迎”应用程序的简单克隆从头开始。如果没有，请确保在`db.py`迁移中启用，否则将不会自动创建新的所需 wiki 表。

编辑控制器并将“索引”操作替换为。

```python
def index(): return auth.wiki()
```

完毕！你有一个完整的 wiki。请注意，wiki 需要定义一些表，并且它们只会在访问控制器时定义。如果您希望它们随时可用，请使用`auth.wiki(resolve=False)`并确保已打开表迁移：更多信息请参见下面的**Extending-the-auth-wiki-feature**部分。

此时还没有创建页面，为了创建页面，您必须登录并且您必须是名为“wiki_editor”或“wiki_author”的组的成员。如果您以管理员身份登录，系统会自动创建“wiki_editor”组并成为其中的成员。编辑和作者的区别在于编辑可以创建页面，编辑和删除任何页面，而作者可以创建页面（有一些可选限制）并且只能编辑/删除他们创建的页面。

该`auth.wiki()`函数在一个字典中返回一个键，该键`content`被脚手架“views/default/index.html”理解。您可以为此操作创建自己的视图：

```
{{extend 'layout.html'}}
{{=content}}
```

并根据需要添加额外的 HTML 或代码。您不必使用“索引”操作来公开 wiki。您可以使用具有不同名称的操作。

要试用 wiki，只需登录 admin，访问页面

```
http://127.0.0.1:8000/wikidemo/default/index
```

然后选择一个 slug（在出版业务中，一个 slug 是给正在生产中的文章的简称），您将被重定向到一个空白页面，您可以在其中使用 MARKMIN wiki 语法编辑内容。一个名为“[wiki]”的新菜单项将允许您创建、搜索和编辑页面。Wiki 页面的 URL 如下：

```
http://127.0.0.1:8000/wikidemo/default/index/[slug]
```

服务页面的名称以下划线开头：

```
http://127.0.0.1:8000/wikidemo/default/index/_create
http://127.0.0.1:8000/wikidemo/default/index/_search
http://127.0.0.1:8000/wikidemo/default/index/_could
http://127.0.0.1:8000/wikidemo/default/index/_recent
http://127.0.0.1:8000/wikidemo/default/index/_edit/...
http://127.0.0.1:8000/wikidemo/default/index/_editmedia/...
http://127.0.0.1:8000/wikidemo/default/index/_preview/...
```

尝试创建更多页面，例如“index”、“aboutus”和“contactus”。尝试编辑它们。

该`wiki`方法具有以下特征：

```python
def wiki(self, slug=None, env=None, render='markmin',
         manage_permissions=False, force_prefix='',
         restrict_search=False, resolve=True,
         extra=None, menu_groups=None)
```

它采用以下参数：

- `render`默认为`'markmin'`但可以设置为等于`'html'`。它决定了 wiki 的语法。稍后我们将讨论 markmin wiki 标记。如果将其更改为 HTML，则可以使用所见即所得的 JavaScript 编辑器，例如 TinyMCE 或 NicEdit。
- `manage_permissions`. 默认情况下设置为`False`，并且仅识别“wiki_editor”和“wiki_author”的权限。如果您将其更改为`True`创建/编辑页面，将提供按名称指定其成员有权阅读和编辑页面的组的选项。有一个“每个人”组，其中包括所有用户。
- `force_prefix`. 如果设置为类似的东西`'%(id)s-'`，它将限制作者（而不是编辑）创建带有“[user id]-[page name]”之类的前缀的页面。前缀可以包含 id ("%(id)s") 或用户名 ("%(username)s") 或 auth_user 表中的任何其他字段，只要相应列包含将传递 URL 的有效字符串验证。
- `restrict_search`. 默认情况下`False`，任何登录用户都可以搜索所有 wiki 页面（但不必阅读或编辑它们）。如果设置为`True`，作者只能搜索自己的页面，编辑可以搜索所有内容，其他用户无法搜索任何内容。
- `menu_groups`. 这默认为`None`并表示始终显示 wiki 管理菜单（搜索、创建、编辑等）。您可以将其设置为组名列表，其成员只能看到此菜单，例如`['wiki_editor','wiki_author']`。请注意，即使每个人都可以看到菜单，但这并不意味着每个人都可以执行菜单中列出的操作，因为它们受访问控制系统的监管。

该`wiki`方法有一些额外的参数，稍后会解释：`slug`、`env`和`extra`。



#### MARKMIN 基础知识

MARKMIN 语法允许您使用 标记**粗体**文本`**bold**`，使用 标记*斜体*文本`''italic''`，并且`code`文本应由双引号分隔。标题必须以 # 为前缀，部分以 ## 为前缀，子部分以 ### 为前缀。使用减号 (-) 作为未订购项目的前缀，使用加号 (+) 作为已订购项目的前缀。URL 会自动转换为链接。以下是 markmin 文本的示例：

```
# This is a title
## this is a section title
### this is a subsection title

Text can be **粗体**, ''斜体'', ``代码`` etc.
Learn more at:

http://web2py.com
```

您可以使用`extra`参数 of`auth.wiki`将额外的渲染规则传递给 MARKMIN 助手。

您可以在第 5 章中找到有关 MARKMIN 语法的更多信息。

`auth.wiki`比准系统 MARKMIN 助手更强大，支持 oembed 和组件。

您可以使用`env`参数 of`auth.wiki`向您的 wiki 公开函数。例如：

```python
auth.wiki(env=dict(join=lambda a:"-".join(a.split(","))))
```

允许您使用标记语法：

```
@{join:1,2,3}
```

这将调用带参数传递给 env 的 join 函数`"1,2,3"`，并将呈现为`1-2-3`.



#### 嵌入式协议

您可以将任何 URL 输入（或剪切并粘贴）到 wiki 页面中，它会呈现为 URL 的链接。有例外：

- 如果 URL 具有图像扩展名，则链接作为图像嵌入，`<img/>`.
- 如果 URL 具有音频扩展名，则链接作为 HTML5 音频嵌入`<audio/>`。
- 如果 URL 具有视频扩展名，则链接将嵌入为 HTML5 video `<video/>`。
- 如果 URL 具有 MS Office 或 PDF 扩展名，则嵌入 Google Doc Viewer，显示文档内容（仅适用于公共文档）。
- 如果 URL 指向 YouTube 页面、Vimeo 页面或 Flickr 页面，web2py 会联系相应的 Web 服务并询问其嵌入内容的正确方法。这是使用`oembed`协议完成的。

以下是支持格式的完整列表：

```
Image (.PNG, .GIF, .JPG, .JPEG)
Audio (.WAV, .OGG, .MP3)
Video (.MOV, .MPE, .MP4, .MPG, .MPG2, .MPEG, .MPEG4, .MOVIE)
```

通过 Google Doc Viewer 支持：

```
Microsoft Excel (.XLS and .XLSX)
Microsoft PowerPoint 2007 / 2010 (.PPTX)
Apple Pages (.PAGES)
Adobe PDF (.PDF)
Adobe Illustrator (.AI)
Adobe Photoshop (.PSD)
Autodesk AutoCad (.DXF)
Scalable Vector Graphics (.SVG)
PostScript (.EPS, .PS)
TrueType (.TTF)
xml Paper Specification (.XPS)
```

由 oembed 支持：

```
flickr.com
youtube.com
hulu.com
vimeo.com
slideshare.net
qik.com
polleverywhere.com
wordpress.com
revision3.com
viddler.com
```

这是在 web2py 文件中实现的`gluon.contrib.autolinks`，特别是在函数中`expand_one`。您可以通过注册更多服务来扩展 oembed 支持。这是通过在`EMBED_MAPS`列表中附加一个条目来完成的：

```python
from gluon.contrib.autolinks import EMBED_MAPS
EMBED_MAPS.append((re.compile('http://vimeo.com/\S*'),
                   'http://vimeo.com/api/oembed.json'))
                   
```



#### 参考维基内容

如果您使用 slug "contactus" 创建一个 wiki 页面，您可以将此页面称为

```
@////contactus
```

这里@//// 代表

```
@/app/controller/function/
```

但“app”、“controller”和“function”被省略，因此假定为默认值。

同样，您可以使用 wiki 菜单上传链接到页面的媒体文件（例如图像）。“管理媒体”页面将显示您上传的所有文件，还将显示链接媒体文件的正确表达式。例如，如果您上传一个名为“test.jpg”且标题为“beach”的文件，则链接表达式将类似于：

```
@////15/beach.jpg
```

`@////`与前面描述的前缀相同。`15`是存储媒体文件的记录的id。`beach`是标题。`.jpg`是原始文件的扩展名。

如果您剪切并粘贴`@////15/beach.jpg`到 wiki 页面中，您将嵌入图像。

请注意媒体文件链接到页面并从页面继承访问权限。



#### 维基菜单

如果您创建一个带有 slug “wiki-menu” 页面的页面，它将被解释为菜单的描述。这是一个例子：

```
- Home > @////index
- Info > @////info
- web2py > http://www.web2py.com
- - About us > @////aboutus
- - Contact us > @////contactus
```

每行一个菜单项。我们对嵌套菜单项使用双破折号。符号将`>`菜单项标题与菜单项链接分开。

请注意菜单附加到`response.menu`. 它不会取代它。`[wiki]`自动添加具有服务功能的菜单项。



#### 服务功能

例如，如果您想使用 wiki 创建一个可编辑的侧边栏，您可以创建一个页面，`slug="sidebar"`然后将其嵌入到您的 layout.html 中

```
{{=auth.wiki(slug='sidebar')}}
```

请注意，“侧边栏”这个词并没有什么特别之处。任何 wiki 页面都可以在您的代码中的任何位置检索和嵌入。这允许您将 wiki 功能与常规 web2py 功能混合和匹配。

> 另请注意
>
> ```
> auth.wiki('sidebar')
> ```
>
> 是相同的
>
> ```
> auth.wiki(slug='sidebar')
> ```
>
> ，因为 slug kwarg 是方法签名中的第一个。前者给出了稍微简单的语法。

您还可以嵌入特殊的 wiki 功能，例如按标签搜索：

```
{{=auth.wiki('_search')}}
```

或标签云：

```
{{=auth.wiki('_cloud')}}
```



#### 扩展 auth.wiki 功能

当您启用 wiki 的应用程序变得更加复杂时，您可能需要自定义由 Auth 接口管理的 wiki db 记录，或者为 wiki CRUD 任务公开自定义表单。例如，您可能想要自定义 wiki 表记录表示或添加新的字段验证器。默认情况下不允许这样做，因为只有在使用 auth.wiki() 方法请求 wiki 接口之后才定义 wiki 模型。要允许在您的应用程序模型中访问特定于 wiki 的数据库设置，您必须将以下语句添加到您的模型文件（即 db.py）

```
# Make sure this is called after the auth instance is created
# and before any change to the wiki tables
auth.wiki(resolve=False)
```

通过在您的模型中使用上面的行，wiki 表将可以访问（即`wiki_page`）用于自定义 CRUD 或其他数据库任务。

> 请注意，您仍然必须在控制器或视图中调用 auth.wiki() 以公开 wiki 界面，因为该`resolve=False`参数指示 auth 对象仅构建 wiki 模型而无需任何其他界面设置。

此外，通过`False`在方法调用中将解析设置为，现在可以通过应用程序的默认数据库接口访问 wiki 表，`<app>/appadmin`以管理 wiki 记录。

另一种可能的定制是在标准 wiki 表格中添加额外的字段（与`auth_user`第 9 章中描述的表格相同）。方法如下：

```
# Place this after auth object initialization
auth.settings.extra_fields["wiki_page"] = [Field("ablob", "blob"), ]
```

`blob`上面的行向`wiki_page`表中添加了一个字段。没有必要call

```
auth.wiki(resolve=False)
```

对于此选项，除非您需要访问 wiki 模型以进行其他自定义。





#### wiki组件

新的 web2py 最强大的功能之一在于能够将一个动作嵌入到另一个动作中。我们称之为组件。

考虑以下模型：

```
db.define_table('thing', Field('name', requires=IS_NOT_EMPTY()))
```

以及以下操作：

```
@auth.requires_login()
def manage_things():
    return SQLFORM.grid(db.thing)
```

此操作很特殊，因为它返回一个小部件/帮助程序，而不是对象的字典。现在我们可以将这个`manage_things`动作嵌入到任何视图中，

```
{{=LOAD('default', 'manage_things', ajax=True)}}
```

这允许访问者通过 Ajax 与组件交互，而无需重新加载嵌入小部件的主机页面。该操作通过 Ajax 调用，继承主机页面的样式，并捕获所有表单提交和 flash 消息，以便在当前页面中处理它们。除此之外，该`SQLFORM.grid`小部件使用数字签名的 URL 来限制访问。有关组件的更多信息，请参见第 13 章。

可以使用 MARKMIN 语法将上述组件嵌入到 wiki 页面中：

```
ValueError: malformed string
```

这只是告诉 web2py 我们希望将“默认”控制器中定义的“manage_things”操作包含为 Ajax“组件”。

> 大多数用户将能够简单地通过使用`auth.wiki`创建页面和菜单并将自定义组件嵌入到 wiki 页面中来构建相对复杂的应用程序。Wiki 可以被认为是一种允许组成员创建页面的机制，但它们也可以被认为是一种以模块化方式开发应用程序的方式。



### 更多关于**admin**

管理界面提供了我们在这里简要回顾的附加功能。



#### site

这个页面是 web2py 的主要管理界面。它在左侧列出了所有已安装的应用程序，而在右侧则有一些特殊的操作表单。

其中第一个显示 web2py 版本，并建议在有新版本时对其进行升级。当然，在升级之前一定要有一个完整的工作备份！然后还有另外两种形式允许通过指定名称来创建新应用程序（简单或使用在线向导）。



以下表单允许从本地文件或远程 URL 上传现有应用程序。当您上传一个应用程序时，您需要为其指定一个名称（使用不同的名称可以让您安装同一应用程序的多个副本）。例如，您可以尝试上传由 Bruno Rocha 创建的 Movuca 社交网络应用程序：



```
https://github.com/rochacbruno/Movuca
```

或 Martin Mulone 创建的 Instant Press CMS：

```
http://code.google.com/p/instant-press/
```

或提供的众多示例应用程序之一：

```
http://web2py.com/appliances
```

> Web2py 文件是作为`.w2p`文件的包。这些是 tar gzip 文件。Web2py 使用`.w2p`扩展名而不是`.tgz`扩展名来防止浏览器在下载时解压缩。可以手动解压缩它们，`tar xzvf [filename]`尽管这不是必需的。



![图片](http://www.web2py.com/books/default/image/29/en4100.png)



上传成功后，web2py 会显示上传文件的 MD5 校验和。您可以使用它来验证文件在上传过程中没有损坏。应用程序名称将出现在已安装应用程序列表中。

如果您从源代码运行 web2py 并且已经安装（如有必要，使用“easy_install gitpython”进行设置），您可以使用上传表单中`gitpython`的 URL 直接从 git 存储库安装应用程序。`.git`在这种情况下，您还可以使用管理界面将更改推送回存储库，但这是一个实验性功能。

例如，您可以使用以下 URL 在 web2py 站点上本地安装显示本书的应用程序：

```
https://github.com/web2py/web2py-book.git
```

> 该存储库托管本书的当前更新版本（可能与您在网站上看到的稳定版本不同）。热烈邀请您使用它以拉取请求的形式提交改进、修复和更正。

对于安装的每个应用程序，您可以使用*站点*页面：

- 通过单击其名称直接转到该应用程序。
- 卸载应用程序。
- 跳转到*关于*页面（阅读下文）。
- 跳转到*编辑*页面（阅读下文）。
- 跳转到*错误*页面（阅读下文）。
- 清理临时文件（会话、错误和 cache.disk 文件）。
- 全部打包。这将返回一个包含应用程序完整副本的 tar 文件。我们建议您在打包应用程序之前清理临时文件。
- 编译应用程序。如果没有错误，此选项将对所有模型、控制器和视图进行字节码编译。因为视图可以扩展并包含树中的其他视图，所以在字节码编译之前，每个控制器的视图树都被折叠到一个文件中。最终效果是字节码编译的应用程序更快，因为在运行时不再需要解析模板或字符串替换。
- 打包编译。此选项仅适用于字节码编译的应用程序。它允许在没有源代码的情况下打包应用程序以作为封闭源代码分发。请注意，Python（与任何其他编程语言一样）在技术上可以被反编译；因此编译不提供对源代码的完全保护。然而，反编译可能很困难并且可能是非法的。
- 删除编译。它只是从应用程序中删除字节码编译的模型、视图和控制器。如果应用程序与源代码一起打包或在本地编辑，则删除字节码编译文件没有任何害处，应用程序将继续工作。如果应用程序是从打包的编译文件中安装的，那么这是不安全的，因为没有可恢复的源代码，应用程序将不再工作。

> web2py 管理站点页面提供的所有功能也可以通过模块中定义的 API 以编程方式访问`gluon/admin.py`。只需打开一个 python shell 并导入这个模块。

如果安装了 Google App Engine SDK，则管理*站点*页面会显示一个按钮，用于将您的应用程序推送到 GAE。如果`python-git`已安装，还有一个按钮可将您的应用程序推送到 Open Shift。要在或其他托管系统上安装应用程序，`Heroku`您应该查看“脚本”文件夹以获取适当的脚本。



#### 关于





关于选项卡允许编辑应用程序及其许可证的描述*。*这些分别写在应用程序文件夹的 ABOUT 和 LICENSE 文件中。



![图片](http://www.web2py.com/books/default/image/29/en4300.png)



您可以对这些文件使用`MARKMIN`或`gluon.contrib.markdown.WIKI`语法，如参考文献中所述。[[降价2](http://web2py.com/books/default/reference/29/markdown2) ]。



#### 设计

您已经在本章中使用了*编辑页面。*在这里，我们想指出*编辑*页面的更多功能。

- 如果单击任何文件名，您可以看到带有语法突出显示的文件内容。
- 如果单击编辑，则可以通过 Web 界面编辑文件。
- 如果单击删除，则可以（永久）删除文件。
- 如果单击测试，web2py 将运行测试。测试由开发人员使用 Python doctest 编写，每个函数都应该有自己的测试。
- 您可以添加语言文件，扫描应用程序以发现所有字符串，并通过 Web 界面编辑字符串翻译。
- 如果静态文件组织在文件夹和子文件夹中，则可以通过单击文件夹名称来切换文件夹层次结构。

下图显示了欢迎应用程序的测试页面的输出。



![图片](http://www.web2py.com/books/default/image/29/en4400.png)



下图显示了欢迎应用程序的语言选项卡。



![图片](http://www.web2py.com/books/default/image/29/en4500.png)



下图显示了如何编辑语言文件，在本例中为欢迎应用程序的“it”（意大利语）语言。



![图片](http://www.web2py.com/books/default/image/29/en4600.png)





#### 集成的基于 Web 的调试器

*（需要 Python 2.7 或更高版本）*

web2py 管理员包括一个基于 Web 的调试器。

使用提供的基于 Web 的编辑器，您可以向 Python 代码添加断点，并且从关联的调试器控制台，您可以检查这些断点处的系统变量并恢复执行。这在以下屏幕截图中进行了说明：交互式控制台还用作 python 暂存器。



![图片](http://www.web2py.com/books/default/image/29/debugger.png)



此功能基于 Mariano Reingart 创建的 Qdb 调试器。它使用 multiprocessing.connection 在后端和前端之间进行通信，使用类似 JSON-RPC 的流协议。[ [qdb](http://web2py.com/books/default/reference/29/qdb) ]

##### 通过代码设置断点

包括这个：

```
from gluon.debug import dbg
```

并放入调试器，将其放在所需的位置：

```
dbg.set_trace()
```

调试器应用程序有一个断点管理器。

> 注意：web2py 不知道您的浏览器中是否真的打开了调试窗口；执行暂停不管。IDE 通常有自己的进程间调试器，例如 PyCharm 或 PyDev。如果您包含*gluon*库，他们可能会抱怨。



#### 基于 Web 的 Python shell

*如果您在edit*中单击控制器选项卡下的“shell”链接，web2py 将打开一个基于 Web 的 Python shell 并将执行当前应用程序的模型。这允许您以交互方式与您的应用程序对话。



![图片](http://www.web2py.com/books/default/image/29/en4700.png)



> 小心使用基于 Web 的 shell - 因为不同的 shell 请求将在不同的线程中执行。这很容易出错，尤其是在您使用数据库创建和连接时。对于此类活动（即，如果您需要持久性），最好使用 python 命令行。



#### crontab

在*编辑*中的控制器选项卡下还有一个“crontab”链接。通过单击此链接，您将能够编辑 web2py crontab 文件。这遵循与 Unix crontab 相同的语法，但不依赖于 Unix。事实上，它只需要 web2py，它可以在 Windows 上运行。它允许您注册需要在预定时间在后台执行的操作。有关这方面的更多信息，请参阅下一章。



#### 错误

在编写 web2py 时，难免会犯错误并引入 bug。*web2py 有两种帮助：1）它允许您为每个可以从编辑*页面在浏览器中运行的功能创建测试；2) 当出现错误时，向访问者发出票证并记录错误。

故意在图像应用程序中引入错误，如下所示：

```
def index():
    images = db().select(db.image.ALL, orderby=db.image.title)
    1/0
    return dict(images=images)
```

当您访问 index 操作时，您将获得以下票证：



![图片](http://www.web2py.com/books/default/image/29/en4800.png)



只有管理员可以访问票证：



![图片](http://www.web2py.com/books/default/image/29/en4900.png)



票据显示回溯、导致问题的文件的内容以及系统的完整状态（变量、请求、会话等）。如果错误发生在视图中，web2py 会显示从 HTML 转换为 Python 的视图代码。这允许轻松识别文件的逻辑结构。

默认情况下，票据存储在文件系统中，并按回溯分组显示。管理界面提供聚合视图（回溯类型和发生次数）和详细视图（所有工单都按工单 ID 列出）。管理员可以在两种视图之间切换。

请注意，在任何地方**admin**都会显示语法突出显示的代码（例如，在错误报告中，web2py 关键字显示为橙色）。如果您单击 web2py 关键字，您将被重定向到有关该关键字的文档页面。

如果您修复索引操作中的除零错误并在索引视图中引入一个：

```
{{extend 'layout.html'}}

<h1>Current Images</h1>
<ul>
{{for image in images:}}
{{1/0}}
{{=LI(A(image.title, _href=URL("show", args=image.id)))}}
{{pass}}
</ul>
```

你得到以下票：



![图片](http://www.web2py.com/books/default/image/29/en5000.png)



请注意，web2py 已将视图从 HTML 转换为 Python 文件，票证中描述的错误指的是生成的 Python 代码，而不是原始视图文件：



![图片](http://www.web2py.com/books/default/image/29/en5100.png)



起初这可能看起来令人困惑，但实际上它使调试更容易，因为 Python 缩进突出显示了您嵌入视图中的代码的逻辑结构。

代码显示在同一页面的底部。

所有工单都在每个应用程序的*错误页面中的管理员下列出：*



![图片](http://www.web2py.com/books/default/image/29/en5200.png)





#### mercurial一种轻量级版本控制

如果您从源代码运行，管理界面会显示一个名为“版本控制”的菜单项。



![图片](http://www.web2py.com/books/default/image/29/en5300.png)



输入评论并在结果页面中按下“提交”按钮将提交当前应用程序。第一次提交时，将为特定应用程序创建一个本地 Mercurial 存储库。在后台，Mercurial 将有关您在代码中所做更改的信息存储到应用程序子文件夹中的隐藏文件夹“.hg”中。每个应用程序都有自己的“.hg”文件夹和自己的“.hgignore”文件（告诉 Mercurial 要忽略哪些文件）。要使用此功能，您必须安装 Mercurial 版本控制库（至少 1.9 版）：

```
pip install mercurial
```

Mercurial Web 界面允许您浏览以前的提交和差异文件，但我们建议您直接从 shell 或许多基于 GUI 的 Mercurial 客户端之一使用 Mercurial，因为它们更强大。例如，它们将允许您将您的应用程序与远程源存储库同步。

您可以在此处阅读有关 Mercurial 的更多信息：

```
http://mercurial.selenic.com/
```



#### Git 集成

管理应用程序还包括 git 集成。需要 Python git 库，例如

```
http://mercurial.selenic.com/
```

然后每个应用程序，您必须克隆或以其他方式配置 git 存储库。

在这些步骤之后，每个 git 管理的应用程序的管理菜单将显示 git push 和 git pull。不受 git 管理的应用程序将被忽略。您可以从默认远程存储库中拉取和推送应用程序。



#### 应用程序向导（实验性）

**管理**界面包括一个可以帮助您创建新应用程序的向导。您可以从“站点”页面访问该向导，如下图所示。



![图片](http://www.web2py.com/books/default/image/29/en5400.png)



该向导将引导您完成创建新应用程序所涉及的一系列步骤：

- 为应用程序选择一个名称
- 配置应用程序并选择所需的插件
- 构建所需的模型（它将为每个模型创建 CRUD 页面）
- 允许您使用 MARKMIN 语法编辑这些页面的视图

下图显示了该过程的第二步。



![图片](http://www.web2py.com/books/default/image/29/en5500.png)



您可以看到一个用于选择布局插件的下拉列表（来自`web2py.com/layouts`），一个用于检查其他插件的多选下拉列表（来自`web2py.com/plugins`）和一个“登录配置”字段，用于放置 Janrain“域：密钥”。

其他步骤几乎是不言自明的。

向导运行良好，但它被认为是*实验性功能*，原因有两个：

- 使用向导创建并手动编辑的应用程序以后不能由向导修改。
- 向导的界面将随着时间的推移而改变，以包括对更多功能的支持和更轻松的可视化开发。

在任何情况下，向导都是快速原型设计的便捷工具，它可用于引导具有备用布局和可选插件的新应用程序。



#### 配置**admin**

通常不需要执行任何**管理员**配置，但可以进行一些自定义。登录 admin 后，您可以通过 URL 编辑 admin 配置文件：

```
http://127.0.0.1:8000/admin/default/edit/admin/models/0.py
```

请注意，**admin**可用于编辑自身。事实上， **admin**和其他任何应用程序一样都是一个应用程序。

文件“0.py”或多或少是自我记录的，无论如何，这里有一些最重要的可能自定义：

```
GAE_APPCFG = os.path.abspath(os.path.join('/usr/local/bin/appcfg.py'))
```

这应该指向 Google App Engine SDK 附带的“appcfg.py”文件的位置。如果您有 SDK，您可能希望将这些配置参数更改为正确的值。它将允许您从管理界面部署到 GAE。

您还可以在演示模式下设置 web2py 管理员：

```
DEMO_MODE = True
FILTER_APPS = ['welcome']
```

并且只有 FILTER_APPS 中列出的应用程序可以访问，并且它们只能在只读模式下访问。

如果您是一名教师并希望向学生公开管理界面，以便学生可以为他们的项目共享一个管理界面（想想虚拟实验室），可以通过设置来实现：

```
MULTI_USER_MODE = True
```

这样，学生将需要登录，并且只能通过管理员访问他们自己的应用程序。作为第一个用户/教师，您将能够访问所有。

在多用户模式下，您可以使用管理员中的“批量注册”链接注册学生，并使用“管理学生”链接管理他们。该系统还跟踪学生何时登录以及他们在代码中添加/删除了多少行代码。该数据在应用程序“关于”页面下以图表的形式呈现给管理员。

请注意，此机制仍假定所有用户都是受信任的。在 admin 下创建的所有应用程序都在同一文件系统上的相同凭据下运行。学生创建的应用程序可以访问其他学生创建的应用程序的数据和源。学生也可以创建一个锁定服务器的应用程序。



#### 移动端**admin**

请注意，管理应用程序包括打包 jQuery Mobile 的“plugin_jqmobile”。当从移动设备访问 admin 时，web2py 会检测到这一点，并使用适合移动设备的布局显示界面：



![图片](http://www.web2py.com/books/default/image/29/mobile.png)





### 更多关于**appadmin**

**appadmin**不打算向公众公开。它旨在通过提供对数据库的轻松访问来帮助您。它仅包含两个文件：控制器“appadmin.py”和视图“appadmin.html”，控制器中的所有操作都使用它们。

appadmin控制器相对较小且可读性强**；**它提供了一个设计数据库接口的示例。

**appadmin**显示哪些数据库可用以及每个数据库中存在哪些表。您可以插入记录并单独列出每个表的所有记录。**appadmin**一次分页输出 100 条记录。

选择一组记录后，页面标题会更改，从而允许您更新或删除所选记录。

要更新记录，请在查询字符串字段中输入 SQL 分配：

```
title = 'test'
```

其中字符串值必须用单引号括起来。多个字段可以用逗号分隔。

要删除记录，请单击相应的复选框以确认您确定。

如果查询包含涉及两个或多个表的 SQL 条件，**appadmin也可以执行连接。**例如，尝试：

```
db.image.id == db.post.image_id
```

web2py 将此传递给 DAL，它知道查询链接了两个表；因此，这两个表都是通过 INNER JOIN 选择的。这是输出：



![图片](http://www.web2py.com/books/default/image/29/en5600.png)



如果单击 id 字段的编号，您将获得具有相应 id 的记录的编辑页面。

如果单击参考字段的编号，您将获得参考记录的编辑页面。

您不能更新或删除连接选择的行，因为它们涉及来自多个表的记录，这将是不明确的。

除了其数据库管理功能，**appadmin**还允许您查看有关应用程序`cache`(at `/yourapp/appadmin/cache`) 内容以及当前`request`、`response`和`session`对象 (at `/yourapp/appadmin/state`) 内容的详细信息。

**appadmin**replaces `response.menu` with its own menu, which provides links to the application's **edit** page in **admin**, the **db** (database administration) page, the **state** page, and the **cache** page. If your application's layout does not generate a menu using `response.menu`, then you will not see the **appadmin** menu. In that case, you can modify the appadmin.html file and add `{{=MENU(response.menu)}}` to display the menu.