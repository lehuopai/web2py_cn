## 第4章 核心库





### 命令行选项

可以跳过 GUI 并直接从命令行启动 web2py，方法是键入以下内容：

```
python web2py.py -a '你的密码' -i 127.0.0.1 -p 8000
```

当 web2py 启动时，它会创建一个名为“parameters_8000.py”的文件，其中存储散列密码。如果您使用“<ask>”作为密码，web2py 会提示您输入密码。

为了提高安全性，您可以使用以下命令启动 web2py：

```
python web2py.py -a '<recycle>' -i 127.0.0.1 -p 8000
```

在这种情况下，web2py 会重用之前存储的散列密码。如果没有提供密码，或者如果“parameters_8000.py”文件被删除，则基于 Web 的管理界面将被禁用。

在某些 Unix/Linux 系统上，如果密码是

```
<pam_user:some_user>
```

web2py 使用操作系统帐户的 PAM 密码`some_user`来验证管理员身份，除非被 PAM 配置阻止。

> web2py 通常与 CPython（Guido van Rossum 创建的 Python 解释器的 C 实现）一起运行，但它也可以与 PyPy 和 Jython 一起运行。后一种可能性允许在 Java EE 基础架构的上下文中使用 web2py。要使用 Jython，只需将“python web2py.py ...”替换为“jython web2py.py ...”。

“web2py.py”脚本可以接受许多命令行参数，指定最大线程数、启用 SSL 等。对于完整的列表类型：

```
>>> python web2py.py -h
Usage: python web2py.py

web2py Web Framework startup script. ATTENTION: unless a password
is specified (-a 'passwd'), web2py will attempt to run a GUI.
In this case command line options are ignored.

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -i IP, --ip=IP        IP address of the server (e.g., 127.0.0.1 or ::1);
                        Note: This value is ignored when using the
                        'interfaces' option.
  -p PORT, --port=PORT  port of server (8000)
  -a PASSWORD, --password=PASSWORD
                        password to be used for administration (use -a
                        "<recycle>" to reuse the last password))
  -c SSL_CERTIFICATE, --ssl_certificate=SSL_CERTIFICATE
                        file that contains ssl certificate
  -k SSL_PRIVATE_KEY, --ssl_private_key=SSL_PRIVATE_KEY
                        file that contains ssl private key
  --ca-cert=SSL_CA_CERTIFICATE
                        Use this file containing the CA certificate to
                        validate X509 certificates from clients
  -d PID_FILENAME, --pid_filename=PID_FILENAME
                        file to store the pid of the server
  -l LOG_FILENAME, --log_filename=LOG_FILENAME
                        file to log connections
  -n NUMTHREADS, --numthreads=NUMTHREADS
                        number of threads (deprecated)
  --minthreads=MINTHREADS
                        minimum number of server threads
  --maxthreads=MAXTHREADS
                        maximum number of server threads
  -s SERVER_NAME, --server_name=SERVER_NAME
                        server name for the web server
  -q REQUEST_QUEUE_SIZE, --request_queue_size=REQUEST_QUEUE_SIZE
                        max number of queued requests when server unavailable
  -o TIMEOUT, --timeout=TIMEOUT
                        timeout for individual request (10 seconds)
  -z SHUTDOWN_TIMEOUT, --shutdown_timeout=SHUTDOWN_TIMEOUT
                        timeout on shutdown of server (5 seconds)
  --socket-timeout=SOCKET_TIMEOUT
                        timeout for socket (5 second)
  -f FOLDER, --folder=FOLDER
                        location of the applications folder (also known as directory)
  -v, --verbose         increase --test verbosity
  -Q, --quiet           disable all output
  -D DEBUGLEVEL, --debug=DEBUGLEVEL
                        set debug output level (0-100, 0 means all, 100 means
                        none; default is 30)
  -S APPNAME, --shell=APPNAME
                        run web2py in interactive shell or IPython (if
                        installed) with specified appname (if app does not
                        exist it will be created). APPNAME like a/c/f (c,f
                        optional)
  -B, --bpython         run web2py in interactive shell or bpython (if
                        installed) with specified appname (if app does not
                        exist it will be created). Use combined with --shell
  -P, --plain           only use plain python shell; should be used with
                        --shell option
  -M, --import_models   auto import model files; default is False; should be
                        used with --shell option
  -R PYTHON_FILE, --run=PYTHON_FILE
                        run PYTHON_FILE in web2py environment; should be used
                        with --shell option
  -K SCHEDULER, --scheduler=SCHEDULER
                        run scheduled tasks for the specified apps: expects a
                        list of app names as -K app1,app2,app3 or a list of
                        app:groups as -K app1:group1:group2,app2:group1 to
                        override specific group_names. (only strings, no
                        spaces allowed. Requires a scheduler defined in the
                        models
  -X, --with-scheduler  run schedulers alongside webserver
  -T TEST_PATH, --test=TEST_PATH
                        run doctests in web2py environment; TEST_PATH like
                        a/c/f (c,f optional)
  -C, --cron            trigger a cron run manually; usually invoked from a
                        system crontab
  --softcron            triggers the use of softcron
  -Y, --run-cron        start the background cron process
  -J, --cronjob         identify cron-initiated command
  -L CONFIG, --config=CONFIG
                        config file
  -F PROFILER_DIR, --profiler=PROFILER_DIR
                        profiler dir
  -t, --taskbar         use web2py gui and run in taskbar (system tray)
  --nogui               text-only, no GUI
  -A ARGS, --args=ARGS  should be followed by a list of arguments to be passed
                        to script, to be used with -S, -A must be the last
                        option
  --no-banner           Do not print header banner
  --interfaces=INTERFACES
                        listen on multiple addresses: "ip1:port1:key1:cert1:ca
                        _cert1;ip2:port2:key2:cert2:ca_cert2;..."
                        (:key:cert:ca_cert optional; no spaces; IPv6 addresses
                        must be in square [] brackets)
  --run_system_tests    runs web2py tests
```

> 请注意：
>
> - `-W`用于安装 Windows 服务的选项已被删除。请参阅`nssm`第[13 章](http://web2py.com/books/default/chapter/29/13#nssm)。
> - Profiler 输出可以使用`runsnakerun`工具进行分析。

**小写选项用于配置 Web 服务器。该`-L`选项告诉 web2py 从文件中读取配置选项，而`-S`,`-P`和`-M`options 启动交互式 Python shell。该`-T`选项在 web2py 执行环境中查找并运行控制器文档测试。例如，以下示例从“欢迎”应用程序中的所有控制器运行 doctest：**

```
python web2py.py -vT welcome
```



### 工作流程

web2py 工作流程如下：

- HTTP 请求到达 Web 服务器（内置 Rocket 服务器或通过 WSGI 或其他适配器连接到 web2py 的不同服务器）。Web 服务器在自己的线程中并行处理每个请求。
- HTTP 请求标头被解析并传递给调度程序（在本章后面的[调度](http://web2py.com/books/default/chapter/29/04/the-core#Dispatching)部分中解释）。
- 调度程序决定哪个已安装的应用程序将处理请求并将 URL 中的 PATH_INFO 映射到函数调用中。每个 URL 对应一个函数调用。
- 直接处理静态文件夹中的文件请求，大文件自动流式传输到客户端。
- 除了静态文件之外的任何请求都被映射到一个动作中（即控制器文件中的一个函数，在请求的应用程序中）。
- 在调用该操作之前，会发生一些事情：如果请求标头包含应用程序的会话 cookie，则检索会话对象；如果没有，则创建会话 id（但会话文件直到稍后才保存）；创建请求的执行环境；模型在此环境中执行。
- 最后，控制器动作在预建环境中执行。
- 如果该操作返回一个字符串，则将其返回给客户端（或者如果该操作返回一个 web2py HTML 帮助器对象，则将其序列化并返回给客户端）。
- 如果操作返回一个可迭代的，这将用于循环并将数据流式传输到客户端。
- 如果操作返回一个字典，web2py 会尝试定位一个视图来呈现字典。视图必须与操作具有相同的名称（除非另有说明），并且与请求的页面具有相同的扩展名（默认为 .html）；失败时，web2py 可能会选择一个通用视图（如果可用并且启用）。视图会看到模型中定义的每个变量以及操作返回的字典中定义的变量，但看不到控制器中定义的全局变量。
- 除非另有说明，否则整个用户代码都在单个数据库事务中执行。
- 如果用户代码成功，则提交事务。
- 如果用户代码失败，则将回溯存储在票证中，并向客户端颁发票证 ID。只有系统管理员可以搜索和读取工单中的回溯。

有一些注意事项需要牢记：

- 同一文件夹/子文件夹中的模型按字母顺序执行。
- 模型中定义的任何变量都将对按字母顺序排列的其他模型、控制器和视图可见。





#### 条件模型





- 子文件夹中的模型根据使用的控制器有条件地执行。这避免了在每次请求时处理所有表定义。例如，如果用户请求了“/a/c/f”，其中“a”是应用程序，“c”是控制器，“f”是函数（动作），则执行以下模型：

```
applications/a/models/*.py
applications/a/models/c/*.py
applications/a/models/c/f/*.py
```

默认情况下强制执行此行为。更改`response.models_to_run`正则表达式列表，您可以强制执行您想要的行为。查看[响应](http://web2py.com/books/default/chapter/29/04/the-core#response_models_to_run)以获取更多详细信息。

- 执行请求的控制器并调用请求的函数。这意味着控制器中的所有顶级代码也会在对该控制器的每次请求时执行。
- 仅当操作返回字典时才调用视图。
- 如果没有找到视图，web2py 会尝试使用通用视图。默认情况下，通用视图被禁用，尽管“欢迎”应用程序在 /models/db.py 中包含一行以仅在 localhost 上启用它们。它们可以根据扩展类型和操作启用（使用`response.generic_patterns`）。一般来说，通用视图是一种开发工具，通常不应在生产中使用。如果您希望某些操作使用通用视图，请在中列出这些操作`response.generic_patterns`（在“服务”一章中更详细地讨论）。

一个动作的可能行为如下：

**返回一个字符串**：

```
def index():
    return 'data'
```

**返回视图的字典**：

```
def index():
    return dict(key='value')
```

动作中所有局部变量的列表可能是一个方便使用的字典：

```
def index():
    return locals()
```

**将用户重定向到另一个页面**：

```
def index():
    redirect(URL('other_action'))
```

**返回除 "200 OK" 之外的 HTTP 状态代码**：

```
def index():
    raise HTTP(404)
```

**返回一个助手**

例如一个表格：

```
def index():
    return FORM(INPUT(_name='test'))
```

（这主要用于 Ajax 回调和组件，请参阅[第 12 章](http://web2py.com/books/default/chapter/29/12)）。

当一个动作返回一个字典时，它可能包含由助手生成的代码，包括基于数据库表的表单或来自工厂的表单，例如：

```
def index():
    return dict(form=SQLFORM.factory(Field('name')).process())
```

（所有由 web2py 生成的表单都使用回发，参见[第 3 章](http://web2py.com/books/default/chapter/29/03#Postbacks)）。

助手对于下载和流式传输也很有用。例如，脚手架应用程序中定义的“下载”操作使用`response.download`帮助程序下载上传的文件：

```
def download():
    return response.download(request, db)
```

查看[响应](http://web2py.com/books/default/chapter/29/04/the-core#response_download)以获取更多详细信息。

`response.stream`辅助方法允许提供任意内容：

```
def index():
    return response.stream('/tmp/foo.bar', request=request, attachment=True)
```

查看[响应](http://web2py.com/books/default/chapter/29/04/the-core#response_stream)以获取更多详细信息。



### 调度





web2py 映射以下形式的 URL：

```
http://127.0.0.1:8000/a/c/f.html
```

`f()`到应用程序“a”中控制器“c.py”中的函数。如果`f`不存在，则 web2py 默认为`index`控制器功能。如果`c`不存在，web2py 默认为“default.py”控制器，如果`a`不存在，web2py 默认为`init`应用程序。如果没有`init`应用程序，web2py 会尝试运行该`welcome`应用程序。这在下图中示意性地显示：



![图片](http://web2py.com/books/default/image/29/en5700.png)



默认情况下，任何新请求也会创建一个新会话。此外，会向客户端浏览器返回会话 cookie 以跟踪会话。

扩展`.html`是可选的；`.html`被假定为默认值。扩展决定了呈现控制器函数输出的视图的扩展`f()`。它允许以多种格式（html、xml、json、rss 等）提供相同的内容。

> 带参数或以双下划线开头的函数不会公开，只能由其他函数调用。

以下形式的 URL 有一个例外：

```
http://127.0.0.1:8000/a/static/filename
```

没有称为“静态”的控制器。web2py 将此解释为对应用程序“a”的子文件夹“static”中名为“filename”的文件的请求。



下载静态文件时，web2py 不会创建会话，也不会发出 cookie 或执行模型。web2py 始终以 1MB 的块流式传输静态文件，并在客户端发送对文件子集的 RANGE 请求时发送 PARTIAL CONTENT。



web2py 还支持 IF_MODIFIED_SINCE 协议，如果文件已经存储在浏览器的缓存中并且文件自该版本以来没有更改，则不会发送文件。

当链接到静态文件夹中的音频或视频文件时，如果您想强制浏览器下载文件而不是通过媒体播放器流式传输音频/视频，请添加`?attachment`到 URL。这告诉 web2py 将`Content-Disposition`HTTP 响应的标头设置为“附件”。例如：

```
<a href="/app/static/my_audio_file.mp3?attachment">Download</a>
```

单击上述链接时，浏览器将提示用户下载 MP3 文件，而不是立即流式传输音频。（正如在[response](http://web2py.com/books/default/chapter/29/04/the-core#response_headers)中所讨论的，您还可以通过将 a `dict`of 标头名称及其值分配给 来直接设置 HTTP 响应标头`response.headers`。）



web2py 映射表单的 GET/POST 请求：



```
http://127.0.0.1:8000/a/c/f.html/x/y/z?p=1&q=2
```

在`f`application 的控制器“c.py”中运行`a`，并将 URL 参数存储在`request`变量中，如下所示：

```
request.args = ['x', 'y', 'z']
```

和：

```
request.vars = {'p': 1, 'q': 2}
```

和：

```
request.application = 'a'
request.controller = 'c'
request.function = 'f'
```

在上面的示例中，两者`request.args[i]`和`request.args(i)`都可以用于检索 的第 i 个元素`request.args`，但是如果列表没有这样的索引，则前者会引发异常，而后者在这种情况下会返回 None 。

```
request.url
```

存储当前请求的完整 URL（不包括 GET 变量）。





```
request.ajax
```

默认为 False，但如果 web2py 确定该操作由 Ajax 请求调用，则为 True。

如果请求是 Ajax 请求并且由 web2py 组件发起，则可以在以下位置找到组件的名称：

```
request.cid
```

[第 12 章](http://web2py.com/books/default/chapter/29/12)将更详细地讨论组件。



如果 HTTP 请求是 GET，则`request.env.request_method`设置为“GET”；如果是 POST，`request.env.request_method`则设置为“POST”。URL 查询变量存储在`request.get_vars`. `request.post_vars`包含传递到请求正文中的所有参数（通常是 POST、PUT 或 DELETE）。`request.vars`存储字典包含它们（`get_vars`并被合并`post_vars`）



web2py 将 WSGI 和 web2py 环境变量存储在 中`request.env`，例如：

```
request.env.path_info = 'a/c/f'
```

和 HTTP 标头到环境变量中，例如：

```
request.env.http_host = '127.0.0.1:8000'
```

> 请注意，web2py 会验证所有 URL 以防止目录遍历攻击。

URL 只允许包含字母数字字符、下划线和斜杠；`args`可能包含不连续的点。在验证之前，空格被下划线替换。如果 URL 语法无效，web2py 会返回 HTTP 400 错误消息[ [http-w](http://web2py.com/books/default/reference/29/http-w) ] [ [http-o](http://web2py.com/books/default/reference/29/http-o) ]。

如果 URL 对应于对静态文件的请求，web2py 只需读取并返回（流式传输）请求的文件。

如果 URL 不请求静态文件，web2py 会按以下顺序处理请求：

- 解析 cookie。
- 创建执行函数的环境。
- 初始化`request`, `response`, `cache`.
- 打开现有的`session`或创建一个新的。
- 执行属于所请求应用程序的模型。
- 执行请求的控制器动作函数。
- 如果函数返回字典，则执行关联的视图。
- 成功时，提交所有打开的事务。
- 保存会话。
- 返回一个 HTTP 响应。

请注意，控制器和视图在同一环境的不同副本中执行；因此，视图看不到控制器，但它看到了模型，并且看到了控制器动作函数返回的变量。

如果引发异常（HTTP 除外），web2py 会执行以下操作：

- 将回溯存储在错误文件中并为其分配票证号。
- 回滚所有打开的数据库事务。
- 返回报告票号的错误页面。

如果异常是`HTTP`异常，则假定这是预期的行为（例如，`HTTP`重定向），并且所有打开的数据库事务都已提交。之后的行为由`HTTP`异常本身指定。`HTTP`异常类不是标准的 Python 异常；它由 web2py 定义。



### 核心库

web2py 库作为全局对象公开给用户应用程序，例如（`request`、`response`、`session`、`cache`）、类（helpers、validators、DAL API）和函数（`T`和`redirect`）。

这些对象在以下核心文件中定义：

```
web2py.py
gluon/__init__.py    gluon/highlight.py   gluon/restricted.py  gluon/streamer.py
gluon/admin.py       gluon/html.py        gluon/rewrite.py     gluon/template.py
gluon/cache.py       gluon/http.py        gluon/rocket.py      gluon/storage.py
gluon/cfs.py         gluon/import_all.py  gluon/sanitizer.py   gluon/tools.py
gluon/compileapp.py  gluon/languages.py   gluon/serializers.py gluon/utils.py
gluon/contenttype.py gluon/main.py        gluon/settings.py    gluon/validators.py
gluon/dal.py         gluon/myregex.py     gluon/shell.py       gluon/widget.py
gluon/decoder.py     gluon/newcron.py     gluon/sql.py         gluon/winservice.py
gluon/fileutils.py   gluon/portalocker.py gluon/sqlhtml.py     gluon/xmlrpc.py
gluon/globals.py     gluon/reserved_sql_keywords.py
```

> 请注意，其中许多模块，特别是`dal`（数据库抽象层）、`template`（模板语言）、`rocket`（Web 服务器）和`html`（帮助程序）没有依赖关系，可以在 web2py 之外使用。

web2py 附带的 tar gzipped 脚手架应用程序是

```
welcome.w2p
```

这是在安装时创建并在升级时覆盖。

> 第一次启动 web2py 时，会创建两个新文件夹：deposit和applications。存款文件夹用作安装和卸载应用程序的临时存储。
>
> 第一次启动 web2py 并在升级后，“welcome”应用程序被压缩到“welcome.w2p”文件中，用作脚手架应用程序。

当 web2py 升级时，它带有一个名为“NEWINSTALL”的文件。如果 web2py 找到这个文件，它就知道升级已执行，因此它会删除该文件并创建一个新的“welcome.w2p”。

当前的 web2py 版本存储在文件“VERSION”中，它遵循标准语义版本控制符号，其中构建 ID 是构建时间戳。

web2py 单元测试在

```
gluon/tests/
```

有用于连接各种 Web 服务器的处理程序：

```
cgihandler.py       # discouraged
gaehandler.py       # for Google App Engine
fcgihandler.py      # for FastCGI
wsgihandler.py      # for WSGI
isapiwsgihandler.py # for IIS
modpythonhandler.py # deprecated
```

（“fcgihandler”调用由 Allan Saddi 开发的“gluon/contrib/gateways/fcgi.py”）和

```
anyserver.py
```

这是一个与许多不同的 Web 服务器交互的脚本，在[第 13 章](http://web2py.com/books/default/chapter/29/13#anyserver-py)中有描述。

“examples”目录下有三个示例文件：

```
options_std.py
routes.parametric.example.py
routes.patterns.example.py
```

它们都应该被复制到根目录（web2py.py 或 web2py.exe 所在的位置）并根据您自己的喜好进行编辑。前者是一个可选配置文件，可以通过`-L`选项传递给 web2py.py。第二个是 URL 映射文件的示例。它在重命名“routes.py”时自动加载。第三种是 URL 映射的替代语法，也可以重命名（或复制到）“routes.py”。

文件

```
app.example.yaml
queue.example.yaml
```

是用于在 Google App Engine 上部署的示例配置文件。[您可以在第 13 章和](http://web2py.com/books/default/chapter/29/13#Deploying-on-Google-App-Engine)Google 文档页面上阅读更多关于它们的信息。

还有一些额外的库，一些是由第三方开发的：

**由 Mark Pilgrim 编写的feedparser** [ [feedparser](http://web2py.com/books/default/reference/29/feedparser) ] 用于阅读 RSS 和 Atom 提要：

```
gluon/contrib/__init__.py
gluon/contrib/feedparser.py
```

**Trent Mick 的markdown2** [ [markdown2](http://web2py.com/books/default/reference/29/markdown2) ] 用于 wiki 标记：

```
gluon/contrib/markdown/__init__.py
gluon/contrib/markdown/markdown2.py
```

**markmin**标记：

```
gluon/contrib/markmin
```

（有关更多信息，请参见[MARKMIN 语法](http://web2py.com/books/default/chapter/29/05#markmin_syntax)）

**fpdf**创建了我的 Mariano Reingart 用于生成 PDF 文档：

```
gluon/contrib/fpdf
```

这在本书中没有记录，但它在[https://github.com/reingart/pyfpdf托管和记录](https://github.com/reingart/pyfpdf)

**pysimplesoap**是由 Mariano Reingart 创建的轻量级 SOAP 服务器实现：

```
gluon/contrib/pysimplesoap/
```

**simplejsonrpc**是一个轻量级 JSON-RPC 客户端，也是由 Mariano Reingart 创建的：



```
gluon/contrib/simplejsonrpc.py
```

**memcache** [ [memcache](http://web2py.com/books/default/reference/29/memcache) ] Evan Martin 的 Python API：

```
gluon/contrib/memcache/__init__.py
gluon/contrib/memcache/memcache.py
```

**redis_cache**

是一个在redis数据库中存储缓存的模块：



```
gluon/contrib/redis_cache.py
```

**gql**，DAL 到 Google App Engine 的端口：

```
gluon/contrib/gql.py
```

**memdb**，位于 memcache 之上的 DAL 端口：

```
gluon/contrib/memdb.py
```

**gae_memcache**是在 Google App Engine 上使用 memcache 的 API：

```
gluon/contrib/gae_memcache.py
```

**pyrtf** [ [pyrtf](http://web2py.com/books/default/reference/29/pyrtf) ] 用于生成富文本格式 (RTF) 文档，由 Simon Cusack 开发并由 Grant Edwards 修订：

```
gluon/contrib/pyrtf/
```

**PyRSS2Gen** [ [pyrss2gen](http://web2py.com/books/default/reference/29/pyrss2gen) ] 由 Dalke Scientific Software 开发，用于生成 RSS 提要：

```
gluon/contrib/rss2.py
```

**Bob Ippolito 的simplejson** [ [simplejson](http://web2py.com/books/default/reference/29/simplejson) ] ，用于解析和编写 JSON 对象的标准库：

```
gluon/contrib/simplejson/
```

**谷歌钱包** [ [googlewallet](http://web2py.com/books/default/reference/29/googlewallet) ]提供“立即支付”按钮，将谷歌链接为支付处理器：

```
gluon/contrib/google_wallet.py
```

**Stripe.com** [ [stripe](http://web2py.com/books/default/reference/29/stripe) ]提供了一个简单的 API 来接受信用卡付款：

```
gluon/contrib/stripe.py
```

**AuthorizeNet** [ [authorizenet](http://web2py.com/books/default/reference/29/authorizenet) ] 提供 API 以通过 Authorize.net 网络接受信用卡付款

```
gluon/contrib/AuthorizeNet.py
```

**Dowcommerce** [ [dowcommerce](http://web2py.com/books/default/reference/29/dowcommerce) ]信用卡处理 API：

```
gluon/contrib/DowCommerce.py
```

**PaymentTech**信用卡处理 API：

```
gluon/contrib/paymentech.py
```

由 Chris AtLee 创建的**PAM** [ [PAM](http://web2py.com/books/default/reference/29/PAM) ]身份验证 API：

```
gluon/contrib/pam.py
```

一个贝叶斯分类器，用虚拟数据填充数据库以进行测试：

```
gluon/contrib/populate.py
```

**在Heroku.com**上运行的带有 API 的文件：



```
gluon/contrib/heroku.py
```

当 web2py 作为服务运行时，允许与 Windows 中的任务栏交互的文件：

```
gluon/contrib/taskbar_widget.py
```

用于身份验证的可选**login_methods和 login_forms：**

```
gluon/contrib/login_methods/__init__.py
gluon/contrib/login_methods/basic_auth.py
gluon/contrib/login_methods/browserid_account.py
gluon/contrib/login_methods/cas_auth.py
gluon/contrib/login_methods/dropbox_account.py
gluon/contrib/login_methods/email_auth.py
gluon/contrib/login_methods/extended_login_form.py
gluon/contrib/login_methods/gae_google_account.py
gluon/contrib/login_methods/ldap_auth.py
gluon/contrib/login_methods/linkedin_account.py
gluon/contrib/login_methods/loginza.py
gluon/contrib/login_methods/oauth10a_account.py
gluon/contrib/login_methods/oauth20_account.py
gluon/contrib/login_methods/oneall_account.py
gluon/contrib/login_methods/openid_auth.py
gluon/contrib/login_methods/pam_auth.py
gluon/contrib/login_methods/rpx_account.py
gluon/contrib/login_methods/x509_auth.py
```

web2py 还包含一个包含有用脚本的文件夹，包括

```
scripts/setup-web2py-fedora.sh
scripts/setup-web2py-ubuntu.sh
scripts/setup-web2py-nginx-uwsgi-ubuntu.sh
scripts/setup-web2py-heroku.sh
scripts/更新-web2py.sh
scripts/make_min_web2py.py
...
scripts/sessions2trash.py
scripts/sync_languages.py
scripts/tickets2db.py
scripts/tickets2email.py
...
scripts/extract_mysql_models.py
scripts/extract_pgsql_models.py
...
scripts/access.wsgi
scripts/cpdb.py
```

它们特别有用，`setup-web2py-*`因为它们尝试从头开始完整安装和设置 web2py 生产环境。其中一些在[第 14 章](http://web2py.com/books/default/chapter/29/14)中进行了讨论，但它们都包含一个文档字符串，其中解释了它们的目的和用法。

最后，web2py 包含了构建二进制发行版所需的这些文件。

```
Makefile
setup_exe.py
setup_app.py
```

**这些分别是py2exe**和**py2app**的设置脚本，它们只需要构建 web2py 的二进制发行版。您永远不需要运行它们。

web2py 应用程序包含附加文件，尤其是第三方 JavaScript 库，例如 jQuery、日历和 Codemirror。他们的作者在文件本身中得到承认。



### 应用开发

在 web2py 中开发的应用程序由以下部分组成：

- **模型**将数据表示为数据库表和表之间的关系。
- **控制器**描述应用程序逻辑和工作流程。
- **视图**描述了如何使用 HTML 和 JavaScript 将数据呈现给用户。
- **语言**描述了如何将应用程序中的字符串翻译成各种支持的语言。
- **静态**文件不需要处理（例如图像、CSS 样式表等）。
- **关于**和**许可**文件是不言自明的。
- **errors**存储应用程序生成的错误报告。
- **会话**存储与每个特定用户相关的信息。
- **数据库**存储 SQLite 数据库和其他表信息。
- **缓存**存储缓存的应用程序项。
- **modules**是其他可选的 Python 模块。
- **私人**文件由控制器访问，但不能由开发人员直接访问。
- **上传**文件由模型访问，但开发人员不能直接访问（例如，应用程序用户上传的文件）。
- **tests**是一个用于存储测试脚本、fixture 和 mock 的目录。

模型、视图、控制器、语言和静态文件可通过 Web 管理 [设计] 界面访问。关于、README 和错误也可以通过管理界面通过相应的菜单项进行访问。应用程序可以访问会话、缓存、模块和私有文件，但不能通过管理界面访问。

一切都整齐地组织在一个清晰的目录结构中，为每个安装的 web2py 应用程序复制，尽管用户不需要直接访问文件系统：





```
__init__.py  ABOUT        LICENSE    models    views
controllers  modules      private    tests     cron
cache        errors       upload     sessions  static
```

“__init__.py”是一个空文件，它是允许 Python（和 web2py）导入`modules`目录中的模块所必需的。

请注意，**管理**应用程序只是为服务器文件系统上的 web2py 应用程序提供了一个 Web 界面。也可以从命令行或您喜欢的文本编辑器/IDE 创建和开发 web2py 应用程序；您不必使用浏览器**管理**界面。可以通过在“applications/newapp/”下复制上述目录结构来手动创建新应用程序（或者简单地将`welcome.w2p`文件解压缩到新应用程序目录中）。也可以从命令行创建和编辑应用程序文件，而无需使用 Web**管理**界面。



### API

模型、控制器和视图在已经为我们导入以下对象的环境中执行：

**全局对象：**



```
request, response, session, cache
```

**国际化：**



```
T
```

**导航：**



```
redirect, HTTP
```

**帮手：**



```
XML, URL, BEAUTIFY

A, ASSIGNJS, B, BODY, BR, BUTTON, CENTER, CODE, COL, COLGROUP,
DIV, EM, EMBED, FIELDSET, FORM, H1, H2, H3, H4, H5, H6,
HEAD, HR, HTML, I, IFRAME, IMG, INPUT, LABEL, LEGEND,
LI, LINK, OL, UL, META, OBJECT, OPTION, P, PRE,
SCRIPT, OPTGROUP, SELECT, SPAN, STRONG, STYLE,
TABLE, TAG, TD, TEXTAREA, TH, THEAD, TBODY, TFOOT,
TITLE, TR, TT, XHTML, xmlescape, embed64

CAT, MARKMIN, MENU, ON
```

**表格和表格**

```
SQLFORM（SQLFORM.factory、SQLFORM.grid、SQLFORM.smartgrid）
```

**验证器：**



```
CLEANUP, CRYPT, IS_ALPHANUMERIC, IS_DATE_IN_RANGE, IS_DATE,
IS_DATETIME_IN_RANGE, IS_DATETIME, IS_DECIMAL_IN_RANGE,
IS_EMAIL, IS_EMPTY_OR, IS_EXPR, IS_FLOAT_IN_RANGE, IS_IMAGE,
IS_IN_DB, IS_IN_SET, IS_INT_IN_RANGE, IS_IPV4, IS_LENGTH,
IS_LIST_OF, IS_LOWER, IS_MATCH, IS_EQUAL_TO, IS_NOT_EMPTY,
IS_NOT_IN_DB, IS_NULL_OR, IS_SLUG, IS_STRONG, IS_TIME,
IS_UPLOAD_FILENAME, IS_UPPER, IS_URL
```

**数据库：**



```
DAL, Field
```

为了向后兼容`SQLDB=DAL`和`SQLField=Field`. 我们鼓励您使用新语法`DAL`and`Field`代替旧语法。

其他对象和模块在库中定义，但它们不会自动导入，因为它们不经常使用。

web2py 执行环境中的核心 API 实体是`request`、`response`、`session`、`cache`、`URL`、和`HTTP`，和 将在下面讨论。`redirect``T`

一些对象和函数，包括**Auth**、**Crud**和**Service**，在“gluon/tools.py”中定义，需要根据需要导入：

```
from gluon.tools import Auth, Crud, Service
```

它们被导入`db.py`到脚手架应用程序中。



### 从 Python 模块访问 API

您的模型或控制器可能会导入 Python 模块。这些通常是您存储在应用程序模块目录中的 Python 文件。他们可能需要使用一些 web2py API。这样做的方法是导入它们：

```
from gluon import ...
```

事实上，任何 Python 模块，即使不是由 web2py 应用程序导入，只要 web2py 在`sys.path`.





#### 使用当前对象与模块共享全局范围

不过，有一个警告。Web2py 定义了一些全局对象（请求、响应、会话、缓存、T），这些对象只有在 HTTP 请求存在（或被伪造）时才能存在。因此，只有从应用程序调用模块才能访问它们。由于这个原因，它们被放置在一个名为 的容器中`current`，该容器是一个线程本地对象。这是一个例子。

创建一个模块“/myapp/modules/mytest.py”，其中包含：

```
from gluon import current
def ip(): return current.request.client
```

现在从“myapp”中的控制器你可以做

```
import mytest
def index():
    return "Your ip is " + mytest.ip()
```

注意几点：

- `import mytest`首先在当前应用的模块文件夹中查找模块，然后在`sys.path`. 因此，应用级模块总是优先于 Python 模块。这允许不同的应用程序附带不同版本的模块，而不会发生冲突。
- 不同的用户可以同时调用同一个动作`index`，调用模块中的函数，但不存在冲突，因为`current.request`是不同线程中的不同对象。请注意不要访问`current.request`模块中的函数或类之外（即顶层）。
- `import mytest`是`from applications.appname.modules import mytest`. 使用较长的语法，可以从其他应用程序导入模块。

为了与正常的 Python 行为保持一致，默认情况下 web2py 在进行更改时不会重新加载模块。然而，这是可以改变的。要打开模块的自动重新加载功能，请使用以下`track_changes`功能（通常在模型文件中，在任何导入之前）：

```
from gluon.custom_import import track_changes; track_changes(True)
```

从现在开始，每次导入模块时，导入器都会检查 Python 源文件（.py）是否发生了变化。如果它已经改变，模块将被重新加载。

> 不要在模块本身中调用 track_changes。

跟踪更改仅跟踪存储在应用程序中的模块的更改。导入的模块`current`可以访问：

- `current.request`
- `current.response`
- `current.session`
- `current.cache`
- `current.T`

以及您的应用程序选择存储在 current.xml 中的任何其他变量。例如一个模型可以做

```
auth = Auth(db)
from gluon import current
current.auth = auth
current.db = db #not needed in this case but useful
```

现在所有导入的模块都可以访问`current.auth`.

`current`并`import`创建一个强大的机制来为您的应用程序构建可扩展和可重用的模块。



#### 警告！不要在模块的全局范围内使用当前对象

谨防！给定`from gluon import current`，使用`current.request`和任何其他线程本地对象都是正确的，但永远不应该将它们分配给模块中的全局变量，例如在

```
request = current.request # WRONG! DANGER!
```

也不应该使用 current 来分配类属性：

```
class MyClass:
    request = current.request # WRONG! DANGER!
```

这是因为必须在运行时提取线程本地对象。而是在第一次导入模型时只定义一次全局变量。

相反，在函数内部分配。

```
from gluon import current
...
def a_module_function():
    db = current.db  # assuming you assigned current.db = db in the model db.py
   ...
```

另一个警告与缓存有关。您不能使用该`cache`对象来装饰模块中的功能，因为它不会按预期运行。为了`f`在模块中缓存函数，您必须使用`lazy_cache`：

```
from gluon.cache import lazy_cache

@lazy_cache('key', time_expire=60, cache_model='ram')
def f(a, b, c): ....
```

请注意，密钥是用户定义的，但必须与函数唯一关联。如果省略 web2py 将自动确定一个键。



### `request`





该`request`对象是无处不在的 web2py 类的一个实例，该类被称为`gluon.storage.Storage`，它扩展了 Python`dict`类。它基本上是一个字典，但项目值也可以作为属性访问：

```
request.vars
```

是相同的：

```
request['vars']
```

与字典不同，如果属性（或键）不存在，它不会引发异常。相反，它返回`None`.

> 创建自己的存储对象有时很有用。你可以这样做：
>
> ```
> from gluon.storage import Storage
> my_storage = Storage()  # empty storage object
> my_other_storage = Storage(dict(a=1, b=2))  # convert dictionary to Storage
> ```

`request`具有以下项目/属性，其中一些也是`Storage`该类的实例：

- `request.cookies`:`Cookie.SimpleCookie()`包含随 HTTP 请求传递的 cookie 的对象。它就像一个 cookie 字典。每个 cookie 都是一个 Morsel 对象[ [morsel](http://web2py.com/books/default/reference/29/morsel) ]。

- `request.env`:`Storage`包含传递给控制器的环境变量的对象，包括来自 HTTP 请求的 HTTP 标头变量和标准 WSGI 参数。环境变量全部转为小写，点转为下划线便于记忆。

- `request.application`：请求的应用程序的名称。

- `request.controller`：请求的控制器的名称。

- `request.function`: 请求函数的名称。

- `request.extension`: 请求动作的扩展。它默认为“html”。如果控制器函数返回一个字典并且没有指定一个视图，这用于确定将呈现字典的视图文件的扩展名（从`request.env.path_info`.

- `request.folder`：应用程序目录。例如如果应用程序是“welcome”，`request.folder`则设置为绝对路径“/path/to/welcome”。在您的程序中，您应该始终使用此变量和`os.path.join`函数来构建您需要访问的文件的路径。尽管 web2py 总是使用绝对路径，但永远不要显式更改当前工作文件夹（无论是什么）是一个很好的规则，因为这不是线程安全的做法。

- `request.now`:`datetime.datetime`存储当前请求的日期时间的对象。

- `request.utcnow`:`datetime.datetime`存储当前请求的 UTC 日期时间的对象。

- `request.args`: 控制器函数名后面的 URL 路径组件列表；相当于`request.env.path_info.split('/')[3:]`

- `request.vars`: 一个`gluon.storage.Storage`包含所有请求参数的对象。

- `request.get_vars`: 一个`gluon.storage.Storage`只包含传递给查询字符串的参数的对象（请求`/a/c/f?var1=1&var2=2`将以 结尾`{var1: "1", var2: "2"}`）

- `request.post_vars`: 一个`gluon.storage.Storage`只包含传入请求体的参数的对象（通常在 POST、PUT、DELETE 请求中）。

- `request.client`：客户端的 IP 地址，由（如果存在）`request.env.http_x_forwarded_for`或`request.env.remote_addr`其他方式确定。虽然这很有用，但它不应该被信任，因为它`http_x_forwarded_for`可以被欺骗。

- `request.is_local`：`True`如果客户端是本地主机，`False`否则。如果代理支持，应该在代理后面工作`http_x_forwarded_for`。

- `request.is_https`:`True`如果请求使用的是 HTTPS 协议，`False`否则。

- `request.body`: 包含 HTTP 请求正文的只读文件流。这会自动解析以获取`request.post_vars`然后倒带。可以用`request.body.read()`.

- `request.ajax`如果函数是通过 Ajax 请求调用的，则为 True。

- `request.cid`是`id`生成 Ajax 请求的组件（如果有）。[您可以在第 12 章](http://web2py.com/books/default/chapter/29/12)中阅读有关组件的更多信息。

- `request.requires_https()`如果请求不是通过 HTTPS，则阻止进一步的代码执行，并通过 HTTPS 将访问者重定向到当前页面。

- `request.restful`这是一个新的非常有用的装饰器，可用于通过分隔 GET/POST/PUT/DELETE 请求来更改 web2py 操作的默认行为。它将在[第 10 章](http://web2py.com/books/default/chapter/29/10#Restful-Web-Services)中详细讨论。

- ```
  request.user_agent()
  ```

  从客户端解析 user_agent 字段，并以字典的形式返回信息。检测移动设备很有用。它使用由 Ross Peoples 创建的“gluon/contrib/user_agent_parser.py”。要查看它的作用，请尝试在视图中嵌入以下代码：

  ```
  {{=BEAUTIFY(request.user_agent())}}
  ```

- `request.global_settings` 包含 web2py 系统范围的设置。它们是自动设置的，您不应更改它们。例如`request.global_settings.gluon_parent`包含 web2py 文件夹的完整路径，`request.global_settings.is_pypy`确定 web2py 是否在 PyPy 上运行。

- `request.wsgi`是一个钩子，允许您从内部操作调用第三方 WSGI 应用程序

后者包括：

- `request.wsgi.environ`
- `request.wsgi.start_response`
- `request.wsgi.middleware`

它们的用法将在本章末尾的[WSGI](http://web2py.com/books/default/chapter/29/04/the-core#WSGI)部分讨论。

例如，典型系统上的以下调用：

```
http://127.0.0.1:8000/examples/default/status/x/y/z?p=1&q=2
```

产生以下`request`对象：



| **多变的**                         | **价值**                               |
| ---------------------------------- | -------------------------------------- |
| `request.application`              | `examples`                             |
| `request.controller`               | `default`                              |
| `request.function`                 | `status`                               |
| `request.extension`                | `html`                                 |
| `request.view`                     | `status`                               |
| `request.folder`                   | `applications/examples/`               |
| `request.args`                     | `['x', 'y', 'z']`                      |
| `request.vars`                     | `<Storage {'p': 1, 'q': 2}>`           |
| `request.get_vars`                 | `<Storage {'p': 1, 'q': 2}>`           |
| `request.post_vars`                | `<Storage {}>`                         |
| `request.is_local`                 | `False`                                |
| `request.is_https`                 | `False`                                |
| `request.ajax`                     | `False`                                |
| `request.cid`                      | `None`                                 |
| `request.wsgi`                     | `<hook>`                               |
| `request.env.content_length`       | `0`                                    |
| `request.env.content_type`         |                                        |
| `request.env.http_accept`          | `text/xml,text/html;`                  |
| `request.env.http_accept_encoding` | `gzip, deflate`                        |
| `request.env.http_accept_language` | `en`                                   |
| `request.env.http_cookie`          | `session_id_examples=127.0.0.1.119725` |
| `request.env.http_host`            | `127.0.0.1:8000`                       |
| `request.env.http_referer`         | `http://web2py.com/`                   |
| `request.env.http_user_agent`      | `Mozilla/5.0`                          |
| `request.env.path_info`            | `/examples/simple_examples/status`     |
| `request.env.query_string`         | `p=1&q=2`                              |
| `request.env.remote_addr`          | `127.0.0.1`                            |
| `request.env.request_method`       | `GET`                                  |
| `request.env.script_name`          |                                        |
| `request.env.server_name`          | `127.0.0.1`                            |
| `request.env.server_port`          | `8000`                                 |
| `request.env.server_protocol`      | `HTTP/1.1`                             |
| `request.env.server_software`      | `Rocket 1.2.6`                         |
| `request.env.web2py_path`          | `/Users/mdipierro/web2py`              |
| `request.env.web2py_version`       | `Version 2.4.1`                        |
| `request.env.wsgi_errors`          | `<open file, mode 'w' at >`            |
| `request.env.wsgi_input`           |                                        |
| `request.env.wsgi_url_scheme`      | `http`                                 |

实际定义哪些环境变量取决于 Web 服务器。这里我们假设内置 Rocket wsgi 服务器。使用 Apache Web 服务器时，变量集没有太大不同。

这些`request.env.http_*`变量是从请求 HTTP 标头中解析的。

这些`request.env.web2py_*`变量不是从 web 服务器环境中解析出来的，而是由 web2py 创建的，以防您的应用程序需要知道 web2py 的位置和版本，以及它是否在 Google App Engine 上运行（因为可能需要特定的优化）。

还要注意`request.env.wsgi_*`变量。它们特定于 wsgi 适配器。



### `response`

`response`是`Storage`该类的另一个实例。它包含以下内容：

- `response.body`: `StringIO`web2py 将输出页面正文写入其中的对象。永远不要改变这个变量。

- `response.cookies`: 类似于`request.cookies`，但后者包含从客户端发送到服务器的 cookie，前者包含服务器发送到客户端的 cookie。会话 cookie 是自动处理的。

- `response.download(request, db)`:一种用于实现允许下载上传文件的控制器功能的方法。`response.download`期望最后一个`arg`是`request.args`编码的文件名（即，在上传时生成并存储在上传字段中的文件名）。它从编码文件名中提取上传字段名和表名以及原始文件名。`response.download`有两个可选参数：`chunk_size`设置分块流的字节大小（默认为 64K），并`attachments`确定下载的文件是否应被视为附件（默认为`True`）。注意，`response.download`专门用于下载与`db`上传字段相关的文件。用于`response.stream`（见下文）其他类型的文件下载和流式传输。另外，请注意，没有必要使用`response.download`访问上传到 /static 文件夹的文件——静态文件可以（通常应该）通过 URL 直接访问（例如，/app/static/files/myfile.pdf）。

- `response.files`：页面所需的`.css`、、、和文件的列表。它们将通过包含的“web2py_ajax.html”自动链接到标准“layout.html”的头部。要包含新的 CSS、JS、COFFEE 或 LESS 文件，只需将其附加到此列表即可。它将处理重复项。顺序很重要。`.js``.coffee``.less`

- `response.include_files()`生成包含所有的 html 头标签`response.files`（在“views/web2py_ajax.html”中使用）。

- `response.flash`：可能包含在视图中的可选参数。通常用于通知用户发生的事情。

- `response.headers`:a`dict`用于 HTTP 响应标头。Web2py 默认设置一些头文件，包括“Content-Length”、“Content-Type”和“X-Powered-By”（设置等于 web2py）。Web2py 还设置“Cache-Control”、“Expires”和“Pragma”标头以防止客户端缓存，但启用了客户端缓存的静态文件请求除外。web2py 设置的标头可以被覆盖或删除，并且可以添加新的标头（例如，`response.headers['Cache-Control'] = 'private'`）。您可以通过从 response.headers 字典中删除其键来删除标头，例如`del response.headers['Custom-Header']`，但是 web2py 的默认标头将在返回响应之前重新添加。为了避免这种行为，只需将标头值设置为 None，例如删除默认的 Content-Type 标头，`response.headers['Content-Type'] = None`

- `response.menu`：可能包含在视图中的可选参数，通常用于将导航菜单树传递给视图。它可以由 MENU 助手渲染。

- `response.meta`: 一个包含可选`<meta>`信息的 Storage 对象，例如`response.meta.author`,`.description`和/或`.keywords`. 每个元变量的内容由“views/web2py_ajax.html”中的代码自动放置在适当的`META`标签中，默认情况下包含在“views/layout.html”中。

- `response.include_meta()`生成一个包含所有`response.meta`序列化标题的字符串（在“views/web2py_ajax.html”中使用）。

- `response.postprocessing`：这是一个函数列表，默认为空。这些函数用于在视图呈现输出之前过滤操作输出处的响应对象。它可用于实现对其他模板语言的支持。

- `response.render(view, vars)`：用于在控制器内部显式调用视图的方法。`view`是一个可选参数，它是视图文件的名称，`vars`是传递给视图的命名值的字典。

- `response.session_file`：包含会话的文件流。

- `response.session_file_name`：将保存会话的文件的名称。

- `response.session_id`: 当前会话的 id。它是自动确定的。永远不要改变这个变量。

- `response.session_id_name`：此应用程序的会话 cookie 的名称。永远不要改变这个变量。

- `response.static_version`：静态资产管理的版本号`response.files`。

- `response.static_version_urls`：设置此项以`True`启用静态文件夹的任何链接上的静态资产管理。

- `response.status`：要传递给响应的 HTTP 状态代码整数。默认值为 200（确定）。

- `response.stream(file, chunk_size, request=request, attachment=False, filename=None)`:当控制器返回它时，web2py 将文件内容以 size 块的形式流回客户端`chunk_size`。该`request`参数是使用 HTTP 标头中的块开始所必需的。`file`应该是一个文件路径（为了向后兼容，它也可以是一个打开的文件对象，但不推荐这样做）。如上所述，`response.download`应该用于检索通过上传字段存储的文件。`response.stream`可以用在其他情况下，比如返回一个临时文件或者控制器创建的StringIO对象。如果`attachment`为 True，则 Content-Disposition 标头将设置为“附件”，如果`filename`还提供，它也会添加到 Content-Disposition 标头中（但仅当`attachment`为 True 时）。如果尚未包含在`response.headers`，将自动设置以下响应标头：Content-Type、Content-Length、Cache-Control、Pragma 和 Last-Modified（后三个设置为允许浏览器缓存文件）。要覆盖这些自动标头设置中的任何一个，只需`response.headers`在调用`response.stream`.

- `response.subtitle`：可能包含在视图中的可选参数。它应该包含页面的副标题。

- `response.title`：可能包含在视图中的可选参数。它应该包含页面的标题，并且应该在标题的 html 标题标签内呈现。

- `response.toolbar`: 一种功能，允许您将工具栏嵌入页面以进行调试`{{=response.toolbar()}}`。工具栏显示每个查询的请求、响应、会话变量和数据库访问时间。

- `response._vars`：这个变量只能在视图中访问，不能在操作中访问。它包含操作返回给视图的值。

- ```
  response._caller
  ```

  ：这是一个包装所有动作调用的函数。它默认为标识函数，但可以修改它以捕获特殊类型的异常以进行额外的日志记录；

  ```
  response._caller = lambda f: f()
  ```

- `response.optimize_css`: 可以设置为 "concat,minify,inline" 来连接、缩小和内联 web2py 包含的 CSS 文件。

- `response.optimize_js`: 可以设置为 "concat,minify,inline" 来连接、缩小和内联 web2py 包含的 JavaScript 文件。

- ```
  response.view
  ```

  ：必须呈现页面的视图模板的名称。默认情况下设置为：

  ```
  "%s/%s.%s" % (request.controller, request.function, request.extension)
  ```

  或者，如果找不到上述文件，则

  ```
  "generic.%s" % (request.extension)
  ```

  更改此变量的值以修改与特定操作关联的视图文件。

- `response.delimiters`默认为`('{{','}}')`. 它允许您更改嵌入在视图中的代码的分隔符。

- `response.xmlrpc(request, methods)`：当控制器返回它时，此函数通过 XML-RPC [ [xmlrpc](http://web2py.com/books/default/reference/29/xmlrpc) ]公开方法。该函数已被弃用，因为有更好的机制可用并在[第 10 章](http://web2py.com/books/default/chapter/29/10#XMLRPC)中进行了描述。

- `response.write(text)`：一种将文本写入输出页面正文的方法。

- `response.js`可以包含 Javascript 代码。[当且仅当响应被第 12 章](http://web2py.com/books/default/chapter/29/12#LOAD-and-Client-Server-component-communications)中讨论的 web2py 组件接收到时，才会执行此代码。

- ```
  response.models_to_run
  ```

   

  

  包含选择要运行的模型的正则表达式列表。

  - 默认情况下，它会自动设置为在`/a/c/f`请求时加载 /a/models/*.py、/a/models/c/*.py 和 /a/models/c/f/*.py 文件。例如，您可以设置`response.models_to_run = ['myfolder/']`强制仅执行应用程序`models/myfolder`子文件夹中的模型。
  - 注意：`response.models_to_run`是正则表达式列表，而不是文件路径列表。正则表达式是相对于 models/ 文件夹的，因此任何具有与其中一个正则表达式匹配的相对文件路径的模型文件都将被执行。另请注意，这不会影响任何已评估的模型，因为它们在字母排序顺序中较早。也就是说，如果控制器 orange 的条件模型是 orange/orange_model.py 并将正则表达式设置为 [.*]，则该更改不会影响先前拒绝加载的任何模型，例如模型 apple/apple_model.py ；它与新的正则表达式匹配，但在 orange/orange_model.py 更改正则表达式之前已被评估并拒绝。
  - 这意味着如果您想使用 models_to_run 在控制器之间共享条件模型，请将模型放在最后排序的子目录中，例如 zzz，然后使用正则表达式“zzz”。

  

由于它`response`是一个`gluon.storage.Storage`对象，因此它可用于存储您可能希望传递给视图的其他属性。虽然没有技术限制，但我们的建议是仅存储要由整体布局中的所有页面呈现的变量（“layout.html”）。

无论如何，我们强烈建议坚持这里列出的变量：

```
response.title
response.subtitle
response.flash
response.menu
response.meta.author
response.meta.description
response.meta.keywords
response.meta.*
```

因为这将使您更容易将 web2py 附带的标准“layout.html”文件替换为另一个布局文件，一个使用相同变量集的布局文件。

旧版本的 web2py 用于`response.author`替代其他元属性，`response.meta.author`并且与其他元属性类似。



### `session`





`session`是`Storage`该类的另一个实例。无论存储到`session`例如：

```
session.myvariable = "hello"
```

可以稍后检索：

```
a = session.myvariable
```

只要代码由同一用户在同一会话中执行（前提是用户未删除会话 cookie 且会话未过期）。因为`session`是一个`Storage`对象，试图访问一个尚未设置的属性/键不会引发异常；它反而返回`None`。

会话对象具有三个重要的方法。一个是`forget`：

```
session.forget(response)
```

它告诉 web2py 不要保存会话。这应该在那些经常调用动作并且不需要跟踪用户活动的控制器中使用。`session.forget()`防止会话文件被写入，无论它是否已被修改。`session.forget(response)`另外解锁和关闭会话文件。您很少需要调用此方法，因为会话在未更改时不会保存。但是，如果页面同时发出多个 Ajax 请求，最好调用通过 Ajax 调用的操作`session.forget(response)`（假设操作不需要会话）。否则，每个 Ajax 操作都必须等待前一个操作完成（并解锁会话文件）才能继续，这将减慢页面加载速度。请注意，会话在存储在数据库中时不会被锁定。

另一种方法是：

```
session.secure()
```

它告诉 web2py 将会话 cookie 设置为安全 cookie。如果应用程序通过 https，则应设置此项。通过将会话 cookie 设置为安全，服务器要求浏览器不要将会话 cookie 发送回服务器，除非通过 https 连接。

另一种方法是`connect`。默认情况下，会话存储在文件系统中，会话 cookie 用于存储和检索`session.id`. 使用 connect 方法可以告诉 web2y 将会话存储在数据库或 cookie 中，从而无需访问文件系统进行会话管理。

例如**在数据库中存储会话**：

```
session.connect(request, response, db, masterapp=None)
```

其中`db`是打开的数据库连接的名称（由 DAL 返回）。它告诉 web2py 您要将会话存储在数据库中而不是文件系统中。`session.connect`必须在 之后`db=DAL(...)`，但在任何其他需要会话的逻辑之前，例如，设置`Auth`。

web2py 创建一个表：

```
db.define_table('web2py_session',
                 Field('locked', 'boolean', default=False),
                 Field('client_ip'),
                 Field('created_datetime', 'datetime', default=now),
                 Field('modified_datetime', 'datetime'),
                 Field('unique_key'),
                 Field('session_data', 'text'))
```

并在现场存储 cPickled 会话`session_data`。

默认情况下，选项`masterapp=None`告诉 web2py 尝试`request.application`在正在运行的应用程序中检索名称为 in 的应用程序的现有会话。

如果您希望两个或更多应用程序共享会话，请设置`masterapp`为主应用程序的名称。

要将**会话存储在 cookie 中**，您可以执行以下操作：

```
session.connect(request, response, cookie_key='yoursecret', compression_level=None)
```

这`cookie_key`是一个对称加密密钥。`compression_level`是可选的`zlib`加密级别。

虽然出于可扩展性的原因，通常建议使用 cookie 中的会话，但它们的大小有限。大型会话将导致 cookie 损坏。

您可以随时通过打印 和 系统变量来检查应用程序`request`的状态`session`。`response`一种方法是创建一个专门的操作：

```
def status():
    return dict(request=request, session=session, response=response)
```

在“generic.html”视图中，这是使用`{{=response.toolbar()}}`.



#### 不要在会话中存储用户定义的类

存储在会话中的变量通过序列化在请求之间保留。

在创建请求的执行环境之前检索会话，即在执行任何模型和控制器之前，当尚未定义用户类时。因此用户定义的类不能被腌制。

模块中定义的类也是灰色地带，不应该存放。大多数时候他们工作，但他们可以打破。这是因为，例如，如果您重新启动 Web 服务器并且用户检索会话，这可能会在导入模块之前发生。当 Web 服务器启动一个新的工作进程时，同样的问题。分布式环境中的同样问题。



#### 单独的会话

如果您在文件系统上存储会话并且有很多会话，则文件系统访问可能会成为瓶颈。一种解决方案如下：

```
session.connect(request, response, separate=True)
```

通过设置`separate=True`web2py 将不会将会话存储在“sessions/”文件夹中，而是存储在“sessions/”文件夹的子文件夹中。子文件夹将自动创建。具有相同前缀的会话将位于同一子文件夹中。同样，请注意，必须在任何可能需要会话的逻辑之前调用上述内容。



### `cache`





`cache`是一个在 web2py 执行环境中也可用的全局对象。它有两个属性：

- `cache.ram`：主内存中的应用程序缓存。
- `cache.disk`：磁盘上的应用程序缓存。

`cache`是可调用的，这允许它用作缓存操作和视图的装饰器。

以下示例将`time.ctime()`函数缓存在 RAM 中：

```
def cache_in_ram():
    import time
    t = cache.ram('time', lambda: time.ctime(), time_expire=5)
    return dict(time=t, link=A('click me', _href=request.url))
```

的输出`lambda: time.ctime()`在 RAM 中缓存 5 秒。该字符串`'time'`用作缓存键。

以下示例将`time.ctime()`函数缓存在磁盘上：

```
def cache_on_disk():
    import time
    t = cache.disk('time', lambda: time.ctime(), time_expire=5)
    return dict(time=t, link=A('click me', _href=request.url))
```

的输出`lambda: time.ctime()`在磁盘上缓存 5 秒。

`cache.ram`请注意， and的第二个参数`cache.disk`必须是函数或可调用对象。如果要缓存现有对象而不是函数的输出，可以简单地通过 lambda 函数返回它：

```
cache.ram('myobject', lambda: myobject, time_expire=60*60*24)
```

下一个示例将函数缓存`time.ctime()`到 RAM 和磁盘：

```
def cache_in_ram_and_disk():
    import time
    t = cache.ram('time', lambda: cache.disk('time', lambda: time.ctime(), time_expire=5), time_expire=5)
    return dict(time=t, link=A('click me', _href=request.url))
```

的输出`lambda: time.ctime()`缓存在磁盘上，然后在 RAM 中保存 5 秒。web2py 首先在 RAM 中查找，如果不存在，则在磁盘上查找。如果它不在 RAM 或磁盘上，`lambda: time.ctime()`则执行并更新缓存。这种技术在多处理器环境中很有用。这两次不必相同。

以下示例在 RAM 中缓存控制器函数的输出（但不是视图）：

```
@cache(request.env.path_info, time_expire=5, cache_model=cache.ram)
def cache_controller_in_ram():
    import time
    t = time.ctime()
    return dict(time=t, link=A('click me', _href=request.url))
```

返回的字典`cache_controller_in_ram`在 RAM 中缓存 5 秒。请注意，如果不先序列化，就无法缓存数据库选择的结果。更好的方法是直接使用`select`方法的`cache`参数缓存数据库选择。

以下示例将控制器函数的输出缓存在磁盘上（但不是视图）：

```
@cache(request.env.path_info, time_expire=5, cache_model=cache.disk)
def cache_controller_on_disk():
    import time
    t = time.ctime()
    return dict(time=t, link=A('click to reload', _href=request.url))
```

返回的字典`cache_controller_on_disk` 在磁盘上缓存 5 秒。请记住，web2py 无法缓存包含不可腌制对象的字典。

也可以缓存视图。诀窍是在控制器函数中渲染视图，以便控制器返回一个字符串。这是通过返回我们打算传递给视图的字典`response.render(d)`在哪里来完成的。`d`以下示例将控制器函数的输出缓存在 RAM 中（包括渲染视图）：

```
@cache(request.env.path_info, time_expire=5, cache_model=cache.ram)
def cache_controller_and_view():
    import time
    t = time.ctime()
    d = dict(time=t, link=A('click to reload', _href=request.url))
    return response.render(d)
```

`response.render(d)`将渲染视图作为字符串返回，现在缓存 5 秒。这是最好和最快的缓存方式。

> 我们建议从 web2py 2.4.7+ 开始使用[@ cache.action](http://web2py.com/books/default/chapter/29/04/the-core#cache_action)

注意，`time_expire`用于将当前时间与请求对象上次保存在缓存中的时间进行比较。它不会影响未来的请求。这使得`time_expire`可以在请求对象时动态设置，而不是在保存对象时进行固定。例如：

```
message = cache.ram('message', lambda: 'Hello', time_expire=5)
```

现在，假设在上述调用之后 10 秒进行了以下调用：

```
message = cache.ram('message', lambda: 'Goodbye', time_expire=20)
```

因为`time_expire`在第二次调用中设置为 20 秒，并且自第一次保存消息以来仅过去了 10 秒，因此将从缓存中检索值“Hello”，并且不会使用“Goodbye”进行更新。第`time_expire`一次调用的 5 秒值对第二次调用没有影响。

设置`time_expire=0`（或负值）强制刷新缓存项（因为自上次保存以来经过的时间将始终 > 0），并且设置`time_expire=None`强制检索缓存的值，而不管自保存后经过的时间（如果`time_expire`总是`None`，缓存的项目将有效地永不过期）。

您可以清除一个或多个缓存变量

```
cache.ram.clear(regex='...')
```

where`regex`是一个正则表达式，匹配您要从缓存中删除的所有键。您还可以通过以下方式清除单个项目：

```
cache.ram(key, None)
```

`key`缓存项的键在哪里。

也可以定义其他缓存机制，例如 memcache。Memcache 可通过第 13 章获得，并在[第 13 章](http://web2py.com/books/default/chapter/29/13#Memcache)`gluon.contrib.memcache`中进行了更详细的讨论。

> 缓存时要小心，记住缓存通常是在应用程序级别而不是用户级别。例如，如果您需要缓存用户特定的内容，请选择包含用户 ID 的键。

> 应用程序的管理应用程序允许您查看缓存键（并清除缓存）。从管理员的数据库管理屏幕访问它。





#### `cache.action`

默认情况下，Web2py 假定返回的内容不会被缓存，因为这减少了页面客户端缓存不当的缺点。

例如，当您向用户显示表单或记录列表时，不应缓存网页，因为其他用户可能在您显示的表上插入了新记录。

相反，如果您向用户显示一个内容永远不会更改（或每周更改一次）的 wiki 页面，则存储该页面很有用，但告诉客户该页面不会继续更有用改变。

这是通过与页面一起发送一些特定的标题来完成的：当客户端的浏览器接收到内容时，它会存储在浏览器的缓存中，并且不会再次向您的站点请求。当然，这对于面向公众的网站来说是一个**重大的加速。**

Web2py 2.4.7+ 引入了一个新的`cache.action`装饰器，可以更智能地处理这种情况。`cache.action`可以使用：

- 用于设置智能缓存头
- 相应地缓存结果

> 注意：它会做一个或另一个或**两者**。

缓存视图的主要问题`@cache(request.env.path_info, time_expire=300, cache_model=cache.ram)`是 request.env.path_info 作为键会导致几个问题，例如

1. 不考虑 URL 变量
   - 你缓存了*/app/default/index 的结果？**search=foo***：接下来的 300 秒*/app/default/index? **search=bar****将返回与/app/default/index*完全相同的内容？***search=foo***
2. 不考虑用户
   - 您的用户经常访问一个页面，而您选择缓存它。但是，您使用 request.env.path_info 作为键缓存了*/app/default/index*的结果，因此另一个用户将看到一个不适合他的页面
   - 您为“Bill”缓存了一个页面，但“Bill”从桌面访问了该页面。现在他尝试从他的手机访问它：如果您为移动用户准备了一个不同于标准模板的模板，“Joe”将看不到它
3. 不考虑语言
   - 缓存页面时，如果对某些元素使用 T() ，页面将以固定翻译存储
4. 方法不考虑
   - 缓存页面时，仅当它是 GET 操作的结果时才应缓存它
5. 不考虑状态码
   - 当您第一次缓存页面时，出了点问题，您返回了一个不错的 404 页面。你不想缓存错误^_^

不是让用户编写大量样板代码来处理所有这些问题，而是`cache.action`创建。默认情况下，它会使用智能缓存头让浏览器缓存结果：如果您将缓存模型传递给它，它也会自动找出最佳键，因此可以相应地存储和检索同一页面的不同版本（例如一种用于英语用户，另一种用于西班牙语用户）

它需要几个参数，具有智能默认值：

- time_expire ：通常，默认为 300 秒

- cache_model ：默认为无。**这**意味着

  ```
  @cache.action
  ```

  只会更改默认标头以让客户端的浏览器缓存内容

  

  

  - 如果你通过，例如，`cache.ram`结果也将存储在缓存中

- prefix ：如果你想为自动生成的密钥添加前缀（对于稍后清除它很有用，例如`cache.ram.clear(prefix*)`）

- session ：如果要考虑会话，默认为 False

- vars ：如果你想考虑 URL vars，默认为 True

- lang ：如果你想考虑语言，默认为 True

- user_agent ：如果要考虑用户代理，默认为 False

- public ：如果您希望所有将访问它的用户都使用相同的页面，则默认为 True

- valid_statuses ：默认为无。`@cache.action`将仅缓存使用 GET 方法请求的页面，其状态代码以 1,2 或 3 开头。您可以传递状态代码列表（当您希望使用这些状态缓存页面时，例如 status_codes=[200] 将仅缓存导致 200 状态代码的页面）

- quick ：默认为 None，但您可以传递首字母列表来设置特定功能：

  - Session、Vars **、****Lang**、**U** ser_agent 、**P** **ublic** eg与`@cache.action(time_expire=300, cache_model=cache.ram, quick='SVP')``@cache.action(time_expire=300, cache_model=cache.ram, session=True, vars=True, public=True)`

“考虑”意味着例如**vars ，如果****vars**不同，您想要缓存不同的页面，所以 */app/default/index?* *对于/app/default/index ，* ***search=foo***不会是同一个？***search=bar***某些设置会覆盖其他设置，因此，例如，如果您设置`session=True, public=True`后者将被丢弃。明智地使用它们！



### `URL`

该`URL`函数是 web2py 中最重要的函数之一。它为操作和静态文件生成内部 URL 路径。

这是一个例子：

```
URL('f')
```

映射到

```
/[application]/[controller]/f
```

请注意，`URL`函数的输出取决于当前应用程序的名称、调用控制器和其他参数。web2py 支持 URL 映射和反向 URL 映射。URL 映射允许您重新定义外部 URL 的格式。如果您使用该`URL`函数生成所有内部 URL，则对 URL 映射的添加或更改将防止 web2py 应用程序中的链接断开。

您可以将其他参数传递给`URL`函数，即 URL 路径 (args) 和 URL 查询变量 (vars) 中的额外项：

```
URL('f', args=['x', 'y'], vars=dict(z='t'))
```

映射到

```
/[application]/[controller]/f/x/y?z=t
```

属性由web2py`args`自动解析、解码并最终存储。`request.args`类似地，它们`vars`被解析、解码，然后存储在`request.vars`. `args`并`vars`提供 web2py 与客户端浏览器交换信息的基本机制。

如果 args 仅包含一个元素，则无需将其传递到列表中。

您还可以使用该`URL`函数为其他控制器和其他应用程序中的操作生成 URL：

```
URL('a', 'c', 'f', args=['x', 'y'], vars=dict(z='t'))
```

映射到

```
/a/c/f/x/y?z=t
```

也可以使用命名参数指定应用程序、控制器和函数：

```
URL(a='a', c='c', f='f')
```

如果应用程序名称*a*缺失，则假定为当前应用程序。

```
URL('c', 'f')
```

如果缺少控制器名称*c*，则假定为当前名称。

```
URL('f')
```

除了传递控制器函数的名称，还可以传递函数本身

```
URL(f)
```

由于上述原因，您应该始终使用该`URL`函数为您的应用程序生成静态文件的 URL。静态文件存储在应用程序的`static`子文件夹中（使用管理界面上传时它们会存放在该文件夹中）。web2py 提供了一个虚拟的“静态”控制器，其工作是从`static`子文件夹中检索文件，确定它们的内容类型，并将文件流式传输到客户端。以下示例生成静态文件“image.png”的 URL：

```
URL('static', 'image.png')
```

映射到

```
/[application]/static/image.png
```

如果静态文件位于文件夹内的子`static`文件夹中，您可以将子文件夹包含在文件名中。例如，要生成：

```
/[application]/static/images/icons/arrow.png
```

应该使用：

```
URL('static', 'images/icons/arrow.png')
```

您不需要对`args`and`vars`参数进行编码/转义；这是自动为您完成的。

默认情况下，将当前请求对应的扩展名（可以在 中找到`request.extension`）附加到函数中，除非 request.extension 是默认的 html。这可以通过显式包含扩展作为函数名的一部分`URL(f='name.ext')`或使用扩展参数来覆盖：

```
URL(..., extension='css')
```

当前扩展可以被显式抑制：

```
URL(..., extension=False)
```



#### 绝对网址

默认情况下，`URL`生成相对 URL。但是，您也可以通过指定`scheme`和`host`参数来生成绝对 URL（这很有用，例如，在电子邮件中插入 URL 时）：

```
URL(..., scheme='http', host='www.mysite.com')
```

您只需将参数设置为 ，即可自动包含当前请求的方案和主机`True`。

```
URL(..., scheme=True, host=True)
```

如果需要，该`URL`函数还接受一个`port`参数来指定服务器端口。



#### 数字签名的网址

生成 URL 时，您可以选择对其进行数字签名。这将附加一个`_signature`可由服务器验证的 GET 变量。这可以通过两种方式完成。

您可以将以下参数传递给 URL 函数：

- `hmac_key`: 用于签署 URL 的密钥（字符串）
- `salt`: 一个可选的字符串，用于在签名之前对数据进行加盐
- `hash_vars`: 来自 URL 查询字符串的变量名称的可选列表（即 GET 变量），要包含在签名中。它也可以设置为`True`（默认值）以包括所有变量，或`False`不包括任何变量。

这是一个使用示例：

```
KEY = 'mykey'

def one():
    return dict(link=URL('two', vars=dict(a=123), hmac_key=KEY))

def two():
    if not URL.verify(request, hmac_key=KEY): raise HTTP(403)
    # do something
    return locals()
```

这使得该操作`two`只能通过数字签名的 URL 访问。一个数字签名的 URL 如下所示：

```
/welcome/default/two?a=123&_signature=4981bc70e13866bb60e52a09073560ae822224e9
```

注意，数字签名是通过`URL.verify`函数验证的。`URL.verify`还接受上述`hmac_key`、`salt`和`hash_vars`参数，并且它们的值必须与`URL`创建数字签名时传递给函数的值相匹配，以验证 URL。

数字签名 URL 的第二种更复杂但更常见的用法是与 Auth 结合使用。最好用一个例子来解释：

```
@auth.requires_login()
def one():
    return dict(link=URL('two', vars=dict(a=123), user_signature=True)

@auth.requires_signature()
def two():
    # do something
    return locals()
```

在这种情况下，`hmac_key`它会自动生成并在会话中共享。这允许 action`two`将任何访问控制委托给 action `one`。如果链接生成并签名，则有效；否则不是。如果链接被其他用户盗用，链接将失效。

始终对 Ajax 回调进行数字签名是一种很好的做法。如果您使用 web2py`LOAD`函数，它也有一个`user_signature`可用于此目的的参数：

```
{{=LOAD('default', 'two', vars=dict(a=123), ajax=True, user_signature=True)}}
```



### `HTTP`和`redirect`





web2py 只定义了一个名为`HTTP`. 可以使用以下命令在模型、控制器或视图中的任何位置引发此异常：

```
raise HTTP(400, "my message")
```

它会导致控制流从用户的代码中跳出来，返回到 web2py，并返回如下 HTTP 响应：

```
HTTP/1.1 400 BAD REQUEST
Date: Sat, 05 Jul 2008 19:36:22 GMT
Server: Rocket WSGI Server
Content-Type: text/html
Via: 1.1 127.0.0.1:8000
Connection: close
Transfer-Encoding: chunked

my message
```

的第一个参数`HTTP`是 HTTP 状态码。第二个参数是将作为响应正文返回的字符串。额外的可选命名参数用于构建响应 HTTP 标头。例如：

```
raise HTTP(400, 'my message', test='hello')
```

生成：

```
HTTP/1.1 400 BAD REQUEST
Date: Sat, 05 Jul 2008 19:36:22 GMT
Server: Rocket WSGI Server
Content-Type: text/html
Via: 1.1 127.0.0.1:8000
Connection: close
Transfer-Encoding: chunked
test: hello

my message
```

如果您不想提交打开的数据库事务，请在引发异常之前回滚。

除了`HTTP`导致 web2py 回滚任何打开的数据库事务、记录错误回溯、向访问者发出票证并返回标准错误页面之外的任何异常。

这意味着只能`HTTP`用于跨页控制流。其他异常必须由应用程序捕获，否则它们会被 web2py 出票。

命令：

```
redirect(location)
```

只是一个快捷方式：

```
raise HTTP(303,
           'You are being redirected <a href="%s">here</a>' % location,
           Location='http://www.web2py.com')
```

初始化方法的命名参数`HTTP`被转换为 HTTP 标头指令，在本例中为重定向目标位置。`redirect`接受一个可选的第二个参数，它是重定向的 HTTP 状态代码（默认为 303）。将此号码更改为 307 用于临时重定向或更改为 301 用于永久重定向。

使用重定向最常见的方法是重定向到同一应用程序中的其他页面并（可选）传递参数：

```
redirect(URL('index', args=(1, 2, 3), vars=dict(a='b')))
```

[第 12 章](http://web2py.com/books/default/chapter/29/12#Components-LOAD-and-Ajax)我们讨论 web2py 组件。他们向 web2py 操作发出 Ajax 请求。如果被调用的操作执行重定向，您可能希望 Ajax 请求跟随重定向，或者您可能希望整个页面执行 Ajax 请求重定向。在后一种情况下，您可以设置：

```
redirect(..., client_side=True)
```



### 国际化和多元化`T`





对象`T`是语言翻译器。它构成了 web2py 类的单个全局实例`gluon.language.translator`。所有字符串常量（并且只有字符串常量）都应该用 标记`T`，例如：

```
a = T("hello world")
```

带有 标记的字符串`T`被 web2py 识别为需要语言翻译，它们将在代码（在模型、控制器或视图中）执行时被翻译。如果要翻译的字符串不是常量而是变量，它会在运行时添加到翻译文件中（GAE 除外）以便稍后翻译。

该`T`对象还可以包含插值变量并支持多种等效语法：

```
a = T("hello %s", ('Tim', ))
a = T("hello %(name)s", dict(name='Tim'))
a = T("hello %s") % ('Tim', )
a = T("hello %(name)s") % dict(name='Tim')
```

推荐使用后一种语法，因为它使翻译更容易。第一个字符串根据请求的语言文件进行翻译，并且`name`变量的替换与语言无关。

您可以连接翻译后的字符串和普通字符串：

```
T("blah ") + name + T(" blah")
```

以下代码也是允许的，而且通常更可取：

```
T("blah %(name)s blah", dict(name='Tim'))
```

或替代语法

```
T("blah %(name)s blah") % dict(name='Tim')
```

在这两种情况下，翻译都发生在变量名被替换到“%(name)s”槽之前。不应使用以下替代方法：

```
T("blah %(name)s blah" % dict(name='Tim'))
```

因为翻译会发生在替换之后。



#### 确定语言

请求的语言由 HTTP 标头中的“Accept-Language”字段确定，但可以通过请求特定文件以编程方式覆盖此选择，例如：

```
T.force('it-it')
```

它读取“languages/it-it.py”语言文件。可以通过管理界面创建和编辑语言文件。

您还可以强制使用每个字符串的语言：

```
T("Hello World", language="it-it")
```

> 在请求多种语言的情况下，例如“it-it, fr-ft”，web2py 会尝试定位“it-it.py”和“fr-fr.py”翻译文件。如果请求的文件都不存在，它会尝试使用“it.py”和“fr.py”。如果这些文件不存在，则默认为“default.py”。如果这也不存在，则默认为不翻译。更一般的规则是 web2py 为每种“xx-xy-yy”接受的语言尝试“xx-xy-yy.py”、“xx-xy.py”、“xx.py”、“default.py”试图找到最接近访问者偏好的匹配项。

您可以通过以下方式完全关闭翻译

```
T.force(None)
```

通常，在渲染视图时会延迟评估字符串翻译；因此，`force`不应在视图内调用翻译器方法。

可以通过禁用惰性评估

```
T.lazy = False
```

以这种方式，字符串由`T`操作员根据当前接受或强制的语言立即翻译。

也可以禁用单个字符串的惰性求值：

```
T("Hello World", lazy=False)
```

一个常见的问题如下。原始申请是英文的。假设有一个翻译文件（例如意大利语，“it-it.py”），并且 HTTP 客户端声明它按顺序接受英语 (en) 和意大利语 (it-it)。出现以下不想要的情况：web2py 不知道默认是用英文（en）写的。因此，它更喜欢将所有内容翻译成意大利语（it-it），因为它只找到意大利语翻译文件。如果它没有找到“it-it.py”文件，它将使用默认语言字符串（英语）。

这个问题有两种解决方案：为英语创建一种翻译语言，这将是多余和不必要的，或者更好地告诉 web2py 哪些语言应该使用默认语言字符串（编码到应用程序中的字符串）。这可以通过以下方式完成：

```
T.set_current_languages('en', 'en-en')
```

它存储在 `T.current_languages`不需要翻译的语言列表中，并强制重新加载语言文件。

请注意，从 web2py 的角度来看，“it”和“it-it”是不同的语言。要同时支持它们，需要两个翻译文件，总是小写。所有其他语言也是如此。

当前接受的语言存储在

```
T.accepted_language
```



#### 翻译变量

`T`不仅可以翻译字符串，还可以翻译存储在变量中的值：

```
>>> a = "test"
>>> print T(a)
```

在这种情况下，单词“test”被翻译，但如果找不到并且文件系统是可写的，它将把它添加到语言文件中要翻译的单词列表中。

请注意，这可能会导致大量文件 IO，您可能希望禁用它：

```
T.is_writable = False
```

防止`T`动态更新语言文件。



#### 评论和多种翻译

同一个字符串有可能出现在应用程序的不同上下文中，需要根据上下文进行不同的翻译。为此，可以向原始字符串添加注释。注释不会被渲染，但会被 web2py 用来确定最合适的翻译。例如：

```
T("hello world ## first occurrence")
T("hello world ## second occurrence")
```

后面的文本`##`，包括双精度`##`，都是注释。



#### 多元化引擎

从 2.0 版开始，web2py 包含一个强大的复数系统（PS）。这意味着当标记为翻译的文本取决于数值变量时，它可能会根据数值进行不同的翻译。例如，在英语中，我们可以呈现：

```
x book(s)
```

和

```
a book (x==1)
5 books (x==5)
```

英语有一种单数形式和一种复数形式。复数形式是通过添加“-s”或“-es”或使用特殊形式来构建的。web2py 提供了一种方法来定义每种语言的复数规则，以及默认规则的例外情况。事实上，web2py 已经知道许多语言的复数规则。例如，它知道斯洛文尼亚语有一种单数形式和 3 种复数形式（对于 x==2、x==3 或 x==4 和 x>4）。这些规则被编码在“gluon/contrib/plural_rules/*.py”文件中，并且可以创建新文件。通过使用管理界面编辑复数文件来创建单词的显式复数。

默认情况下，PS 未激活。它由函数的`symbols`参数触发`T`。例如：

```
T("You have %s %%{book}", symbols=10) 
```

或者，不那么冗长：

```
T("You have %s %%{book}", 10) 
```

现在 PS 为单词“book”和数字 10 激活。英文的结果将是：“You have 10 books”。请注意，“book”已被复数为“books”。

PS由3部分组成：

- 在输入`%%{}`中标记单词的占位符`T`
- 决定使用哪种词形式的规则（“gluon/contrib/plural_rules/*.py”）
- 带有单词复数形式的字典（“applications/app/languages/plural-*.py”）

符号的值可以是单个变量、变量列表/元组或字典。

占位符`%%{}`可能由 3 个部分组成：

```
%%{[<modifier>]<word>[<parameter>]}
```

在哪里：

```
<modifier> ::= ! | !! | !!!
<word> ::= any word or phrase in singular in lower case (!)
<parameter> ::= [index] | (key) | (number)
```

以下是一些可能性：

- `%%{word}`相当于`%%{word[0]}`（如果没有使用修饰符）。

- ```
  %%{word[index]}
  ```

  当符号是元组时使用。symbols[index] 为我们提供了一个

  ```
  number
  ```

  用来决定选择哪种单词形式的参数。例如在：

  ```
  T("blabla %s %s %%{word[1]}", (var1, var2))  
  ```

  PS 将分别用于“word”和“var2”。

- `%%{word(key)}``number`当symbols是字典时，用于从symbols[key]中获取参数。

- `%%{word(number)}`允许`number`直接传递 a（例如：）`%%{word(%i)}`。

其他也允许`number`直接传递 a 的占位符是：

- `%%{?word?number}``number == 1`如果，则返回“单词” ，`number`否则返回。
- `%%{?number} or %%{??number}`返回`number`if `number != 1`，否则不返回任何内容。

您可以将多个`%%{}`占位符与一个索引一起使用：

```
T("%%{this} %%{is} %s %%{book}", var)
```

生成：

```
var  output
------------------
 1   this is 1 book
 2   these are 2 books
 3   these are 2 books
 ...
```

同样，您可以将字典传递给符号：

```
T("blabla %(var1)s %(wordcnt)s %%{word(wordcnt)}",
    dict(var1="tututu", wordcnt=20))  
     
```

产生：

```
blabla tututu 20 words
```

你可以用这个占位符替换你想要的任何单词“1” `%%{?word?number}`。例如：

```
T("%%{this} %%{is} %%{?a?%s} %%{book}", var)
```

产生：

```
var  output
------------------
 1   this is a book
 2   these are 2 books
 3   these are 3 books
 ...
```

在里面`%%{...}`你还可以使用以下修饰符：

- `!`将文本大写（相当于`string.capitalize`）
- `!!`将每个单词大写（相当于`string.title`）
- `!!!`将每个字符大写（相当于`string.upper`）

请注意，您可以使用 \ 来转义`!`和`?`.



#### 翻译、plural和 MARKMIN

您还可以通过替换在翻译字符串中使用强大的 MARKMIN 语法

```
T("hello world")
```

和

```
T.M("hello world")
```

现在字符串接受 MARKMIN 标记，如[第 5 章所述](http://web2py.com/books/default/chapter/29/05#markmin_syntax)



### cookie

web2py 使用 Python cookie 模块来处理 cookie。

来自浏览器的`request.cookies`cookie 位于 中，服务器发送的 cookie 位于`response.cookies`.

您可以按如下方式设置 cookie：

```
response.cookies['mycookie'] = 'somevalue'
response.cookies['mycookie']['expires'] = 24 * 3600
response.cookies['mycookie']['path'] = '/'
```

第二行告诉浏览器将 cookie 保留 24 小时。第三行告诉浏览器将 cookie 发送回当前域中的任何应用程序（URL 路径）。请注意，如果您不指定 cookie 的路径，浏览器将假定所请求的 URL 的路径，因此仅当请求相同的 URL 路径时，cookie 才会返回给服务器。

可以通过以下方式使 cookie 变得安全：

```
response.cookies['mycookie']['secure'] = True
```

这告诉浏览器仅通过 HTTPS 而不是通过 HTTP 将 cookie 发送回。

可以通过以下方式检索 cookie：

```
if request.cookies.has_key('mycookie'):
    value = request.cookies['mycookie'].value
```

除非会话被禁用，否则 web2py 会在后台设置以下 cookie 并使用它来处理会话：

```
response.cookies[response.session_id_name] = response.session_id
response.cookies[response.session_id_name]['path'] = "/"
```

请注意，如果单个应用程序包含多个子域，并且您希望在这些子域之间共享会话（例如 sub1.yourdomain.com、sub2.yourdomain.com 等），则必须将会话 cookie 的域显式设置为如下：

```
if not request.env.remote_addr in ['127.0.0.1', 'localhost']:
    response.cookies[response.session_id_name]['domain'] = ".yourdomain.com"  
      
```

例如，如果您希望允许用户跨子域保持登录状态，上述内容可能很有用。



### 应用程序**初始化**

当你部署 web2py 时，你会想要设置一个默认应用程序，即当 URL 中有空路径时启动的应用程序，如下所示：

```
http://127.0.0.1:8000
```

默认情况下，当遇到空路径时，web2py 会查找名为**init**的应用程序。如果没有 init 应用程序，它会查找名为**welcome**的应用程序。

通过在 routes.py 中设置，可以将默认应用程序的名称从**init**更改为另一个名称：`default_application`

```
default_application  =  "我的应用"
```

注：`default_application`首次出现于 web2py 1.83 版本。

以下是设置默认应用程序的四种方法：

- 调用您的默认应用程序“init”。
- `default_application`在 routes.py 中设置为您的应用程序的名称
- 建立一个从“applications/init”到应用程序文件夹的符号链接。
- 使用下一节中讨论的 URL 重写。



### 网址重写

web2py 有能力在调用控制器动作之前重写传入请求的 URL 路径（URL 映射），反之，web2py 可以重写`URL`函数生成的 URL 路径（反向 URL 映射）。这样做的一个原因是为了处理遗留 URL，另一个是为了简化路径并使它们更短。

web2py 包括两个不同的 URL 重写系统：一个易于使用*的基于参数的*系统，用于大多数用例，一个灵活*的基于模式的*系统，用于更复杂的情况。要指定 URL 重写规则，请在名为“web2py”的文件夹中创建一个新文件`routes.py`（其内容`routes.py`取决于您选择的两个重写系统中的哪一个，如下两节所述）。这两个系统不能混用。

> 请注意，如果您编辑 routes.py，则必须重新加载它。这可以通过两种方式完成：重新启动 Web 服务器或单击 admin 中的路由重新加载按钮。如果路线中有错误，它们将不会重新加载。



#### 基于参数的系统

基于参数的（参数）路由器提供了对几种“固定”URL 重写方法的轻松访问。其功能包括：

- 从外部可见的 URL（由 URL() 函数创建的 URL）中省略默认的应用程序、控制器和函数名称
- 将域（和/或端口）映射到应用程序或控制器
- 在 URL 中嵌入语言选择器
- 从传入 URL 中删除固定前缀并将其添加回传出 URL
- 将 /robots.txt 等根文件映射到应用程序静态目录

参数路由器还提供了对传入 URL 的更灵活的验证。

假设您编写了一个名为的应用程序`myapp`并希望将其设为默认值，这样应用程序名称就不再是用户看到的 URL 的一部分。您的默认控制器仍然是`default`，并且您还想从用户可见的 URL 中删除其名称。这是您放入的内容`routes.py`：

```
routers = dict(
    BASE  = dict(default_application='myapp'),
)
```

而已。参数路由器足够聪明，知道如何使用 URL 做正确的事情，例如：

```
http://domain.com/myapp/default/myapp
```

或者

```
http://domain.com/myapp/myapp/index
```

正常的缩短是模棱两可的。如果您有两个应用程序`myapp`和`myapp2`，您将获得相同的效果，并且`myapp2`只要安全（大多数情况下），还会从 URL 中删除默认控制器。

这是另一种情况：假设您想支持基于 URL 的语言，您的 URL 如下所示：

```
http://myapp/en/some/path
```

或（重写）

```
http://en/some/path
```

就是这样：

```
routers = dict(
    BASE  = dict(default_application='myapp'),
    myapp = dict(languages=['en', 'it', 'jp'], default_language='en'),
)
```

现在传入的 URL 是这样的：

```
http://domain.com/it/some/path
```

将被路由到：

```
/myapp/some/path
```

并且 request.uri_language 将设置为 'it'，因此您可以强制翻译。您还可以拥有特定于语言的静态文件。

```
http://domain.com/it/static/filename
```

将映射到：

```
应用程序/myapp/静态/它/文件名
```

如果该文件存在。如果没有，则 URL 如下：

```
http://domain.com/it/static/base.css
```

仍将映射到：

```
应用程序/myapp/static/base.css
```

（因为没有`static/it/base.css`）。

因此，如果需要，您现在可以拥有特定于语言的静态文件，包括图像。还支持域映射：

```
routers = dict(
    BASE  = dict(
        domains = {
            'domain1.com' : 'app1',
            'domain2.com' : 'app2',
        }
    ),
)
```

做你所期望的。

```
routers = dict(
    BASE  = dict(
        domains = {
            'domain.com:80'  : 'app/insecure',
            'domain.com:443' : 'app/secure',
        }
    ),
)
```

将访问映射`http://domain.com`到名为 的控制器`insecure`，而`HTTPS`访问则转到`secure`控制器。或者，您可以以显而易见的方式将不同的端口映射到不同的应用程序。

有关详细信息，请参阅标准 web2py 发行版的“examples”文件夹中提供的文件[“routes.parametric.example.py” 。](https://github.com/web2py/web2py/blob/master/examples/routes.parametric.example.py)

注：基于*参数的*系统最早出现在 web2py 1.92.1 版本。



#### 基于模式的系统

尽管刚刚描述*的基于参数的*系统对于大多数用例来说应该足够了，但是*基于模式*的替代系统为更复杂的情况提供了一些额外的灵活性。要使用基于模式的系统，不是将路由器定义为路由参数的字典，而是定义两个 2 元组列表（或元组），`routes_in`并且`routes_out`. 每个元组包含两个元素：要替换的模式和替换它的字符串。例如：

```
routes_in = (
    ('/testme', '/examples/default/index'),
)
routes_out = (
    ('/examples/default/index', '/testme'),
)
```

使用这些路由，URL：

```
http://127.0.0.1:8000/testme
```

映射为：

```
http://127.0.0.1:8000/examples/default/index
```

对于访问者来说，所有指向页面 URL 的链接看起来都像`/testme`.

这些模式与 Python 正则表达式具有相同的语法。例如：

```
(r'.*\.php', '/init/default/index'),
```

将所有以“.php”结尾的 URL 映射到索引页面。

规则的第二项也可以是重定向到另一个页面：

```
(r'.*\.php', '303->http://example.com/newpage'),
```

这里 303 是重定向响应的 HTTP 代码。

有时您想从 URL 中删除应用程序前缀，因为您打算只公开一个应用程序。这可以通过以下方式实现：

```
routes_in = (
    ('/(?P<any>.*)', '/init/\g<any>'),
)
routes_out = (
    ('/init/(?P<any>.*)', '/\g<any>'),
)
```

还有一种替代语法可以与上面的正则表达式符号混合。它由使用`$name`代替`(?P<name>\w+)`或组成`\g<name>`。例如：

```
routes_in = (
    ('/$c/$f', '/init/$c/$f'),
)

routes_out = (
    ('/init/$c/$f', '/$c/$f'),
)
```

还将消除所有 URL 中的“/init”应用程序前缀。

使用该`$name`符号，您可以自动映射`routes_in`到`routes_out`，前提是您不使用任何正则表达式。例如：

```
routes_in = (
    ('/$c/$f', '/init/$c/$f'),
)

routes_out = [(x, y) for (y, x) in routes_in]
```

如果有多个路由，则执行第一个匹配 URL 的路由。如果没有模式匹配，则路径保持不变。

您可以使用`$anything`匹配任何内容 ( `.*`) 直到行尾。

这是用于处理网站图标和机器人请求的最小“routes.py”：





```
routes_in = (
    (r'/favicon\.ico', '/examples/static/favicon.ico'),
    (r'/robots\.txt', '/examples/static/robots.txt'),
)
routes_out = ()
```

这是一个更复杂的示例，它公开了一个没有不必要前缀的应用程序“myapp”，但也公开了**admin**、**appadmin**和 static：

```
routes_in = (
    ('/admin/$anything', '/admin/$anything'),
    ('/static/$anything', '/myapp/static/$anything'),
    ('/appadmin/$anything', '/myapp/appadmin/$anything'),
    ('/favicon.ico', '/myapp/static/favicon.ico'),
    ('/robots.txt', '/myapp/static/robots.txt'),
)
routes_out = [(x, y) for (y, x) in routes_in[:-2]]
```

路由的一般语法比我们目前看到的简单示例更复杂。这是一个更普遍和有代表性的例子：

```
routes_in = (
    (r'140\.191\.\d+\.\d+:https?://www\.web2py\.com:post /(?P<any>.*)\.php',
     '/test/default/index?vars=\g<any>'),
)
```

它从与正则表达式匹配的远程 IP映射`http`或`https` `POST`请求（注意小写“post”）到主机`www.web2py.com`

```
r'140\.191\.\d+.\d+'
```

请求匹配正则表达式的页面

```
r'/(?P<any>.*)\.php'
```

进入

```
'/test/default/index?vars=\g<any>'
```

where`\g<any>`被匹配的正则表达式替换。

一般语法是

```
'[remote address]:[protocol]://[host]:[method] [path]'
```

如果模式的第一部分（除了`[path]`）缺失，web2py 提供一个默认值：

```
'.*?:https?://[^:/]+:[az]+'
```

整个表达式作为正则表达式匹配，所以“.” 必须转义，并且可以`(?P<...>...)`使用 Python 正则表达式语法捕获任何匹配的子表达式。请求方法（通常是 GET 或 POST）必须小写。被匹配的 URL 有任何未引用的`%xx`转义。

这允许基于客户端 IP 地址或域、基于请求的类型、方法和路径来重新路由请求。它还允许 web2py 将不同的虚拟主机映射到不同的应用程序中。任何匹配的子表达式都可用于构建目标 URL，并最终作为 GET 变量传递。

所有主要的 Web 服务器，例如 Apache 和 lighttpd，也具有重写 URL 的能力。在生产环境中，这可能是一个选项，而不是`routes.py`. 无论您决定做什么，我们强烈建议您不要在应用程序中对内部 URL 进行硬编码，而是使用 URL 函数来生成它们。如果路由发生变化，这将使您的应用程序更具可移植性。

##### 特定于应用程序的 URL 重写

使用基于模式的系统时，应用程序可以在位于应用程序基本文件夹中的特定于应用程序的 routes.py 文件中设置自己的路由。这是通过`routes_app`在基本 routes.py 中配置来启用的，以从传入的 URL 中确定要选择的应用程序的名称。发生这种情况时，将使用特定于应用程序的 routes.py 代替基本 routes.py。

的格式与`routes_app`相同`routes_in`，只是替换模式只是应用程序名称。如果应用`routes_app`到传入的 URL 不会产生应用程序名称，或者找不到产生的应用程序特定的 routes.py，则照常使用基本的 routes.py。

注：`routes_app`首次出现于 web2py 1.83 版本。

##### 默认应用程序、控制器和函数

使用基于模式的系统时，可以通过在 routes.py 中设置适当的值，将默认应用程序、控制器和函数的名称分别从**init**、**default**和**index**更改为另一个名称：

```
default_application  =  "myapp" 
default_controller  =  "admin" 
default_function  =  "start"
```

注意：这些项目首次出现在 web2py 版本 1.83。



#### 错误路线

如果服务器出现错误，您还可以使用`routes.py`将请求重新路由到特殊操作。您可以为每个应用程序、每个错误代码或每个应用程序和错误代码全局指定此映射。这是一个例子：

```
routes_onerror = [
    ('init/400', '/init/default/login'),
    ('init/*', '/init/static/fail.html'),
    ('*/404', '/init/static/cantfind.html'),
    ('*/*', '/init/error/index')
]
```

对于每个元组，第一个字符串与“[app name]/[error code]”匹配。如果找到匹配项，失败的请求将重新路由到匹配元组的第二个字符串中的 URL。如果错误处理 URL 不是静态文件，则以下 GET 变量将传递给错误操作：

- `code`：HTTP 状态码（例如，404、500）
- `ticket`: 形式为“[app name]/[ticket number]”（如果没有ticket，则为“None”）
- `requested_uri`： 相当于`request.env.request_uri`
- `request_url`： 相当于`request.url`

这些变量将可通过错误处理操作访问，`request.vars`并可用于生成错误响应。特别是，错误操作返回原始 HTTP 错误代码而不是默认的 200 (OK) 状态代码是一个好主意。这可以通过设置来完成`response.status = request.vars.code`。也可以让错误操作向管理员发送（或排队）一封电子邮件，其中包括指向工单的链接`admin`。

不匹配的错误显示默认错误页面。也可以在此处自定义此默认错误页面（参见“examples”文件夹中的“routes.parametric.example.py”和“routes.patterns.example.py”）：

```
error_message = '<html><body><h1>%s</h1></body></html>'
error_message_ticket = '''<html><body><h1>Internal error</h1>
     Ticket issued: <a href="/admin/default/ticket/%(ticket)s"
     target="_blank">%(ticket)s</a></body></html>'''
```

当请求无效的应用程序或函数时，第一个变量包含错误消息。第二个变量包含出票时的错误消息。

`routes_onerror`使用两种路由机制。

在“routes.py”中，您还可以指定负责错误处理的操作：

```
error_handler = dict(application='error',
                     controller='default',
                     function='index')
```

如果`error_handler`指定了，则在没有用户重定向的情况下调用操作，并且处理程序操作将负责处理错误。如果错误处理页面本身返回错误，web2py 将回退到其旧的静态响应。



#### 静态资源管理

从 2.1.0 版本开始，web2py 具有管理静态资产的能力。

在开发应用程序时，静态文件会经常更改，因此 web2py 会发送没有缓存头的静态文件。这具有“强制”浏览器在每次请求时请求静态文件的副作用。这会导致加载页面时性能低下。

在“生产”站点中，您可能希望提供带有`cache`标题的静态文件，以防止不必要的下载，因为静态文件不会更改。

`cache`headers 允许浏览器只获取每个文件一次，从而节省带宽并减少加载时间。

但是有一个问题：缓存头应该声明什么？文件应该什么时候过期？首次提供文件时，服务器无法预测它们何时会被更改。

手动方法包括为不同版本的静态文件创建子文件夹。例如，早期版本的“layout.css”可以在 URL“/myapp/static/css/1.2.3/layout.css”上获得。当您更改文件时，您会创建一个新的子文件夹并将其链接为“/myapp/static/css/1.2.4/layout.css”。

此过程有效，但它是迂腐的，因为每次更新 css 文件时，您必须记住将其移动到另一个文件夹，更改 layout.html 中文件的 URL 并部署。

静态资产管理通过允许开发人员为一组静态文件声明一个版本来解决这个问题，并且只有在版本号发生变化时才会再次请求它们。资产版本号是文件 url 的一部分，如上例所示。与前一种方法的不同之处在于版本号只出现在 URL 中，而不出现在文件系统中。

如果您想使用缓存标头提供“/myapp/static/layout.css”，您只需将文件包含在包含版本号的修改后的 URL 中：

```
/myapp/static/_1.2.3/layout.css
```

（注意 URL 定义了一个版本号，它不会出现在其他任何地方）。

请注意，URL 以“/myapp/static/”开头，后跟由下划线和由句点分隔的 3 个整数组成的版本号（如[SemVer](http://semver.org/)中所述），然后是文件名。另请注意，您不必创建“_1.2.3/”文件夹。

每次使用 url 中的版本请求静态文件时，都会使用“遥远的未来”缓存标头提供服务，具体而言：

```
Cache-Control : max-age=315360000
Expires: Thu, 31 Dec 2037 23:59:59 GMT
```

这意味着浏览器只会获取这些文件一次，它们将“永远”保存在浏览器的缓存中。

每次请求“_1.2.3/filename”时，web2py 都会从路径中删除版本部分，并为您的文件提供远在未来的标头，因此它们将被永久缓存。如果您更改了 URL 中的版本号，这会诱使浏览器认为它正在请求不同的文件，然后再次获取该文件。

您可以使用“_1.2.3”、“_0.0.0”、“_999.888.888”，只要版本以下划线开头，后跟三个以句点分隔的数字即可。

在开发时，您可以使用`response.files.append(...)`链接静态文件的静态 URL。在这种情况下，您可以手动包含“_1.2.3/”部分，或者您可以利用响应对象的新参数：`response.static_version`. 只需按照您以前的方式包含文件，例如

```
{{response.files.append(URL('static', 'layout.css'))}}
```

在模型集中

```
response.static_version = '1.2.3'
```

对于包含在`response.files`.

在模型中，您还可以设置

```
response.static_version_urls = True
```

重写**任何**指向静态目录的链接，除了那些包含在`response.files`.

通常在生产中，您让网络服务器（apache、nginx 等）为静态文件提供服务。您需要调整配置，使其“跳过”“_1.2.3/”部分。

例如，在 Apache 中，改变这个：

```
AliasMatch  ^/([^/]+)/static/(.*)  /home/www-data/web2py/applications/$1/static/$2
```

进入这个：

```
AliasMatch  ^/([^/]+)/static/(?:_[\d]+.[\d]+.[\d]+/)?(.*)  /home/www-data/web2py/applications/$1/static/$2
```

同样，在 Nginx 中更改此：

```
location ~* /(\w+)/static/ {
    root /home/www-data/web2py/applications/;
    expires max;
}
```

进入这个：

```
location ~* /(\w+)/static(?:/_[\d]+.[\d]+.[\d]+)?/(.*)$ {
   alias /home/www-data/web2py/applications/$1/static/$2;
   expires max;
}
```



### 在后台运行任务

在 web2py 中，每个 HTTP 请求都在其自己的线程中提供服务。线程被回收以提高效率并由 Web 服务器管理。为安全起见，Web 服务器为每个请求设置超时。这意味着操作不应该运行耗时过长的任务，不应该创建新线程，也不应该派生进程（这是可能的，但不推荐）。

运行耗时任务的正确方法是在后台执行。没有一种方法可以做到这一点，但在这里我们描述了 web2py 中内置的三种机制：**cron**、**自制任务队列**和**调度程序**。

**cron**我们指的是 web2py 功能，而不是 Unix Cron 机制。web2py cron 也适用于 Windows。

如果您需要在预定时间在后台执行任务，并且与两次调用之间的时间间隔相比，这些任务所需的时间相对较短，那么 web2py cron 就是您的选择。每个任务都在自己的进程中运行，多个任务可以同时运行，但您无法控制运行的任务数量。如果意外地一项任务与自身重叠，则可能导致数据库锁定和内存使用量激增。

web2py 调度程序采用不同的方法。运行进程的数量是固定的，它们可以运行在不同的机器上。每个进程被称为一个工人。每个工作人员在可用时选择一个任务，并在计划运行的时间后尽快执行它，但不一定在那个确切时间。运行的进程数不能超过计划任务的数量，因此不会出现内存峰值。调度程序任务可以在模型中定义并存储在数据库中。web2py 调度程序没有实现分布式队列，因为它假设分发任务的时间与运行任务的时间相比可以忽略不计。工作人员从数据库中获取任务。

在某些情况下，自制任务队列可以成为 web2py 调度程序的更简单替代方案。



#### Cron

web2py cron 使应用程序能够以独立于平台的方式在预设时间执行任务。

对于每个应用程序，cron 功能由 crontab 文件定义：

```
app/cron/crontab
```

它遵循参考文献中定义的语法。[ [cron](http://web2py.com/books/default/reference/29/cron) ]（带有一些特定于 web2py 的扩展）。

> 在 web2py 2.1.1 之前，cron 默认是启用的，可以通过`-N`命令行选项禁用。从 2.1.1 开始，cron 默认是禁用的，可以通过`-Y`选项启用。这种变化的动机是希望推动用户使用新的调度程序（优于 cron 机制），也因为 cron 可能会影响性能。

这意味着每个应用程序都可以有一个单独的 cron 配置，并且可以从 web2py 中更改 cron 配置，而不会影响主机操作系统本身。

这是一个例子：

```
0-59/1  *  *  *  *  root python /path/to/python/script.py
30      3  *  *  *  root *applications/admin/cron/db_vacuum.py
*/30    *  *  *  *  root **applications/admin/cron/something.py
@reboot root    *mycontroller/myfunction
@hourly root    *applications/admin/cron/expire_sessions.py
```

此示例中的最后两行使用常规 cron 语法的扩展来提供额外的 web2py 功能。

> 文件“applications/admin/cron/expire_sessions.py”实际上存在并随**管理**应用程序一起提供。它检查过期的会话并删除它们。“applications/admin/cron/crontab”每小时运行一次这个任务。

如果任务/脚本以星号 ( ) 为前缀并以`*`结尾`.py`，它将在 web2py 环境中执行。这意味着您将拥有所有控制器和模型供您使用。如果您使用两个星号 ( `**`)，则不会执行模型。这是推荐的调用方式，因为它具有较少的开销并避免了潜在的锁定问题。

请注意，在 web2py 环境中执行的脚本/函数需要`db.commit()`在函数末尾使用手册，否则事务将被还原。

web2py 不会在 shell 模式下生成票证或有意义的回溯，这就是 cron 的运行方式，因此请确保您的 web2py 代码在将其设置为 cron 任务之前运行没有错误，因为您可能无法看到这些错误从 cron 运行。此外，请注意如何使用模型：虽然执行发生在单独的进程中，但必须考虑数据库锁，以避免页面等待可能阻塞数据库的 cron 任务。`**`如果您不需要在 cron 任务中使用数据库，请使用该语法。

您也可以调用控制器函数，在这种情况下无需指定路径。控制器和功能将是调用应用程序的。请特别注意上面列出的注意事项。例子：

```
*/30  *  *  *  *  root *mycontroller/myfunction
```

如果您`@reboot`在 crontab 文件的第一个字段中指定，则给定任务将仅在 web2py 启动时执行一次。如果您想在 web2py 启动时为应用程序预缓存、检查或初始化数据，则可以使用此功能。请注意，cron 任务与应用程序并行执行 --- 如果应用程序在 cron 任务完成之前还没有准备好为请求提供服务，您应该实施检查以反映这一点。例子：

```
@reboot  root *mycontroller/myfunction
```

根据您调用 web2py 的方式，web2py cron 有四种操作模式。

- *soft cron*：在所有执行模式下都可用
- *hard cron*：如果使用内置 Web 服务器（直接或通过 Apache mod_proxy）可用
- *external cron*：如果您可以访问系统自己的 cron 服务，则可用
- 没有 cron

如果您使用的是内置 Web 服务器，则默认为硬 cron；在所有其他情况下，默认为软 cron。`enabled`如果您使用 CGI、FASTCGI 或 WSGI，软 cron 是默认方法（但请注意，在 web2py 提供的标准`wsgihandler.py`文件中默认情况下软 cron 不是）。

使用软 cron，您的任务将在 crontab 中指定的时间后第一次调用（页面加载）web2py 时执行；但仅在处理页面之后，因此用户不会观察到延迟。显然，关于何时执行任务的确切时间存在一些不确定性，具体取决于站点接收的流量。此外，如果 Web 服务器设置了页面加载超时，则 cron 任务可能会中断。如果这些限制不可接受，请参阅*external cron*。软 cron 是一个合理的最后手段，但如果您的 Web 服务器允许其他 cron 方法，则它们应该优于软 cron。

如果您使用内置 Web 服务器（直接或通过 Apache mod_proxy），硬 cron 是默认设置。硬 cron 在并行线程中执行，因此与软 cron 不同，在运行时间或执行时间精度方面没有限制。

外部 cron 在任何情况下都不是默认设置，但需要您有权访问系统 cron 设施。它在并行进程中运行，因此不适用软 cron 的任何限制。这是在 WSGI 或 FASTCGI 下使用 cron 的推荐方式。

添加到系统 crontab 的行示例（通常是 /etc/crontab）：

```
0-59/1 * * * * web2py cd /var/www/web2py/ && python web2py.py -J -C -D 1 >> /tmp/cron.output 2>&1
```

使用 external `cron`，请确保添加如上所示的`-J`（或`--cronjob`相同的），以便 web2py 知道该任务由 cron 执行。Web2py 在内部设置了 soft 和 hard `cron`。



#### 自制任务队列

虽然 cron 对于定期运行任务很有用，但它并不总是运行后台任务的最佳解决方案。为此，web2py 提供了运行任何 Python 脚本的能力，就好像它在控制器中一样：

```
python web2py.py -S app -M -R applications/app/private/myscript.py -A a b c
```

where`-S app`告诉 web2py 将“myscript.py”作为“app”运行，`-M`告诉 web2py 执行模型，并`-A a b c`传递可选的命令行参数：

```
sys.argv = ['applications/app/private/myscript.py', 'a', 'b', 'c']
```

到“myscript.py”。

这种类型的后台进程不应通过 cron 执行（可能除了 cron @reboot），因为您需要确保同时运行的实例不超过一个。使用 cron，一个进程可能从 cron 迭代 1 开始，而 cron 迭代 2 没有完成，因此 cron 一次又一次地启动它 - 从而阻塞邮件服务器。

在[第 8 章](http://web2py.com/books/default/chapter/29/08#Sending-messages-using-a-background-task)中，我们将提供一个示例，说明如何使用上述方法发送电子邮件。





### web2py 调度器Scheduler

在后台运行任务（因此远离 webserver 进程）的主流 web2py 解决方案是内置调度程序。

稳定的 API 由以下函数组成：

- 禁用（）
- 恢复（）
- 终止（）
- 杀（）
- queue_task()
- 任务状态（）
- 停止任务（）

web2py 调度程序的工作方式与上一小节中描述的任务队列非常相似，但有一些区别：

- 它为创建、调度和监视任务提供了标准机制。
- 没有一个后台进程，而是一组工作进程。
- 可以监视工作节点的工作，因为它们的状态以及任务的状态都存储在数据库中。
- 它可以在没有 web2py 的情况下工作，但这里没有记录。

调度程序不使用 cron，尽管可以使用 cron @reboot 来启动工作节点。

有关在 Linux 和 Windows 下部署调度程序的更多信息，请参见[第 13 章](http://web2py.com/books/default/chapter/29/13)。

在调度程序中，任务只是在模型中（或在模块中并由模型导入）中定义的函数。例如：

```
def task_add(a, b):
    return a + b
```

任务将始终在控制器看到的相同环境中调用，因此它们会看到模型中定义的所有全局变量，包括数据库连接 ( `db`)。任务与控制器操作不同，因为它们与 HTTP 请求无关，因此没有`request.env`. 此外，任务可以访问正常请求中不存在的另一个环境变量：`W2P_TASK`. `W2P_TASK.id`保存正在运行的任务的和`scheduler_task.id`字段。`W2P_TASK.uuid``scheduler_task.uuid`

> `db.commit()`如果涉及到数据库的插入/更新，请记住在每个任务结束时调用。web2py 默认在成功操作结束时提交，但调度程序任务不是操作。

要启用调度程序，您必须在模型中实例化调度程序类。为您的应用启用调度程序的推荐方法是创建一个名为的模型文件`scheduler.py`并在其中定义您的函数。在函数之后，您可以将以下代码放入模型中：

```
from gluon.scheduler import Scheduler
scheduler = Scheduler(db)
```

如果您的任务是在模块中定义的（而不是模型），您可能必须重新启动工作人员。

该任务计划与

```
scheduler.queue_task(task_add, pvars=dict(a=1, b=2))
```



#### 参数Parameters

该类的第一个参数`Scheduler`必须是调度程序用于与工作人员通信的数据库。这可以是`db`应用程序的，也可以是另一个专用的`db`，可能是由多个应用程序共享的。如果您使用 SQLite，建议使用与您的应用程序使用的数据库不同的数据库，以保持应用程序响应。一旦定义了任务并`Scheduler`实例化了，所需要做的就是启动工作程序。您可以通过以下几种方式做到这一点：

```
python web2py.py -K myapp
```

启动应用程序的工作人员`myapp`。如果您想为同一个应用程序启动多个工作人员，您可以通过传递`myapp,myapp`. 您还可以通过`group_names`（覆盖模型中的一组）

```
python web2py.py -K myapp:group1:group2,myotherapp:group1
```

如果您有一个模型，`scheduler.py`您可以从 web2py 的默认窗口（用于设置 IP 地址和端口的窗口）启动/停止工作人员。



#### 调度程序部署

最后一个不错的补充：如果您使用嵌入式网络服务器，您可以只用一行代码启动网络服务器和调度程序（这假设您不希望弹出 web2py 窗口，否则您可以使用“调度程序”菜单代替)

```
python web2py.py -a yourpass -K myapp -X
```

您可以传递通常的参数（-i，-p，这里 -a 阻止窗口显示），传递 -K 参数中的任何应用程序并附加 -X。调度程序将与网络服务器一起运行！

希望创建服务的 Windows 用户应该在[第 13 章](http://web2py.com/books/default/chapter/29/13#Using-nssm-to-run-as-a-Windows-service)中看到。





#### 完成调度程序签名

调度器的完整签名是：

```
Scheduler(db,
          tasks=None,
          migrate=True,
          worker_name=None,
          group_names=None,
          heartbeat=HEARTBEAT,
          max_empty_runs=0,
          discard_results=False,
          utc_time=False)
```

让我们按顺序查看它们：

- `db`是您希望放置调度程序表的数据库 DAL 实例。
- `tasks`是将任务名称映射到函数的字典。如果不传递此参数，则会在应用环境中搜索函数。
- `worker_name`默认为无。一旦 worker 启动，就会生成一个 worker 名称作为 hostname-uuid。如果要指定它，请确保它是唯一的。
- `group_names`默认设置为**[main]**。所有任务都有一个`group_name`参数，默认设置为**main**。工人只能接他们分配的组的任务。

> 注意：如果您有不同的工人实例（例如在不同的机器上）并且您想将任务分配给特定的工人，这很有用。
>
> NB2：可以为工作人员分配更多组，它们也可以完全相同，例如`['mygroup','mygroup']`. 考虑到具有 group_names 的工作人员`['mygroup','mygroup']`能够处理具有 group_names 的工作人员的双倍任务，将分配任务`['mygroup']`。

- `heartbeat`默认设置为 3 秒。此参数控制调度程序多久检查一次表上的状态，`scheduler_worker`并查看是否有任何**分配**给自己的任务要处理。
- `max_empty_runs`默认为 0，这意味着一旦任务被分配，worker 将继续处理**任务**。如果您将其设置为一个值，例如 10，那么如果一个工作人员处于**活动**状态并且在 10 个循环中没有为其**分配**任何任务，它就会自动死亡。`heartbeat`循环是工作人员每 3 秒（或 set ）搜索任务时
- `discard_results`默认为 False。如果设置为 True，则不会创建 scheduler_run 记录。

> 注意： scheduler_run 记录将像以前一样为**FAILED**，**TIMEOUT**和**STOPPED**任务的状态创建。

- `utc_time`默认为 False。如果您需要与居住在不同时区的工作人员协调，或者在太阳/夏令时时间、提供来自不同国家/地区的日期时间等方面存在问题，您可以将其设置为 True。调度程序将遵守 UTC 时间并工作，而将本地时间放在一边。警告：您需要使用 UTC 时间安排任务（例如 start_time、stop_time 等）。

现在我们已经有了基础设施：定义任务，告诉调度器它们，启动工人。剩下的就是实际安排任务。



#### 任务

可以以编程方式或通过 appadmin 安排任务。实际上，只需在“scheduler_task”表中添加一个条目即可安排任务，您可以通过 appadmin 访问该条目：

```
http://127.0.0.1:8000/myapp/appadmin/insert/db/scheduler_task
```

这张表中各字段的含义是显而易见的。“args”和“vars”字段是以JSON格式传递给任务的值。在上面的“task_add”的情况下，“args”和“vars”的示例可能是：

```
args = [3, 4]
vars = {}
```

或者

```
args = []
vars = {'a': 3, 'b': 4}
```

该`scheduler_task`表是组织任务的表。

要通过 API 添加任务，请使用

```
scheduler.queue_task('mytask', ...)
```

正如本章稍后在[queue_task](http://web2py.com/books/default/chapter/29/04/the-core#queue_task)中解释的那样。



#### 任务生命周期

所有任务都遵循一个生命周期



![调度任务](http://web2py.com/books/default/image/29/ce8edcc3.png)



默认情况下，当您向调度程序发送任务时，它处于**QUEUED**状态。如果您需要稍后执行，请使用`start_time`参数（默认 = now）。如果由于某种原因您需要确保任务在某个时间点之后没有被执行（可能是对在凌晨 1 点关闭的 Web 服务的请求，需要在工作时间之后发送的邮件等） ...）你可以`stop_time`为它设置一个（默认=无）。如果您的任务之前没有被工人接走`stop_time`，它将被设置为**EXPIRED**。在stop_time**之前**没有`stop_time`设置或拾取的任务将**分配**给工作人员。当工作人员拿起任务时，其状态设置为**RUNNING**。

**RUNNING**任务可能会结束：

- 当参数超过`n`秒时**超时**`timeout`（默认 = 60 秒）。
- 检测到异常时**失败，**
- **COMPLETED**当他们成功完成时。

`start_time`和的值`stop_time`应该是日期时间对象。例如，要安排“mytask”在距当前时间 30 秒后运行，您可以执行以下操作：

```
from datetime import timedelta as timed
scheduler.queue_task('mytask', start_time=request.now + timed(seconds=30))
```

此外，您可以控制任务应重复多少次（即您需要以指定的时间间隔聚合一些数据）。为此，请设置`repeats`参数（默认 = 仅 1 次，0 = 无限制）。`period`您可以使用参数（默认 = 60 秒）影响执行之间应该经过多少秒。

> 默认行为：不是计算第一轮的END到下一轮的START之间的时间段，而是从第一轮的START时间到下一个循环的START时间）。这可能会导致在工作开始时间累积“漂移”。在 v 2.8.2 之后，`prevent_drift`添加了一个新参数，默认为 False。如果在排队任务时设置为 True，则 start_time 参数将优先于时间段，防止漂移。

您还可以使用参数（默认 = 0，-1 = 无限制）设置函数可以引发异常（即从慢速 Web 服务请求数据）并再次排队而不是在**FAILED状态下停止的次数。**`retry_failed`



![任务重复](http://web2py.com/books/default/image/29/7d8b85e4.png)



总结：你有

- `period`并`repeats`获得自动重新安排的功能
- `timeout`确保函数不超过一定的时间
- `retry_failed`控制任务“失败”的次数
- `start_time`并`stop_time`在有限的时间范围内安排功能



#### `queue_task`任务队列

方法：

```
scheduler.queue_task(function,
                     pargs=[],
                     pvars={},
                     start_time=now,  # datetime
                     stop_time=None,  # datetime
                     timeout = 60,  # seconds
                     prevent_drift=False,
                     period=60,  # seconds
                     immediate=False,
                     repeats=1)
```

允许您将要由工作人员执行的任务排队。它返回一行（参见[此处](http://web2py.com/books/default/chapter/29/04/the-core#queue_task_return)），并采用以下参数：

- `function`（必需）：它可以是任务名称或对实际功能的引用。
- `pargs`: 是要传递给任务的参数，存储为 Python 列表。
- `pvars`: 是要传递给任务的命名参数，存储为 Python 字典。
- 所有其他 scheduler_task 列都可以作为关键字参数传递；显示最重要的。

例如：

```
scheduler.queue_task('demo1', [1, 2])
```

做同样的事情

```
scheduler.queue_task('demo1', pvars={'a': 1, 'b': 2})
```

作为

```
st.validate_and_insert(function_name='demo1', args=json.dumps([1, 2]))
```

并作为：

```
st.validate_and_insert(function_name='demo1', vars=json.dumps({'a': 1, 'b': 2}))
```

这是一个更复杂的完整示例：

```
def task_add(a, b):
    return a + b

scheduler = Scheduler(db, tasks=dict(demo1=task_add))

scheduler.queue_task('demo1', pvars=dict(a=1, b=2), repeats = 0, period=180)
```

从 2.4.1 版本开始，如果您传递一个附加参数`immediate=True`，它将强制主要工作人员重新分配任务。`5*heartbeats`在 2.4.1 之前，worker 每 5 个周期（所以，秒）检查一次新任务。如果您的应用程序需要经常检查新任务，为了获得*快速*的行为，您不得不降低`heartbeat`参数，无缘无故地给数据库施加压力。`immediate=True`您可以强制检查新任务：它最多会在几`heartbeat`秒钟后发生

调用`scheduler.queue_task`返回任务`id`和`uuid`您排队的任务（可以是您传递的任务或自动生成的任务），并且可能`errors`：

```
<Row {'errors': {}, 'id': 1, 'uuid': '08e6433a-cf07-4cea-a4cb-01f16ae5f414'}>
```

如果有错误（通常是语法错误或输入验证错误），你会得到验证的结果，id 和 uuid 将为 None

```
<Row {'errors': {'period': 'enter an integer greater than or equal to 0'}, 'id': None, 'uuid': None}>
```



#### `task_status`

要查询有关任务的调度程序，请使用`task_status`

```
scheduler.task_status(ref, output=False)
```

论据`ref`可以是

- integer --> 查找将由 scheduler_task.id 完成
- string --> 查找将由 scheduler_task.uuid 完成
- 查询 --> 根据需要查找（如 db.scheduler_task.task_name == 'test1'）

`output=True`获取 scheduler_run 记录

它返回单个 Row 对象，用于匹配条件的最新排队任务。

scheduler_run 记录由左连接获取，因此它可以包含所有字段 == 无

##### 示例：检索调度程序任务状态、结果和回溯

这里调度程序实例是`mysched`

```
task = mysched.queue_task(f, ...)
task_status = mysched.task_status(task.id, output=True)
traceback = task_status.scheduler_run.traceback
result = task_status.scheduler_run.run_result #or
result = task_status.result
   
  
```



#### 结果和输出

“scheduler_run”表存储了所有正在运行的任务的状态。每条记录都引用了一个工作人员接手的任务。一项任务可以有多次运行。例如，计划每小时重复 10 次的任务可能会运行 10 次（除非一次失败或运行时间超过 1 小时）。请注意，如果任务没有返回值，它会在完成后立即从 scheduler_run 表中删除。

可能的运行状态是：

```
RUNNING, COMPLETED, FAILED, TIMEOUT
```

如果运行完成，没有抛出异常，也没有任务超时，则运行标记为`COMPLETED`，任务标记为`QUEUED`或`COMPLETED`取决于它是否应该稍后再次运行。任务的输出以 JSON 格式序列化并存储在运行记录中。

当`RUNNING`任务抛出异常时，运行标记为`FAILED`，任务标记为`FAILED`。回溯存储在运行记录中。

同样，当一次运行超过超时时间时，它会停止并标记为`TIMEOUT`，任务标记为`TIMEOUT`。

在任何情况下，标准输出都会被捕获并记录到运行记录中。

> 由于多处理限制，请注意不要在排队函数上使用大量返回值或大量打印语句。由于输出被缓冲，您的任务可能会因为父进程挂起读取值而失败。此外，将打印语句保持在最低限度，如果需要，请使用不会弄乱标准输出的适当日志库。对于巨大的返回值，更好的选择是使用函数保存结果的表：您可以只返回对特定结果行的引用，而不会妨碍调度程序的主进程。

使用appadmin，可以检查所有`RUNNING`任务、任务输出、`COMPLETED`任务错误`FAILED`等。

调度程序还创建了一张名为“scheduler_worker”的表，其中存储了工作人员的心跳和他们的状态。



#### 管理流程

职工精细化管理难。这个模块尽量不留下任何平台（Mac、Win、Linux）。

当你启动一个 worker 时，你稍后可能想要：

- 杀死它“不管它在做什么”
- 仅当它不处理任务时才杀死它
- 让它睡觉

也许您还有一些任务排队，并且您想节省一些资源。您知道您希望它们每小时处理一次，因此，您需要：

- 处理所有排队的任务并自动死亡

所有这些都是可能的管理`Scheduler`参数或`scheduler_worker`表格。更准确地说，对于刚起步的工人，您可以更改`status`任何工人的价值以影响其行为。对于任务，worker 可以处于以下状态之一：ACTIVE、DISABLED、TERMINATE 或 KILLED。

**ACTIVE**和**DISABLED**是“持久的”，而**TERMINATE**或**KILL**，正如状态名称所暗示的那样，是比实际状态更多的“命令”。按 ctrl+c 等于将一个工人设置为**KILL**



![工人状态](http://web2py.com/books/default/image/29/bd891eed.png)



从 2.4.1 版本开始有一些商品功能（不言自明）

```
scheduler.disable()
scheduler.resume()
scheduler.terminate()
scheduler.kill()
```

每个函数都采用一个可选参数，可以是字符串或列表，以根据他们的`group_names`. 它默认为`group_names`调度程序实例中定义的。

一个例子胜过千言万语：`scheduler.terminate('high_prio')`将终止所有正在处理`high_prio`任务的工作人员，同时`scheduler.terminate(['high_prio', 'low_prio'])`终止所有`high_prio`工作`low_prio`人员。

> 注意：如果你有一个工作进程处理`high_prio`and `low_prio`，`scheduler.terminate('high_prio')`将一起终止这个工作进程，即使你也不想终止`low_prio`。

可以通过 appadmin 执行的所有操作都可以通过在这些表中插入和更新记录以编程方式执行。

无论如何，不应更新与`RUNNING`任务相关的记录，因为这可能会产生意外行为。最佳实践是使用“queue_task”方法对任务进行排队。

例如：

```
scheduler.queue_task(function_name='task_add',
                     pargs=[],
                     pvars={'a': 3, 'b': 4},
                     repeats=10,  # run 10 times
                     period=3600,  # every 1h
                     timeout=120,  # should take less than 120 seconds
                     )
```

请注意，“scheduler_task”表字段“times_run”、“last_run_time”和“assigned_worker_name”不是在调度时间提供的，而是由工作人员自动填写的。

您还可以检索已完成任务的输出：

```
completed_runs = db(db.scheduler_run.run_status='COMPLETED').select()
```

> 调度程序被认为是实验性的，因为它需要更广泛的测试，并且表结构可能会随着更多功能的添加而改变。



#### 报告进度百分比

在函数的打印语句中遇到的特殊“单词”会清除所有先前的输出。那个词是`!clear!`。这与`sync_output`参数相结合，允许报告百分比。

这是一个例子：

```
def reporting_percentages():
    time.sleep(5)
    print '50%'
    time.sleep(5)
    print '!clear!100%'
    return 1
```

该函数`reporting_percentages`休眠 5 秒，输出`50%`. 然后，它再休眠 5 秒并输出`100%`. 请注意，scheduler_run 表中的输出每 2 秒同步一次，并且包含的第二个打印语句`!clear!100%`将`50%`输出清除并仅替换为`100%`。

```
scheduler.queue_task(reporting_percentages, sync_output=2)
```





### 第三方模块

web2py 是用 Python 编写的，因此它可以导入和使用任何 Python 模块，包括第三方模块。它只需要能够找到它们。与任何 Python 应用程序一样，模块可以安装在官方 Python “site-packages”目录中，然后可以从代码中的任何位置导入。

顾名思义，“站点包”目录中的模块是站点级包。除非单独安装这些模块，否则需要站点包的应用程序是不可移植的。在“站点包”中包含模块的优点是多个应用程序可以共享它们。例如，让我们考虑名为“matplotlib”的绘图包。您可以使用 PEAK`easy_install`命令[ [easy-install](http://web2py.com/books/default/reference/29/easy-install) ]（或其现代替代品`pip` [ [PIP](http://web2py.com/books/default/reference/29/PIP) ]）从 shell 安装它：

```
easy_install py-matplotlib
```

然后您可以将其导入任何模型/控制器/视图：

```
import matplotlib
```

web2py 源代码分发和 Windows 二进制分发在顶级文件夹中有一个站点包。Mac 二进制发行版的文件夹中有一个 site-packages 文件夹：

```
web2py.app/Contents/Resources/site-packages
```

使用站点包的问题是很难同时使用单个模块的不同版本，例如可能有两个应用程序，但每个应用程序使用同一文件的不同版本。在此示例中，`sys.path`不能更改，因为它会影响两个应用程序。

对于这种情况，web2py 提供了另一种导入模块的方式，这种方式`sys.path`不会改变全局：将它们放在应用程序的“模块”文件夹中。一个好处是模块将自动复制并与应用程序一起分发。

> 一旦将模块“mymodule.py”放入应用程序“modules/”文件夹中，就可以从 web2py 应用程序中的任何位置导入它（无需更改`sys.path`）：
>
> ```
> import mymodule
> ```



### 执行环境

> 虽然这里讨论的一切都很好，但我们建议使用组件来构建您的应用程序，如[第 12 章](http://web2py.com/books/default/chapter/29/12)所述。

web2py 模型和控制器文件不是 Python 模块，因为它们不能使用 Python`import`语句导入。这样做的原因是模型和控制器被设计为在预先填充了 web2py 全局对象（请求、响应、会话、缓存和 T）和辅助函数的准备好的环境中执行。这是必要的，因为 Python 是一种静态（词法）作用域语言，而 web2py 环境是动态创建的。

web2py 提供了`exec_environment`允许您直接访问模型和控制器的功能。`exec_environment`创建一个 web2py 执行环境，将文件加载到其中，然后返回一个包含该环境的 Storage 对象。Storage 对象还用作命名空间机制。任何设计为在执行环境中执行的 Python 文件都可以使用`exec_environment`. 用途`exec_environment`包括：

- 从其他应用程序访问数据（模型）。
- 从其他模型或控制器访问全局对象。
- 从其他控制器执行控制器功能。
- 加载站点范围的帮助程序库。

`user`此示例从应用程序的表中读取行`cas`：

```
from gluon.shell import exec_environment
cas = exec_environment('applications/cas/models/db.py')
rows = cas.db().select(cas.db.user.ALL)
```

另一个例子：假设你有一个控制器“other.py”，它包含：

```
def some_action():
    return dict(remote_addr=request.env.remote_addr)
```

以下是如何从另一个控制器（或从 web2py shell）调用此操作：

```

from gluon.shell import exec_environment
other = exec_environment('applications/app/controllers/other.py', request=request)
result = other.some_action()
```



上面在第 2 行中，`request=request`是可选的。它具有将当前请求传递到“其他”环境的效果。如果没有这个参数，环境将包含一个新的和空的（除了`request.folder`）请求对象。也可以将响应和会话对象传递给`exec_environment`. 传递请求、响应和会话对象时要小心——被调用动作的修改或被调用动作中的编码依赖关系可能会导致意外的副作用。

第 3 行的函数调用不执行视图；它只是返回字典，除非`response.render`“some_action”显式调用。

最后一个警告：不要使用`exec_environment`不当。如果您想要在另一个应用程序中执行操作的结果，您可能应该实现一个 XML-RPC API（使用 web2py 实现一个 XML-RPC API 几乎是微不足道的）。不要`exec_environment`用作重定向机制；使用`redirect`助手。



### 合作

应用程序可以通过多种方式进行协作：

- 应用程序可以连接到同一个数据库，从而共享表。数据库中的所有表不必由所有应用程序定义，但它们必须由使用它们的那些应用程序定义。所有使用同一个表但只有一个表的应用程序必须使用`migrate=False`.
- 应用程序可以使用 LOAD 帮助器（在[第 12 章](http://web2py.com/books/default/chapter/29/12#Components-LOAD-and-Ajax)中描述）嵌入来自其他应用程序的组件。
- 应用程序可以共享会话。
- 应用程序可以通过 XML-RPC 远程调用彼此的操作。
- 应用程序可以通过文件系统访问彼此的文件（假设它们共享相同的文件系统）。
- `exec_environment`应用程序可以使用上面讨论的在本地调用彼此的操作。
- 应用程序可以使用以下语法导入彼此的模块：

```
from applications.otherapp.modules import mymodule
```

或者

```
import applications.otherapp.modules.othermodule
```

- 应用程序可以导入`PYTHONPATH`搜索路径中的任何模块，`sys.path`.

一个应用程序可以使用以下命令加载另一个应用程序的会话：

```
session.connect(request, response, masterapp='appname', db=db)
```

这里的“appname”是主应用程序的名称，它在 cookie 中设置初始 session_id。`db`是到包含会话表 ( ) 的数据库的数据库连接`web2py_session`。所有共享会话的应用程序必须使用相同的数据库进行会话存储。



### 日志记录

Python 提供了日志记录 API。Web2py 提供了一种机制来配置它，以便应用程序可以使用它。

在您的应用程序中，您可以创建一个记录器，例如在模型中：

```
import logging
logger = logging.getLogger("web2py.app.myapp")
logger.setLevel(logging.DEBUG)
```

你可以用它来记录各种重要的消息

```
logger.debug("Just checking that %s", details)
logger.info("You ought to know that %s", details)
logger.warn("Mind that %s", details)
logger.error("Oops, something bad happened %s", details)
```

`logging`是在https://docs.python.org/2/library/logging.html (Python 2.x) 和https://docs.python.org/3/library/logging.html (Python 3.x)

字符串“web2py.app.myapp”定义了一个应用级别的记录器。

要使其正常工作，您需要记录器的配置文件。一个由 web2py 在“examples”文件夹“logging.example.conf”中提供。您需要将文件复制到 web2py 的目录并将文件重命名为“logging.conf”并根据需要对其进行自定义。

这个文件是自我记录的，所以你应该打开它并阅读它。

要为应用程序“myapp”创建可配置的记录器，您必须将 myapp 添加到 [loggers] 键列表：

```
[loggers]
keys=root,rocket,markdown,web2py,rewrite,app,welcome,myapp
```

并且您必须添加一个 [logger_myapp] 部分，使用 [logger_welcome] 作为起点。

```
[logger_myapp]
level=WARNING
qualname=web2py.app.myapp
handlers=consoleHandler
propagate=0
```

“handlers”指令指定了日志记录的类型，这里将“myapp”记录到控制台。



### WSGI

web2py 和 WSGI 有着爱恨交织的关系。我们的观点是，WSGI 是作为一种协议开发的，用于以可移植的方式将 Web 服务器连接到 Web 应用程序，我们将其用于此目的。web2py 的核心是一个 WSGI 应用程序：`gluon.main.wsgibase`. 一些开发人员将 WSGI 作为中间件通信协议推到了极限，并将 Web 应用程序开发为具有多层的洋葱（每一层都是独立于整个框架开发的 WSGI 中间件）。web2py 内部不采用这种结构。这是因为我们认为框架的核心功能（处理 cookie、会话、错误、事务、调度）如果由单个综合层处理，则可以更好地优化速度和安全性。

然而 web2py 允许您以三种方式（及其组合）使用第三方 WSGI 应用程序和中间件：

- 您可以编辑文件“wsgihandler.py”并包含任何第三方 WSGI 中间件。
- 您可以将第三方 WSGI 中间件连接到应用程序中的任何特定操作。
- 您可以从您的操作中调用第三方 WSGI 应用程序。

唯一的限制是您不能使用第三方中间件来替换核心 web2py 功能。



#### 外部中间件

考虑文件“wsgihandler.py”：

```
#...
LOGGING = False
#...
if LOGGING:
    application = gluon.main.appfactory(wsgiapp=gluon.main.wsgibase,
                                        logfilename='httpserver.log',
                                        profilerfilename=None)
else:
    application = gluon.main.wsgibase
```

当`LOGGING`设置为 时`True`，`gluon.main.wsgibase`由中间件函数包装`gluon.main.appfactory`。它提供对“httpserver.log”文件的日志记录。以类似的方式，您可以添加任何第三方中间件。更多详细信息，请参阅官方 WSGI 文档。



#### 内部中间件

给定控制器中的任何操作（例如`index`）和任何第三方中间件应用程序（例如`MyMiddleware`，将输出转换为大写），您可以使用 web2py 装饰器将中间件应用于该操作。这是一个例子：

```
class MyMiddleware:
    """converts output to upper case"""
    def __init__(self, app):
        self.app = app
    def __call__(self, environ, start_response):
        items = self.app(environ, start_response)
        return [item.upper() for item in items]

@request.wsgi.middleware(MyMiddleware)
def index():
    return 'hello world'
```

我们不能保证所有第三方中间件都可以使用这种机制。



#### 调用*WSGI*应用程序

从 web2py 操作调用 WSGI 应用程序很容易。这是一个例子：

```
def test_wsgi_app(environ, start_response):
    """this is a test WSGI app"""
    status = '200 OK'
    response_headers = [('Content-type', 'text/plain'),
                        ('Content-Length', '13')]
    start_response(status, response_headers)
    return ['hello world!\n']

def index():
    """a test action that calls the previous app and escapes output"""
    items = test_wsgi_app(request.wsgi.environ,
                          request.wsgi.start_response)
    for item in items:
        response.write(item, escape=False)
    return response.body.getvalue()
```

在这种情况下，`index`操作调用`test_wsgi_app`并将返回的值逐字复制到页面输出。请注意，`index`它本身不是 WSGI 应用程序，它必须使用普通的 web2py API（例如`response.write`写入套接字）。