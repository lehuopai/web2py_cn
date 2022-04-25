## 第6章 数据库抽象层[¶](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#The-database-abstraction-layer)



### 依赖项

web2py 带有一个数据库抽象层 (DAL)，这是一个将 Python 对象映射到数据库对象（如查询、表和记录）的 API。DAL使用指定的方言为数据库后端动态实时生成SQL，这样您就不必编写SQL代码或学习不同的SQL方言（SQL一词是通用的），应用程序将在不同类型的数据库。下表显示了支持的数据库的部分列表。请查看 web2py 网站和邮件列表以获取最新的适配器。Google NoSQL 在第 13 章中被视为一个特例。

本章末尾的 Gotchas 部分包含有关特定数据库的更多信息。

Windows 二进制发行版可与 SQLite、MSSQL、PostgreSQL 和 MySQL 一起使用。Mac 二进制发行版可与 SQLite 一起使用。要使用任何其他数据库后端，请从源代码分发版运行并为所需的后端安装适当的驱动程序。

安装正确的驱动程序后，从源代码启动 web2py，它会找到驱动程序。以下是 web2py 可以使用的驱动程序列表：



| database   | drivers (source)                                             |
| ---------- | ------------------------------------------------------------ |
| SQLite     | sqlite3 or pysqlite2 or zxJDBC [[zxjdbc](http://web2py.com/books/default/reference/29/zxjdbc)] (on Jython) |
| PostgreSQL | psycopg2 [[psycopg2](http://web2py.com/books/default/reference/29/psycopg2)] or zxJDBC [[zxjdbc](http://web2py.com/books/default/reference/29/zxjdbc)] (on Jython) |
| MySQL      | pymysql [[pymysql](http://web2py.com/books/default/reference/29/pymysql)] or MySQLdb [[mysqldb](http://web2py.com/books/default/reference/29/mysqldb)] |
| Oracle     | cx_Oracle [[cxoracle](http://web2py.com/books/default/reference/29/cxoracle)] |
| MSSQL      | pyodbc [[pyodbc](http://web2py.com/books/default/reference/29/pyodbc)] or pypyodbc[[pypyodbc](http://web2py.com/books/default/reference/29/pypyodbc)] |
| FireBird   | kinterbasdb [[kinterbasdb](http://web2py.com/books/default/reference/29/kinterbasdb)] or fdb or pyodbc |
| DB2        | pyodbc [[pyodbc](http://web2py.com/books/default/reference/29/pyodbc)] |
| Informix   | informixdb [[informixdb](http://web2py.com/books/default/reference/29/informixdb)] |
| Ingres     | ingresdbi [[ingresdbi](http://web2py.com/books/default/reference/29/ingresdbi)] |
| Cubrid     | cubriddb [[cubridb](http://web2py.com/books/default/reference/29/cubridb)] |
| Sybase     | Sybase [[Sybase](http://web2py.com/books/default/reference/29/Sybase)] |
| Teradata   | pyodbc [[Teradata](http://web2py.com/books/default/reference/29/Teradata)] |
| SAPDB      | sapdb [[SAPDB](http://web2py.com/books/default/reference/29/SAPDB)] |
| MongoDB    | pymongo [[pymongo](http://web2py.com/books/default/reference/29/pymongo)] |
| IMAP       | imaplib [[IMAP](http://web2py.com/books/default/reference/29/IMAP)] |

`sqlite3`, `pymysql`, 并`imaplib`随 web2py 一起发布。对 MongoDB 的支持是实验性的。IMAP 选项允许使用 DAL 访问 IMAP。



### DAL：快速浏览

web2py 定义了构成 DAL 的以下类：

**DAL**对象代表一个数据库连接。例如：

```
db = DAL('sqlite://storage.sqlite')
```

**Table**表示一个数据库表。您不直接实例化 Table；相反，`DAL.define_table`实例化它。

```
db.define_table('mytable', Field('myfield')) 
```

Table 最重要的方法是：

`insert`, `truncate`,`drop`和`import_from_csv_file`.

**Field**表示一个数据库字段。它可以被实例化并作为参数传递给`DAL.define_table`.

**DAL 行**

是数据库选择返回的对象。可以将其视为`Row`行列表：



```
rows = db(db.mytable.myfield != None).select()
```

**row**包含字段值。

```
for row in rows:
    print row.myfield
```

**Query**是一个表示 SQL “where”子句的对象：

```
myquery = (db.mytable.myfield != None) | (db.mytable.myfield > 'A')
```

**Set**是表示一组记录的对象。它最重要的方法是`count`、`select`、`update`和`delete`。例如：

```
myset = db(myquery)
rows = myset.select()
myset.update(myfield='somevalue')
myset.delete()
```

**表达式**类似于`orderby`or`groupby`表达式。Field 类是从 Expression 派生的。这是一个例子。

```
myorder = db.mytable.myfield.upper() | db.mytable.id
db().select(db.table.ALL, orderby=myorder)
```



### 单独使用 DAL

DAL 可以通过以下方式在非 web2py 环境中使用

```
from pydal import DAL, Field
```





### DAL构造函数

基本用途：

```
>>> db = DAL('sqlite://storage.sqlite')
```

数据库现在已连接，连接存储在全局变量`db`中。

您可以随时检索连接字符串。

```
>>> db._uri
sqlite://storage.sqlite
```

和数据库名称

```
>>> db._dbname
sqlite
```

连接字符串称为 a `_uri`，因为它是统一资源标识符的一个实例。

DAL 允许与同一个数据库或不同数据库，甚至是不同类型的数据库的多个连接。现在，我们将假设存在单个数据库，因为这是最常见的情况。



#### DAL signature

```
DAL(uri='sqlite://dummy.db',
    pool_size=0,
    folder=None,
    db_codec='UTF-8',
    check_reserved=None,
    migrate=True,
    fake_migrate=False,
    migrate_enabled=True,
    fake_migrate_all=False,
    decode_credentials=False,
    driver_args=None,
    adapter_args=None,
    attempts=5,
    auto_import=False,
    bigint_id=False,
    debug=False,
    lazy_tables=False,
    db_uid=None,
    do_connect=True,
    after_connection=None,
    tables=None,
    ignore_field_case=True,
    entity_quoting=False,
    table_hash=None)
```





#### 连接字符串（uri 参数）

通过创建 DAL 对象的实例来建立与数据库的连接：

```
db = DAL('sqlite://storage.sqlite', pool_size=0)
```

`db`不是关键字；它是一个存储连接对象的局部变量`DAL`。你可以自由地给它一个不同的名字。DAL的构造函数需要一个参数，即连接字符串。连接字符串是唯一依赖于特定后端数据库的 web2py 代码。以下是特定类型支持的后端数据库的连接字符串示例（在所有情况下，我们假设数据库在其默认端口上从 localhost 运行并命名为“test”）

| **SQLite**           | `sqlite://storage.sqlite`                                    |
| -------------------- | ------------------------------------------------------------ |
| **MySQL**            | `mysql://username:password@localhost/test?set_encoding=utf8mb4` |
| **PostgreSQL**       | `postgres://username:password@localhost/test`                |
| **MSSQL (legacy)**   | `mssql://username:password@localhost/test`                   |
| **MSSQL (>=2005)**   | `mssql3://username:password@localhost/test`                  |
| **MSSQL (>=2012)**   | `mssql4://username:password@localhost/test`                  |
| **FireBird**         | `firebird://username:password@localhost/test`                |
| **Oracle**           | `oracle://username/password@test`                            |
| **DB2**              | `db2://username:password@test`                               |
| **Ingres**           | `ingres://username:password@localhost/test`                  |
| **Sybase**           | `sybase://username:password@localhost/test`                  |
| **Informix**         | `informix://username:password@test`                          |
| **Teradata**         | `teradata://DSN=dsn;UID=user;PWD=pass;DATABASE=test`         |
| **Cubrid**           | `cubrid://username:password@localhost/test`                  |
| **SAPDB**            | `sapdb://username:password@localhost/test`                   |
| **IMAP**             | `imap://user:password@server:port`                           |
| **MongoDB**          | `mongodb://username:password@localhost/test`                 |
| **Google/SQL**       | `google:sql://project:instance/database`                     |
| **Google/NoSQL**     | `google:datastore`                                           |
| **Google/NoSQL/NDB** | `google:datastore+ndb`                                       |

请注意，在 SQLite 中，数据库由单个文件组成。如果它不存在，则创建它。该文件每次被访问时都会被锁定。对于 MySQL、PostgreSQL、MSSQL、FireBird、Oracle、DB2、Ingres 和 Informix，数据库“test”必须在 web2py 之外创建。建立连接后，web2py 将适当地创建、更改和删除表。

在 MySQL 连接字符串中，`?set_encoding=utf8mb4`末尾的 将编码设置为 UTF-8 并避免`Invalid utf8 character string:`在由四个字节组成的 Unicode 字符上出错，因为默认情况下，MySQL 只能处理由一到三个字节组成的 Unicode 字符。[[[mathiasbyensbe]](http://web2py.com/books/default/reference/29/mathiasbyensbe)]

在 Google/NoSQL 的情况下，该`+ndb`选项打开 NDB。NDB 使用 Memcache 缓冲区来读取经常访问的数据。这是完全自动的，在数据存储级别完成，而不是在 web2py 级别。

也可以将连接字符串设置为`None`. 在这种情况下，DAL 不会连接到任何后端数据库，但仍然可以访问 API 进行测试。

有时您可能需要生成 SQL，就好像您有连接但没有实际连接到数据库一样。这可以通过

```
db = DAL('...', do_connect=False)
```

在这种情况下，您将能够调用`_select`、`_insert`、`_update`和`_delete`来生成 SQL，但不能调用`select`、`insert`、`update`和`delete`。在大多数情况下，`do_connect=False`即使没有所需的数据库驱动程序，您也可以使用。

请注意，默认情况下 web2py 对数据库使用 utf8 字符编码。如果您使用行为不同的现有数据库，则必须使用可选参数更改它，`db_codec`例如

```
db = DAL('...', db_codec='latin1')
```

否则你会得到 UnicodeDecodeError 的tickets。



#### 连接池

DAL 构造函数的一个常见参数是`pool_size`; 它默认为零。

由于为每个请求建立一个新的数据库连接相当慢，web2py 实现了一种连接池机制。一旦建立连接并且页面已经被提供并且事务完成，连接不会关闭而是进入池中。当下一个 http 请求到达时，web2py 尝试从池中回收连接并将其用于新事务。如果池中没有可用的连接，则建立一个新的连接。

当 web2py 启动时，池总是空的。池增长到`pool_size`并发请求的值和最大数量之间的最小值。这意味着如果`pool_size=10`我们的服务器从不接收超过 5 个并发请求，那么实际池大小只会增长到 5。如果`pool_size=0`那时不使用连接池。

池中的连接在线程之间按顺序共享，从某种意义上说，它们可能被两个不同但不是同时的线程使用。每个 web2py 进程只有一个池。

SQLite 和 Google App Engine 会忽略该`pool_size`参数。SQLite 忽略了连接池，因为它不会产生任何好处。



#### 连接失败（attempts 参数）

如果 web2py 无法连接到数据库，它会等待 1 秒，默认情况下会再次尝试最多 5 次，然后才宣布失败。在连接池的情况下，数据库端可能会关闭一个保持打开但未使用一段时间的池连接。由于重试功能，web2py 会尝试重新建立这些断开的连接。尝试次数通过尝试参数设置。



#### 懒人表

设置`lazy_tables = True`提供了主要的性能提升。见下文：[惰性表](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#lazy_tables)



#### 无模型应用

为您的应用程序模型使用 web2py 的模型目录非常方便且高效。使用惰性表和条件模型，即使对于大型应用程序，性能通常也是可以接受的。许多有经验的开发人员使用这是生产环境。

但是，可以在控制器功能或模块内按需定义 DAL 表。当表定义的数量或复杂性超过了惰性表和条件模型的使用时，这可能是有意义的。

这被 web2py 社区称为“无模型”开发。这意味着更少使用模型目录中 Python 文件的自动执行。这并不意味着放弃模型、视图和控制器的概念。

Web2py 在模型目录中自动执行 Python 代码会为您执行以下操作：

1. 每次处理请求时都会自动运行模型
2. 模型访问 web2py 的全局范围。

当使用 -M 命令行选项启动 web2py 时，模型还可以提供有用的交互式 shell 会话。

另外，请记住可维护性：其他 web2py 开发人员希望在模型目录中找到模型定义。

要使用“无模型”方法，您需要负责完成这两项内务管理任务。您可以在需要时调用表定义，并通过当前对象提供对全局范围的必要访问（如[第 4 章](http://web2py.com/books/default/chapter/29/04#current_object)所述）。

例如，典型的无模型应用程序可能会将数据库连接对象的定义保留在模型文件中，但根据控制器功能按需定义表。

典型情况是将表定义移动到模块文件（保存在模块目录中的 Python 文件）。

如果在名为“table_setup.py”的模块中调用定义一组表的函数，则`define_employee_tables()`想要引用与员工记录相关的表以生成 SQLFORM 的控制器需要`define_employee_tables()`在访问任何表之前调用该函数。该`define_employee_tables()`函数需要访问数据库连接对象才能定义表。这就是为什么您需要正确使用`current`包含的模块文件中的对象`define_employee_tables()`（如上所述）。



#### 复制数据库

的第一个参数`DAL(...)`可以是 URI 列表。在这种情况下，web2py 会尝试连接到它们中的每一个。这样做的主要目的是处理多个数据库服务器并在它们之间分配工作负载）。这是一个典型的用例：

```
db = DAL(['mysql://...1', 'mysql://...2', 'mysql://...3'])
```

在这种情况下，DAL 会尝试连接到第一个，如果失败，它将尝试第二个和第三个。这也可用于在数据库主从配置中分配负载。[我们将在第 13 章](http://web2py.com/books/default/chapter/29/13#Using-replicated-databases)的可扩展性背景下对此进行更多讨论。



#### 保留关键字

`check_reserved`告诉构造函数根据目标后端数据库中的保留 SQL 关键字检查表名和列名。`check_reserved`默认为无。

这是包含数据库后端适配器名称的字符串列表。

适配器名称与 DAL 连接字符串中使用的名称相同。因此，如果您想检查 PostgreSQL 和 MSSQL，那么您的连接字符串将如下所示：

```
db = DAL('sqlite://storage.sqlite', check_reserved=['postgres', 'mssql'])
```

DAL 将按照与列表相同的顺序扫描关键字。

有两个额外的选项“all”和“common”。如果指定 all，它将检查所有已知的 SQL 关键字。如果您指定 common，它将仅检查常见的 SQL 关键字，例如`SELECT`, `INSERT`,`UPDATE`等。

对于受支持的后端，您还可以指定是否还要检查非保留 SQL 关键字。在这种情况下，您将附加`_nonreserved`到名称。例如：

```
check_reserved=['postgres', 'postgres_nonreserved']
```

以下数据库后端支持保留字检查。



| **PostgreSQL** | `postgres(_nonreserved)` |
| -------------- | ------------------------ |
| **MySQL**      | `mysql`                  |
| **FireBird**   | `firebird(_nonreserved)` |
| **MSSQL**      | `mssql`                  |
| **Oracle**     | `oracle`                 |



#### 数据库引用和案例设置





在 DAL 中默认启用 SQL 实体的引用，即：

```
entity_quoting = True
```

这样，标识符在 DAL 生成的 SQL 中自动引用。在 SQL 级别，关键字和不带引号的标识符不区分大小写，因此引用 SQL 标识符使其区分大小写。

> 请注意，根据 SQL 标准，后端引擎应始终将未加引号的标识符折叠为小写，但并非所有引擎都符合这一点（例如 PostgreSQL 默认折叠为大写）。

默认情况下，DAL 也忽略字段大小写，以更改此用法：

```
ignore_field_case = False
```

为了确保在 python 和 DB 模式中使用相同的名称，您必须安排上述两个设置。这是一个例子：

```
db = DAL(ignore_field_case=False)
db.define_table('table1', Field('column'), Field('COLUMN'))
query = db.table1.COLUMN != db.table1.column
    
```



#### 建立安全连接

有时有必要（并且建议）使用安全连接连接到您的数据库，特别是如果您的数据库与您的应用程序不在同一台服务器上。在这种情况下，您需要将其他参数传递给数据库驱动程序。有关详细信息，您应该参考数据库驱动程序文档。

对于带有 psycopg2 的 PostgreSQL，它应该如下所示：

```
DAL('postgres://user_name:user_password@server_addr/db_name',
    driver_args={'sslmode': 'require', 'sslrootcert': 'root.crt',
                 'sslcert': 'postgresql.crt', 'sslkey': 'postgresql.key'})
```

其中参数`sslrootcert`，`sslcert`并且`sslkey`应该包含文件的完整路径。您应该参考 PostgreSQL 文档，了解如何配置 PostgreSQL 服务器以接受安全连接。



#### 其他 DAL 构造函数参数

##### 数据库文件夹位置

`folder`设置将创建迁移文件的位置（有关详细信息，请参阅本章中的[迁移部分）。](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#table_migrations)它也用于 SQLite 数据库。在 web2py 中自动设置。在 web2py 之外使用 DAL 时设置路径。

##### 默认迁移设置

DAL 构造函数迁移设置是影响默认值和全局行为的布尔值。

`migrate = True`为所有表设置默认迁移行为

`fake_migrate = False`为所有表设置默认 fake_migrate 行为

`migrate_enabled = True`如果设置为 False 禁用所有迁移

`fake_migrate_all = False`如果设置为 True，则假迁移所有表



#### 尝试使用 web2py shell

你可以使用 web2py shell 来试验 DAL API，它可以使用`-S`命令行选项（在[第 4 章](http://web2py.com/books/default/chapter/29/04#CommandLineOptions)阅读更多内容）。

> 您需要选择一个应用程序来运行 shell，注意数据库更改可能是持久的。所以要小心，创建一个新的应用程序来进行测试，而不是篡改现有的应用程序。

首先创建一个连接。例如，您可以使用 SQLite。当您更改后端引擎时，此讨论中的任何内容都不会改变。

请注意，大多数包含 python 提示符的代码片段`>>>`都可以通过纯 shell 直接执行，您可以使用`-PS`命令行选项获取它。





### 表构造函数





表在 DAL 中通过`define_table`.



#### 定义表签名

define_table 方法的签名是：

```
define_table(tablename, *fields, **kwargs)
```

它接受一个强制性的表名和一个可选的`Field`实例数（甚至没有）。您还可以传递一个`Table`（或子类）对象而不是一个对象`Field`，这会将所有字段（但“id”）克隆并添加到定义表中。其他可选的关键字参数是：`rname`, `redefine`, `common_filter`, `fake_migrate`, `fields`, `format`, `migrate`, `on_define`, `plural`, `polymodel`, `primarykey`, `sequence_name`, `singular`, `table_class`, 和`trigger_name`, 将在下面讨论。

例如：

```
>>> db.define_table('person', Field('name'))
<Table person (id, name)>
```

它定义、存储和返回一个`Table`名为“person”的对象，其中包含一个字段（列）“name”。这个对象也可以通过 访问`db.person`，所以你不需要捕获define_table返回的值。



#### `id`: 关于主键的注意事项

不要声明名为“id”的字段，因为无论如何都是由 web2py 创建的。默认情况下，每个表都有一个名为“id”的字段。它是一个自增整数字段（通常从 1 开始），用于交叉引用和使每条记录唯一，因此“id”是主键。（注意：从 1 开始的 id 计数器是特定于后端的。例如，这不适用于 Google App Engine NoSQL。）

或者，您可以定义一个字段，`type='id'`web2py 将使用该字段作为自动增量 id 字段。除非访问具有不同名称的主键的旧数据库表，否则不建议这样做。有一些限制，您还可以使用`primarykey`参数使用不同的主键。



#### `plural`和`singular`

Smartgrid 对象可能需要知道表的单复数名称。默认值是智能的，但这些参数允许您具体化。[第 7 章](http://web2py.com/books/default/chapter/29/07#SQLFORM-smartgrid)介绍了Smartgrid。



#### `redefine`

表只能定义一次，但您可以强制 web2py 重新定义现有表：

```
db.define_table('person', Field('name'))
db.define_table('person', Field('name'), redefine=True)
```

如果表定义发生变化，重新定义可能会触发迁移。





#### `format`: 记录表示

它是可选的，但建议使用参数指定记录的格式表示`format`。

```
db.define_table('person', Field('name'), format='%(name)s')
```

或者

```
db.define_table('person', Field('name'), format='%(name)s %(id)s')
```

甚至更复杂的使用函数：

```
db.define_table('person', Field('name'),
                format=lambda r: r.name or 'anonymous')
```

format 属性将用于两个目的：

- 在选择/选项下拉列表中表示引用的记录。
- 为引用该表的所有字段设置`db.othertable.otherfield.represent`属性。这意味着 SQLTABLE 不会按 id 显示引用，而是使用格式首选表示。（查看本章[视图部分的序列化行以了解有关 SQLTABLE 的更多信息。）](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#sqltable)



#### `rname`： 真正的名字

`rname`为表设置数据库后端名称。这使得 web2py 表名成为别名，并且`rname`是构建后端查询时使用的真实名称。为了说明一种用途，`rname`可用于提供 MSSQL 完全限定的表名来访问服务器上属于其他数据库的表：

```
rname = 'db1.dbo.table1'
```







#### `primarykey`: 支持旧表

`primarykey`帮助支持具有现有主键的遗留表，甚至是多部分的。请参阅本章中的[遗留数据库和键控表部分。](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#LegacyDatabases)



#### `migrate`,`fake_migrate`

`migrate`设置表的迁移选项。有关详细信息，请参阅本章中的[迁移部分。](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#table_migrations)



#### `table_class`

如果将自己的Table类定义为pydal.objects.Table的子类，可以在这里提供；这允许您扩展和覆盖方法。例子：

```
from pydal.objects import Table

class MyTable(Table):
    ...

db.define_table(..., table_class=MyTable)
```



#### `sequence_name`

自定义表序列的名称（如果数据库支持）。可以创建一个 SEQUENCE（从 1 开始并以 1 递增）或将其用于具有自定义序列的旧表。

> 请注意，必要时，web2py 默认会自动创建序列。



#### `trigger_name`

涉及到`sequence_name`。与一些不支持自动递增数字字段的后端相关。



#### `polymodel`

对于谷歌应用引擎



#### `on_define`

`on_define`是在实例化lazy_table 时触发的回调，尽管如果表不是惰性的，无论如何都会调用它。这允许对表进行动态更改，而不会失去延迟实例化的优势。

例子：

```
db = DAL(lazy_tables=True)
db.define_table('person',
    Field('name'),
    Field('age', 'integer'),
    on_define=lambda table: [
        table.name.set_attributes(requires=IS_NOT_EMPTY(), default=''),
        table.age.set_attributes(requires=IS_INT_IN_RANGE(0, 120), default=30) ])
```

请注意，此示例显示了如何使用`on_define`，但实际上并不是必需的。可以将简单的`requires`值添加到字段定义中，并且表仍然是惰性的。但是，`requires`将 Set 对象作为第一个参数，例如 IS_IN_DB，将进行如下查询

```
db.sometable.somefield == some_value
```

这将导致`sometable`提前定义。这是被救下的局面`on_define`。







#### 懒惰的表，一个主要的性能提升

web2py 模型在控制器之前执行，因此所有表都是在每次请求时定义的。并非所有表都需要处理每个请求，因此可能会浪费一些定义表的时间。条件模型（见[第 4 章](http://web2py.com/books/default/chapter/29/04#conditional_models)）可以提供帮助，但是 web2py 通过lazy_tables 提供了很大的性能提升。这个特性意味着表的创建被推迟到表被实际引用。通过 DAL 构造函数初始化数据库时启用惰性表。它需要设置lazy_tables 参数：

```
DAL(..., lazy_tables=True)
```

这是 web2py 中最显着的响应时间性能提升之一。





#### 向字段和表添加属性

如果您需要为字段添加自定义属性，您可以简单地执行以下操作：

```
db.table.field.extra = {}
```

“额外”不是关键字；它是现在附加到字段对象的自定义属性。您也可以对表执行此操作，但必须在它们前面加上下划线以避免与字段发生命名冲突：

```
db.table._extra = {}
```



### 字段构造函数

这些是 Field 构造函数的默认值：

```
Field(fieldname, type='string', length=None, default=DEFAULT,
      required=False, requires=DEFAULT,
      ondelete='CASCADE', notnull=False, unique=False,
      uploadfield=True, widget=None, label=None, comment=None,
      writable=True, readable=True, searchable=True, listable=True,
      update=None, authorize=None, autodelete=False, represent=None,
      uploadfolder=None, uploadseparate=None, uploadfs=None,
      compute=None, filter_in=None, filter_out=None,
      custom_qualifier=None, map_none=None, rname=None)
```

其中 DEFAULT 是一个特殊值，用于允许参数值为 None。

并非所有这些都与每个领域相关。`length`仅与“字符串”类型的字段相关。`uploadfield`、`authorize`和`autodelete`仅与“上传”类型的字段相关。`ondelete`仅与“参考”和“上传”类型的字段相关。

- `length`设置“字符串”、“密码”或“上传”字段的最大长度。如果`length`未指定，则使用默认值，但不保证默认值向后兼容。*为避免升级时不必要的迁移，我们建议您始终指定字符串、密码和上传字段的长度。*
- `default`设置字段的默认值。如果未明确指定值，则在执行插入时使用默认值。它还用于预填充使用 SQLFORM 从表构建的表单。请注意，默认值不是一个固定值，而是一个函数（包括一个 lambda 函数），它为字段返回适当类型的值。在这种情况下，即使在单个事务中插入多条记录，也会为插入的每条记录调用一次该函数。
- `required`告诉 DAL 如果未明确指定此字段的值，则不应在此表上允许插入。
- `requires`是验证器或验证器列表。DAL 不使用它，但 SQLFORM 使用它。给定类型的默认验证器显示在下一节中。

> 请注意，`requires=...`在表单级别`required=True`强制执行，在 DAL（插入）级别强制执行，而`notnull`在`unique`数据库`ondelete`级别强制执行。虽然它们有时看起来是多余的，但在使用 DAL 编程时保持区别很重要。

- `rname`为字段提供“真实名称”，即数据库适配器已知的字段名称；当使用该字段时，它是发送到数据库的 rname 值。该字段的 web2py 名称实际上是一个别名。

- `ondelete`转换为“ON DELETE”SQL 语句。默认情况下，它设置为“级联”。这告诉数据库，当它删除一条记录时，它也应该删除所有引用它的记录。要禁用此功能，请设置`ondelete`为“NO ACTION”或“SET NULL”。

- `notnull=True`转换为“NOT NULL”SQL 语句。它可以防止数据库为该字段插入空值。

- `unique=True`转换为“UNIQUE”SQL 语句，并确保该字段的值在表中是唯一的。它在数据库级别强制执行。

- `uploadfield`仅适用于“上传”类型的字段。“upload”类型的字段存储保存在其他地方的文件的名称，默认情况下在应用程序“uploads/”文件夹下的文件系统中。如果`uploadfield`设置为 True，则文件存储在同一个表中的 blob 字段中，并且值为`uploadfield`blob 字段的名称。这将在本章稍后的[更多关于上传的部分中更详细地讨论。](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#More-on-uploads)

- ```
  uploadfolder
  ```

  设置上传文件的文件夹。默认情况下，上传的文件会进入应用程序的“uploads/”文件夹，即进入

  ```
  os.path.join(request.folder, 'uploads')
  ```

  （目前 MongoAdapter 似乎不是这种情况）。例如：

  ```
  字段( ... ,  uploadfolder = os .path . join ( request . folder , 'static/temp' ) ) 
  ```

  将文件上传到“web2py/applications/myapp/static/temp”文件夹。

- `uploadseparate`如果设置为 True 将上传*uploadfolder*文件夹的不同子文件夹下的文件。这已优化以避免同一文件夹/子文件夹下的文件过多。注意：您不能在`uploadseparate`不破坏现有上传链接的情况下将值从 True 更改为 False。web2py 要么使用单独的子文件夹，要么不使用。上传文件后更改行为将阻止 web2py 检索这些文件。如果发生这种情况，可以移动文件并解决问题，但这里没有描述。





- ```
  uploadfs
  ```

  允许您指定不同的文件系统来上传文件，包括 Amazon S3 存储或远程 SFTP 存储。

  > 您需要安装 PyFileSystem 才能正常工作。`uploadfs`必须指向 PyFileSystem。

- `autodelete`确定在删除引用文件的记录时是否应删除相应的上传文件。仅适用于“上传”字段。但是，由于 CASCADE 操作而被数据库本身删除的记录不会触发 web2py 的自动删除。web2py Google 小组有解决方法讨论。

- `widget`必须是可用的小部件对象之一，包括自定义小部件，例如：`SQLFORM.widgets.string.widget`. 稍后将讨论可用小部件的列表。每个字段类型都有一个默认小部件。

- `label`是一个字符串（或帮助程序或可以序列化为字符串的东西），其中包含要用于自动生成表单中该字段的标签。

- `comment` 是一个字符串（或帮助程序或可以序列化为字符串的东西），其中包含与此字段关联的注释，并将显示在自动生成的表单中输入字段的右侧。

- `writable`声明一个字段在表单中是否可写。

- `readable`声明一个字段在表单中是否可读。如果一个字段既不可读也不可写，它不会显示在创建和更新表单中。

- `searchable`声明一个字段是否可以在网格中搜索（`SQLFORM.grid`并`SQLFORM.smartgrid`在[第 7 章](http://web2py.com/books/default/chapter/29/07#SQLFORM-grid-and-SQLFORM-smartgrid)中描述）。请注意，要搜索的字段也必须是可读的。

- `listable`声明一个字段在网格中是否可见（列出多条记录时）

- `update`包含更新记录时该字段的默认值。

- `compute`是一个可选功能。如果插入或更新记录，将执行计算函数，并用函数结果填充字段。记录作为 a 传递给计算函数`dict`，并且 dict 将不包含该值或任何其他计算字段的当前值。

- `authorize`可用于要求对相应字段进行访问控制，仅适用于“上传”字段。它将在身份验证和授权的上下文中更详细地讨论。

- ```
  represent
  ```

  可以是 None 或者可以指向一个函数，该函数接受一个字段值并返回字段值的替代表示。例子：

  ```
  db.mytable.name.represent = lambda name, row: name.capitalize()
  db.mytable.other_id.represent = lambda oid, row: row.myfield
  db.mytable.some_uploadfield.represent = lambda val, row: A('get it', _href=URL('download', args=val))
  ```

- `filter_in`并且`filter_out`可以设置为可调用对象以进一步处理字段的值。`filter_in`在插入或更新之前传递要写入数据库的字段值，同时`filter_out`传递在字段分配之前从数据库中检索的值。然后使用可调用对象返回的值。请参阅本章中的[filter_in 和 filter_out部分。](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#filter_in-and-filter_out)

- `custom_qualifier`是在创建表时要使用的字段的自定义 SQL 限定符（不能用于“id”、“reference”或“big-reference”类型的字段）。





#### 字段类型

| **field type**           | **default field validators**                     |
| ------------------------ | ------------------------------------------------ |
| `string`                 | `IS_LENGTH(length)` default length is 512        |
| `text`                   | `IS_LENGTH(length)` default length is 32768      |
| `blob`                   | `None` default length is 2**31 (2 GiB)           |
| `boolean`                | `None`                                           |
| `integer`                | `IS_INT_IN_RANGE(-2**31, 2**31)`                 |
| `double`                 | `IS_FLOAT_IN_RANGE(-1e100, 1e100)`               |
| `decimal(n,m)`           | `IS_DECIMAL_IN_RANGE(-10**10, 10**10)`           |
| `date`                   | `IS_DATE()`                                      |
| `time`                   | `IS_TIME()`                                      |
| `datetime`               | `IS_DATETIME()`                                  |
| `password`               | `IS_LENGTH(length)` default length is 512        |
| `upload`                 | `None` default length is 512                     |
| `reference <table>`      | `IS_IN_DB(db, table.field, format)`              |
| `list:string`            | `None`                                           |
| `list:integer`           | `None`                                           |
| `list:reference <table>` | `IS_IN_DB(db, table._id, format, multiple=True)` |
| `json`                   | `IS_EMPTY_OR(IS_JSON())` default length is 512   |
| `bigint`                 | `IS_INT_IN_RANGE(-2**63, 2**63)`                 |
| `big-id`                 | `None`                                           |
| `big-reference`          | `None`                                           |

十进制需要并返回值作为`Decimal`对象，如 Python`decimal`模块中所定义。SQLite 不处理该`decimal`类型，因此在内部我们将其视为`double`. (n,m) 分别是总位数和小数点后的位数。

The `big-id` and, `big-reference` are only supported by some of the database engines and are experimental. They are not normally used as field types unless for legacy tables, however, the DAL constructor has a `bigint_id` argument that when set to `True` makes the `id` fields and `reference` fields `big-id` and `big-reference` respectively.

些`list:<type>`字段是特殊的，因为它们旨在利用 NoSQL 上的某些非规范化功能（在 Google App Engine NoSQL 的情况下，字段类型`ListProperty`和`StringListProperty`）并将它们反向移植到所有其他受支持的关系数据库中。在关系数据库中，列表存储为一个`text`字段。这些项目由 a 分隔，并且字符串中的`|`每个项目都被转义为 a 。它们在[list: 中讨论并包含](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#list_types)本章中的部分。`|``||`

`json`字段类型几乎可以解释。它可以存储任何 json 可序列化对象。它专为 MongoDB 工作而设计，并向后移植到其他数据库适配器以实现可移植性。

`blob`领域也很特殊。默认情况下，二进制数据在存储到实际数据库字段之前以 base64 编码，并在提取时进行解码。这具有在 blob 字段中使用比所需存储空间多 33% 的负面影响，但具有使通信独立于后端特定的转义约定的优势。



#### 运行时字段和表修改

字段和表的大部分属性在定义后都可以修改：

```
>>> db.define_table('person', Field('name', default=''), format='%(name)s')
<Table person (id, name)>
>>> db.person._format = '%(name)s/%(id)s'
>>> db.person.name.default = 'anonymous'
```

请注意，表的属性通常带有下划线前缀，以避免与可能的字段名称冲突。

您可以列出为给定数据库连接定义的表：

```
>>> db.tables
['person']
```

您可以查询表的类型：

```
>>> type(db.person)
<class 'pydal.objects.Table'>
```

您可以使用不同的语法访问表：

```
>>> db.person is db['person']
True
```

您还可以列出为给定表定义的字段：

```
>>> db.person.fields
['id', 'name']
```

同样，您可以通过多种等效方式从其名称中访问字段：

```
>>> type(db.person.name)
<class 'pydal.objects.Field'>
>>> db.person.name is db.person['name']
True 
```

给定一个字段，您可以访问其定义中设置的属性：

```
>>> db.person.name.type
string
>>> db.person.name.unique
False
>>> db.person.name.notnull
False
>>> db.person.name.length
32
```

包括它的父表、表名和父连接：

```
>>> db.person.name._table == db.person
True
>>> db.person.name._tablename == 'person'
True
>>> db.person.name._db == db
True
```

字段也有方法。其中一些用于构建查询，我们稍后会看到它们。字段对象的一个特殊方法是`validate`，它调用字段的验证器。

```
>>> db.person.name.validate('John')
('John', None)
```

它返回一个元组`(value, error)`。`error`是`None`输入是否通过验证。





### 迁移

`define_table`检查对应的表是否存在。如果没有，它会生成 SQL 来创建它并执行 SQL。如果表确实存在但与定义的表不同，它会生成 SQL 以更改表并执行它。如果一个字段改变了类型但没有改变名称，它会尝试转换数据（如果你不想要这个，你需要重新定义表两次，第一次，让 web2py 通过删除它来删除字段，第二次添加新定义的字段，以便 web2py 可以创建它。）。如果表存在并且与当前定义匹配，它将不理会它。在所有情况下，它将创建`db.person`代表表的对象。

我们将这种行为称为“迁移”。web2py 在文件“sql.log”中记录所有迁移和迁移尝试。

> 请注意，默认情况下 web2py 使用“app/databases”文件夹存储日志文件和它需要的所有其他迁移文件。您可以将此设置`folder`参数更改为 DAL。要设置不同的日志文件名，例如“migrate.log”，您可以这样做
>
> ```
> db = DAL(..., adapter_args=dict(logfile='migrate.log'))
> ```

的第一个参数`define_table`始终是表名。其他未命名的参数是字段（Field）。该函数还接受一个名为“migrate”的可选关键字参数：

```
分贝。define_table ( 'person' ,  ... ,  migrate = 'person.table' )
```

migrate 的值是 web2py 存储此表的内部迁移信息的文件名。这些文件非常重要，并且在相应的表存在时绝不应该删除。如果表已被删除并且相应的文件仍然存在，则可以手动将其删除。默认情况下，迁移设置为 True。这会导致 web2py 从连接字符串的哈希中生成文件名。如果 migrate 设置为 False，则不执行迁移，并且 web2py 假定该表存在于数据存储中，并且它包含（至少）中列出的字段`define_table`。

同一应用程序中可能不会有两个表具有相同的迁移文件名。

DAL 类还采用“迁移”参数，该参数确定 migrate 调用的默认值`define_table`。例如，

```
db.define_table('person', ..., migrate='person.table')
```

`db.define_table`每当在没有 migrate 参数的情况下调用时，都会将 migrate 的默认值设置为 False 。

> 请注意，web2py 仅迁移新列、删除的列和列类型的更改（SQLite 除外）。web2py 不会迁移属性的更改，例如 、 、 和 的`default`值`unique`的`notnull`更改`ondelete`。

可以一次为所有表禁用迁移：

```
db = DAL('sqlite://storage.sqlite', migrate=False)
```

当两个应用程序共享同一个数据库时，这是推荐的行为。两个应用程序中只有一个应该执行迁移，另一个应该禁用它们。



### 修复损坏的迁移

迁移有两个常见问题，并且有一些方法可以从中恢复。

一个问题是 SQLite 特有的。SQLite 不强制列类型并且不能删除列。这意味着，如果您有一列类型为 string 的列并将其删除，则它并没有真正被删除。如果您再次使用不同的类型（例如 datetime）添加列，您最终会得到一个包含字符串的 datetime 列（出于实际目的的垃圾）。web2py 不会抱怨这一点，因为它不知道数据库中有什么，直到它尝试检索记录并失败。

如果 web2py 在选择记录时在某些解析函数中返回错误，很可能是由于上述问题导致列中的数据损坏。

解决方案包括更新表的所有记录并使用 None 更新相关列中的值。

另一个问题更通用，但在 MySQL 中很典型。MySQL 不允许在一个事务中使用多个 ALTER TABLE。这意味着 web2py 必须将复杂的事务分解成更小的事务（当时一个 ALTER TABLE）并一次提交一个。因此，复杂事务的一部分可能会被提交而一部分失败，从而使 web2py 处于损坏状态。为什么部分交易会失败？因为，例如，它涉及更改表并将字符串列转换为日期时间列，所以 web2py 尝试转换数据，但无法转换数据。web2py 会发生什么？它对实际存储在数据库中的表结构到底是什么感到困惑。

该解决方案包括启用虚假迁移：

```
db.define_table(...., migrate=True, fake_migrate=True)
```

这将根据表定义重建有关表的 web2py 元数据。尝试多个表定义以查看哪个有效（迁移失败之前的一个和迁移失败之后的一个）。一旦成功删除`fake_migrate=True`参数。

在尝试修复迁移问题之前，谨慎的做法是复制“applications/yourapp/databases/*.table”文件。

也可以一次修复所有表的迁移问题：

```
db = DAL(..., fake_migrate_all=True)
```

如果模型描述了数据库中不存在的表，这也会失败，但它可以帮助缩小问题范围。



### 迁移控制摘要

这个伪代码总结了各种迁移参数的逻辑：

```
if DAL.migrate_enabled and table.migrate:
   if DAL.fake_migrate_all or table.fake_migrate:
       perform fake migration
   else:
       perform migration
```



### `insert`

给定一个表，你可以插入记录

```
>>> db.person.insert(name="Alex")
1
>>> db.person.insert(name="Bob")
2
```

Insert 返回插入的每条记录的唯一“id”值。

您可以截断表，即删除所有记录并重置id的计数器。

```
>>> db.person.truncate()
```

现在，如果您再次插入一条记录，计数器将再次从 1 开始（这是特定于后端的，不适用于 Google NoSQL）：

```
>>> db.person.insert(name="Alex")
1
```

请注意，您可以将参数传递给`truncate`，例如，您可以告诉 SQLite 重新启动 id 计数器。

```
>>> db.person.truncate('RESTART IDENTITY CASCADE')
```

该参数在原始 SQL 中，因此是特定于引擎的。

web2py 还提供了一个 bulk_insert 方法

```
>>> db.person.bulk_insert([{'name': 'Alex'}, {'name': 'John'}, {'name': 'Tim'}])
[3, 4, 5]
```

它需要一个要插入的字段字典列表，并一次执行多个插入。它返回插入记录的“id”值列表。在受支持的关系数据库上，与循环和执行单独的插入相比，使用此功能没有任何优势，但在 Google App Engine NoSQL 上，有一个主要的速度优势。



### `commit`和`rollback`

在 web2py 发出 commit 命令之前，插入、截断、删除和更新操作实际上并没有提交。创建和删除操作可能会立即执行，具体取决于数据库引擎。对 web2py 操作的调用会自动包装在事务中。如果您通过 shell 执行命令，则需要手动提交：

```
>>> db.commit()
```

要检查它，让我们插入一条新记录：

```
>>> db.person.insert(name="Bob")
2
```

并回滚，即忽略自上次提交以来的所有操作：

```
>>> db.rollback()
```

如果您现在再次插入，计数器将再次设置为 2，因为先前的插入已回滚。

```
>>> db.person.insert(name="Bob")
2
```

模型、视图和控制器中的代码包含在 web2py 代码中，如下所示（伪代码）：

```
try:
    execute models, controller function and view
except:
    rollback all connections
    log the traceback
    send a ticket to the visitor
else:
    commit all connections
    save cookies, sessions and return the page  
```

因此，在模型、视图和控制器中，除非您需要更精细的控制，否则无需在 web2py中调用`commit` 或显式调用。`rollback`但是，在模块中，您需要使用`commit()`.



### Raw SQL



#### 定时查询

所有查询都由 web2py 自动计时。该变量`db._timings`是一个元组列表。每个元组都包含传递给数据库驱动程序的原始 SQL 查询以及执行时间（以秒为单位）。可以使用工具栏在视图中显示此变量：

```
{{=response.toolbar()}}
```



#### `executesql`

DAL 允许您显式地发出 SQL 语句。

```
>>> db.executesql('SELECT * FROM person;')
[(1, u'Massimo'), (2, u'Massimo')]
```

在这种情况下，返回值不会被 DAL 解析或转换，格式取决于特定的数据库驱动程序。选择的这种用法通常不需要，但索引更常见。

`executesql`接受五个可选参数：`placeholders`、`as_dict`、`fields`、`colnames`和`as_ordered_dict`.

`placeholders`是要替换的可选值序列，或者，如果 DB 驱动程序支持，它是一个字典，其键与 SQL 中的命名占位符匹配。

如果`as_dict`设置为 True，则 DB 驱动程序返回的结果游标将被转换为以 db 字段名称为键的字典序列。返回的结果与将**.as_list()**应用于普通选择`as_dict = True`时返回的结果相同：

```
[{'field1': val1_row1, 'field2': val2_row1}, {'field1': val1_row2, 'field2': val2_row2}]
```

`as_ordered_dict`很像，`as_dict`但前者确保结果字段（OrderedDict 键）的顺序反映了它们从 DB 驱动程序返回的顺序：

```
[OrderedDict([('field1', val1_row1), ('field2', val2_row1)]),
 OrderedDict([('field1', val1_row2), ('field2', val2_row2)])]
```

参数是与从数据库返回的字段匹配的`fields`DAL 字段对象列表。Field 对象应该是在 DAL 对象上定义的一个或多个 Table 对象的一部分。该`fields`列表可以包括一个或多个 DAL Table 对象，以补充或代替包括 Field 对象，或者它可以只是一个表（不在列表中）。在这种情况下，将从表中提取 Field 对象。

可以将参数指定为 tablename.fieldname 格式的字段名称列表，而不是指定`fields`参数。`colnames`同样，这些应该表示在 DAL 对象上定义的表和字段。

也可以指定两者`fields`和关联的`colnames`. 在这种情况下，`fields`除了 Field 对象之外，还可以包括 DAL Expression 对象。对于“fields”中的 Field 对象，关联的`colnames`必须仍然是 tablename.fieldname 格式。对于 中的 Expression 对象`fields`，关联的`colnames`可以是任意标签。

请注意，DAL Table 对象引用`fields`或`colnames`可以是虚拟表，并且不必表示数据库中的任何真实表。另外，请注意`fields`和`colnames`必须与从数据库返回的结果游标中的字段顺序相同。



#### `_lastsql`

无论 SQL 是使用 executesql 手动执行还是由 DAL 生成的 SQL，您始终可以在`db._lastsql`. 这对于调试目的很有用：

```
>>> rows = db().select(db.person.ALL)
>>> db._lastsql
SELECT person.id, person.name FROM person;
```

> web2py 从不使用“*”运算符生成查询。web2py 在选择字段时总是显式的。



### `drop`

最后，您可以删除表，所有数据都将丢失：

```
db.person.drop()
```



### 索引

目前 DAL API 不提供在表上创建索引的命令，但这可以使用`executesql`命令来完成。这是因为索引的存在会使迁移变得复杂，最好明确地处理它们。经常查询中使用的那些字段可能需要索引。

以下是如何[在 SQLite 中使用 SQL 创建索引](http://www.sqlite.org/lang_createindex.html)的示例：

```
db = DAL('sqlite://storage.sqlite')
db.define_table('person', Field('name'))
db.executesql('CREATE INDEX IF NOT EXISTS myidx ON person (name);')
```

其他数据库方言具有非常相似的语法，但可能不支持可选的“IF NOT EXISTS”指令。





### 遗留数据库和键控表

web2py 在某些情况下可以连接到遗留数据库。

最简单的方法是在满足这些条件时：

- 每个表必须有一个唯一的自增整数字段，称为“id”
- 必须使用“id”字段专门引用记录。

在访问现有表时，即当前应用程序中不是由 web2py 创建的表时，始终设置`migrate=False`.

如果遗留表有一个自增整数字段但它不称为“id”，web2py 仍然可以访问它，但表定义必须声明具有 'id' 类型的自增字段（即使用`FIeld('...', 'id')`）。

最后，如果遗留表使用的主键不是自动增量 id 字段，则可以使用“键控表”，例如：

```
db.define_table('account',
                Field('accnum', 'integer'),
                Field('acctype'),
                Field('accdesc'),
                primarykey=['accnum', 'acctype'],
                migrate=False)
```

- `primarykey`是组成主键的字段名称的列表。
- `NOT NULL`即使未指定，所有主键字段都有一个集合。
- 键控表只能引用其他键控表。
- 引用字段必须使用该`reference tablename.fieldname`格式。
- 该`update_record`功能不适用于键控表行。

> 目前仅 DB2、MSSQL、Ingres 和 Informix 支持键控表，但将添加其他引擎。

在撰写本文时，我们不能保证该`primarykey`属性适用于每个现有的旧表和每个支持的数据库后端。为简单起见，如果可能，我们建议创建一个具有自动递增 id 字段的数据库视图。



### 分布式事务

> 在撰写本文时，仅 PostgreSQL、MySQL 和 Firebird 支持此功能，因为它们公开了用于两阶段提交的 API。

假设您有两个（或更多）连接到不同的 PostgreSQL 数据库，例如：

```
db_a = DAL('postgres://...')
db_b = DAL('postgres://...')
```

在您的模型或控制器中，您可以同时提交它们：

```
DAL.distributed_transaction_commit(db_a, db_b)
```

失败时，此函数回滚并引发`Exception`.

在控制器中，当一个操作返回时，如果您有两个不同的连接并且您没有调用上述函数，web2py 将分别提交它们。这意味着有一个提交成功而一个失败的可能性。分布式事务可以防止这种情况发生。



### 更多关于上传

考虑以下模型：

```
db.define_table('myfile',
                Field('image', 'upload', default='path/to/file'))
```

在“上传”字段的情况下，可以选择将默认值设置为路径（绝对路径或相对于当前应用程序文件夹的路径），然后将默认值分配给未指定的每个新记录图片。

请注意，这样多条记录可能会结束引用相同的默认图像文件，这可能是`autodelete`启用的字段上的问题。当您不想允许图像字段重复（即引用同一文件的多个记录）但仍想为“上传”设置默认值时，您需要一种方法为每个新记录复制默认文件不指定图像。这可以使用引用默认文件作为`default`Field 参数的类文件对象来获得，甚至可以使用：

```
Field('image', 'upload', default=dict(data='<file_content>', filename='<file_name>'))
```

通常，插入是通过 SQLFORM 或 crud 表单（即 SQLFORM）自动处理的，但有时您已经在文件系统上拥有该文件并希望以编程方式上传它。这可以通过以下方式完成：

```
with open(filename, 'rb') as stream:
    db.myfile.insert(image=db.myfile.image.store(stream, filename))   
     
```

也可以以更简单的方式插入文件并`store`自动调用 insert 方法：

```
with open(filename, 'rb') as stream:
    db.myfile.insert(image=stream)  
    
```

在这种情况下，如果可用，则从流对象中获取文件名。

上传字段对象的`store`方法接受一个文件流和一个文件名。它使用文件名来确定文件的扩展名（类型），为文件创建一个新的临时名称（根据 web2py 上传机制）并将文件内容加载到这个新的临时文件中（除非另有说明，否则在上传文件夹下）。它返回新的临时名称，然后将其存储在表的`image`字段中`db.myfile`。

注意，如果文件要存储在关联的blob字段中而不是文件系统中，该`store`方法不会在blob字段中插入文件（因为`store`在插入之前调用），所以文件必须显式插入到blob中场地：

```
db.define_table('myfile',
                Field('image', 'upload', uploadfield='image_file'),
                Field('image_file', 'blob'))
with open(filename, 'rb') as stream:
    db.myfile.insert(image=db.myfile.image.store(stream, filename),
                     image_file=stream.read())
```

该`retrieve`方法与`store`.

当上传的文件存储在文件系统上时（如在普通的情况下`Field('image', 'upload')`），代码：

```
row = db(db.myfile).select().first()
(filename, fullname) = db.myfile.image.retrieve(row.image, nameonly=True)
```

检索用户在上传时看到的原始文件名（文件名）和存储文件的名称（全名，相对于应用程序文件夹的路径）。一般来说，电话：

```
(filename, stream) = db.myfile.image.retrieve(row.image)
```

检索原始文件名（文件名）和准备访问上传的文件数据（流）的类文件对象。

> 请注意，在`retrieve`上传的文件存储在文件系统上的情况下，返回的流是一个真实的文件对象。在这种情况下，请记住在完成调用后关闭文件`stream.close()`。

下面是一个安全使用的例子`retrieve`：

```
from contextlib import closing
import shutil
row = db(db.myfile).select().first()
(filename, stream) = db.myfile.image.retrieve(row.image)
with closing(stream) as src, closing(open(filename, 'wb')) as dest:
    shutil.copyfileobj(src, dest)
```



### `Query`, `Set`, `Rows`





让我们再次考虑之前定义（和删除）的表并插入三个记录：

```
>>> db.define_table('person', Field('name'))
<Table person (id, name)>
>>> db.person.insert(name="Alex")
1
>>> db.person.insert(name="Bob")
2
>>> db.person.insert(name="Carl")
3
```

您可以将表存储在变量中。例如，使用 variable `person`，您可以执行以下操作：

```
>>> person = db.person
```

您还可以将字段存储在变量中，例如`name`. 例如，您还可以这样做：

```
>>> name = person.name
```

您甚至可以构建查询（使用 ==、!=、<、>、<=、>=、like、belongs 等运算符）并将查询存储在变量`q`中，例如：

```
>>> q = name == 'Alex'
```

当您`db`使用查询调用时，您定义了一组记录。您可以将其存储在变量中`s`并编写：

```
>>> s = db(q)
```

请注意，到目前为止尚未执行任何数据库查询。DAL + Query 只需在此数据库中定义一组与查询匹配的记录。web2py 从查询中确定涉及哪些表（或哪些表），实际上，无需指定。



### `select`

给定一个 Set，`s`您可以使用以下命令获取记录`select`：

```
>>> rows = s.select()
```

它返回一个类的可迭代对象，`pydal.objects.Rows`其元素是 Row 对象。`pydal.objects.Row`对象就像字典一样，但它们的元素也可以作为属性访问，比如`gluon.storage.Storage`. 前者与后者不同，因为它的值是只读的。

Rows 对象允许循环选择结果并打印每一行的选定字段值：

```
>>> for row in rows:
...     print row.id, row.name
... 
1 Alex
```

您可以在一个语句中完成所有步骤：

```
>>> for row in db(db.person.name == 'Alex').select():
...     print row.name
... 
Alex
     
```

select 命令可以带参数。所有未命名的参数都被解释为您要获取的字段的名称。例如，您可以明确获取字段“id”和字段“name”：

```
>>> for row in db().select(db.person.id, db.person.name):
...     print row.name
... 
Alex
Bob
Carl
     
```

表属性 ALL 允许您指定所有字段：

```
>>> for row in db().select(db.person.ALL):
...     print row.id, row.name
... 
1 Alex
2 Bob
3 Carl
```

请注意，没有传递给 db 的查询字符串。web2py 明白，如果您想要表格人员的所有字段而不需要其他信息，那么您需要表格人员的所有记录。

等效的替代语法如下：

```
>>> for row in db(db.person).select():
...     print row.id, row.name
... 
1 Alex
2 Bob
3 Carl
      
```

而 web2py 明白，如果你在没有附加信息的情况下询问 table person 的所有记录，那么你想要 table person 的所有字段。

给定一排

```
>>> row = rows[0]
```

您可以使用多个等效表达式提取其值：

```
>>> row.name
Alex
>>> row['name']
Alex
>>> row('person.name')
Alex
```

在选择EN表达式而不是列时，后一种语法特别方便。我们稍后会展示这个。

你也可以做

```
rows.compact = False
```

禁用符号

```
rows[i].name
```

并启用不太紧凑的符号：

```
rows[i].person.name
```

是的，这是不寻常的，很少需要。

行对象也有两个重要的方法：

```
row.delete_record()
```

和

```
row.update_record(name="new value")
```



#### 使用基于迭代器的选择以减少内存使用

Python“迭代器”是一种“惰性求值”。他们一次一步“提供”数据；传统的 Python 循环在循环之前在内存中创建整个数据集。

select 的传统用法是：

```
for row in db(db.table).select():
    ...
```

但对于大量行，使用基于迭代器的替代方案会显着降低内存使用量：

```
for row in db(db.table).iterselect():
    ...
```

测试表明这也快了 10% 左右，即使在具有大 RAM 的机器上也是如此。



#### 使用表示渲染行

您可能希望重写 select 返回的行，以利用字段的表示设置中包含的格式信息。

```
rows = db(query).select()  
repr_row = rows.render(0)
```

如果你不指定索引，你会得到一个生成器来迭代所有的行：

```
for row in rows.render():
    print row.myfield
```

也可以应用于切片：

```
for row in rows[0:10].render():
    print row.myfield
```

如果您只想通过其“represent”属性转换所选字段，您可以在“fields”参数中列出它们：

```
repr_row = row.render(0, fields=[db.mytable.myfield])
```

请注意，它返回原始行的转换副本，因此没有 update_record（无论如何您都不想要）或 delete_record。



#### 捷径

DAL 支持各种代码简化快捷方式。特别是：

```
myrecord = db.mytable[id]
```

`id`如果存在，则返回给定的记录。如果`id`不存在，则返回`None`。上面的语句等价于

```
myrecord = db(db.mytable.id == id).select().first()  
```

您可以通过 id 删除记录：

```
del db.mytable[id]
```

这相当于

```
db(db.mytable.id == id).delete()  
```

并删除给定的记录`id`（如果存在）。

注意：如果激活了[版本控制](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#versioning)，则此删除快捷方式语法当前不起作用

您可以插入记录：

```
db.mytable[None] = dict(myfield='somevalue')
```

它相当于

```
db.mytable.insert(myfield='somevalue')
```

它使用右侧字典指定的字段值创建一个新记录。

注意：插入快捷方式以前是`db.table[0] = ...`. 它在 PyDAL 19.02 中进行了更改，以允许正常使用 id 0。

您可以更新记录：

```
db.mytable[id] = dict(myfield='somevalue')
```

这相当于

```
db(db.mytable.id == id).update(myfield='somevalue')  
```

它使用右侧字典指定的字段值更新现有记录。



#### 获取一个`Row`

另一个方便的语法如下：

```
record = db.mytable(id)
record = db.mytable(db.mytable.id == id)
record = db.mytable(id, myfield='somevalue')
   
```

显然类似于`db.mytable[id]`上面的语法更加灵活和安全。首先，它检查是否`id`是 int（或者`str(id)`是 int），`None`如果不是则返回（它从不引发异常）。它还允许指定记录必须满足的多个条件。如果不满足，它也会返回`None`。



#### 递归`select`_

考虑前一个表 person 和一个引用“person”的新表“thing”：

```
db.define_table('thing',
                Field('name'),
                Field('owner_id', 'reference person'))
```

并从此表中进行简单选择：

```
things = db(db.thing).select()
```

这相当于

```
things = db(db.thing._id != None).select()
```

where`_id`是对表主键的引用。通常`db.thing._id`是相同的`db.thing.id`，我们将在本书的大部分内容中假设。

对于每一行事物，不仅可以从所选表（事物）中获取字段，还可以从链接表（递归）中获取字段：

```
for thing in things:
    print thing.name, thing.owner_id.name
```

这里`thing.owner_id.name`需要为事物中的每个事物选择一个数据库，因此效率低下。我们建议尽可能使用连接而不是递归选择，但是这在访问单个记录时既方便又实用。

您也可以通过选择一个人引用的事物来倒退：

```
person = db.person(id)
for thing in person.thing.select(orderby=db.thing.name):
    print person.name, 'owns', thing.name
       
```

在最后一个表达式`person.thing`中是一个快捷方式

```
db(db.thing.owner_id == person.id)
```

即`thing`当前 s 引用的 Set `person`。如果引用表具有对被引用表的多个引用，则此语法会失效。在这种情况下，需要更加明确并使用完整的查询。





#### `Rows`在视图中序列化

给定以下包含查询的操作

```
def index():
    return dict(rows = db(query).select())
```

选择的结果可以使用以下语法显示在视图中：

```
{{extend 'layout.html'}}
<h1>Records</h1>
{{=rows}}
```

这相当于：

```
{{extend 'layout.html'}}
<h1>Records</h1>
{{=SQLTABLE(rows)}}
```

`SQLTABLE`将行转换为 HTML 表，表头包含列名，每条记录一行。这些行被标记为交替的类“偶数”和类“奇数”。在底层，Rows 首先被转换为 SQLTABLE 对象（不要与 Table 混淆），然后被序列化。从数据库中提取的值也由与该字段关联的验证器格式化，然后转义。

然而，显式调用 SQLTABLE 是可能的，有时也很方便。

SQLTABLE 构造函数采用以下可选参数：

- `linkto`用于链接引用字段的 lambda 函数或操作（默认为无）。

如果你为它分配一个带有动作名称的字符串，它将生成一个指向该函数的链接，将它作为参数传递给它，表的名称和每条记录的 id（按此顺序）。例子：

```
linkto = 'pointed_function' # generates something like <a href="pointed_function/table_name/id_value">
```

如果您想生成不同的链接，您可以指定一个 lambda，它将接收作为参数、id 的值、对象的类型（例如表）和对象的名称。例如，如果您想以相反的顺序接收参数：

```
linkto = lambda id, type, name: URL(f='pointed_function', args=[id, name])
```

- `upload`允许下载上传文件的 URL 或下载操作（默认为无）
- `headers`将字段名称映射到用作标题的标签的字典（默认为`{}`）。它也可以是一个指令。目前我们支持`headers='fieldname:capitalize'`.
- `truncate`截断表中长值的字符数（默认为 16）
- `columns`要显示为列的字段名列表（以 tablename.fieldname 格式）。未列出的不显示（默认为全部）。
- `**attributes`要传递给最外部的 TABLE 对象的通用助手属性。

这是一个例子：

```
{{extend 'layout.html'}}
<h1>Records</h1>
{{=SQLTABLE(rows,
            headers='fieldname:capitalize',
            truncate=100,
            upload=URL('download'))
}}
```





> `SQLTABLE`很有用，但有时需要更多。`SQLFORM.grid`是 SQLTABLE 的扩展，它创建具有搜索功能和分页的表，以及打开详细记录、创建、编辑和删除记录的能力。`SQLFORM.smartgrid`是进一步的概括，它允许上述所有内容，但也创建按钮来访问引用记录。

下面是一个使用的例子`SQLFORM.grid`：

```
def index():
    return dict(grid=SQLFORM.grid(query))
```

和相应的视图：

```
{{extend 'layout.html'}}
{{=grid}}
```

用于处理多行，`SQLFORM.grid`并且由于它们更强大而`SQLFORM.smartgrid`被首选。`SQLTABLE`请参阅[第 7 章](http://web2py.com/books/default/chapter/29/07#SQLFORM-grid-and-SQLFORM-smartgrid)。





#### `orderby`, `groupby`, `limitby`, `distinct`, `having`, `orderby_on_limitby`, `join`, `left`, `cache`

该`select`命令采用许多可选参数。

##### orderby

您可以获取按名称排序的记录：





```
>>> for row in db().select(db.person.ALL, orderby=db.person.name):
...     print row.name
... 
Alex
Bob
Carl 
     
```

您可以以相反的顺序获取按名称排序的记录（注意波浪号）：

```
>>> for row in db().select(db.person.ALL, orderby=~db.person.name):
...     print row.name
...
Carl
Bob
Alex 
     
```

您可以让获取的记录以随机顺序出现：

```
>>> for row in db().select(db.person.ALL, orderby='<random>'):
...     print row.name
... 
Carl
Alex
Bob
```

> `orderby='<random>'`Google NoSQL 不支持使用。然而，为了克服这个限制，可以对选定的行进行排序：
>
> ```
> import random
> rows = db(...).select().sort(lambda row: random.random())
> ```

您可以通过使用“|”连接多个字段来对记录进行排序：

```
>>> for row in db().select(db.person.name, orderby=db.person.name|db.person.id):
...     print row.name
... 
Alex
Bob
Carl
```

##### groupby, having

`groupby`与 一起使用，`orderby`您可以对指定字段的具有相同值的记录进行分组（这是特定于后端的，不在 Google NoSQL 上）：

```
>>> for row in db().select(db.person.ALL,
...                        orderby=db.person.name,
...                        groupby=db.person.name):
...     print row.name
... 
Alex
Bob
Carl                 
                       
     
```

您可以`having`与 结合使用有条件地分组`groupby`（仅对`having`条件进行分组）。

```
>>> print db(query1).select(db.person.ALL, groupby=db.person.name, having=query2)
```

请注意，query1 过滤要显示的记录，query2 过滤要分组的记录。

##### distinct清楚的

使用参数`distinct=True`，您可以指定您只想选择不同的记录。除了不需要排序之外，这与使用所有指定字段进行分组具有相同的效果。使用 distinct 时，重要的是不要选择所有字段，尤其是不要选择“id”字段，否则所有记录将始终是不同的。

这是一个例子：

```
>>> for row in db().select(db.person.name, distinct=True):
...     print row.name
... 
Alex
Bob
Carl
     
```

请注意，`distinct`也可以是表达式，例如：

```
>>> for row in db().select(db.person.name, distinct=db.person.name):
...     print row.name
... 
Alex
Bob
Carl
```

##### limitby限制比

使用`limitby=(min, max)`，您可以选择从 offset=min 到但不包括 offset=max 的记录子集。在下一个示例中，我们选择从零开始的前两条记录：

```
>>> for row in db().select(db.person.ALL, limitby=(0, 2)):
...     print row.name
... 
Alex
Bob
     
```

##### orderby_on_limitby

请注意，DAL 默认在使用 limitby 时隐式添加 orderby。这确保了相同的查询每次返回相同的结果，这对分页很重要。但它可能会导致性能问题。用于`orderby_on_limitby = False`更改此设置（默认为 True）。

##### join, left

这些都涉及管理[一对多关系](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#One-to-many-relation)。它们分别在[内部联接](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#Inner-join)和[左外部联接](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#Left-outer-join)部分中进行了描述。

##### cache, cacheable

提供更快选择的示例用法是：

```
行 = 分贝（查询）。选择（缓存= （缓存。内存， 3600 ）， 可缓存=真）
```

查看本章中的[缓存选择](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#Caching-selects)部分，以了解权衡是什么。



#### 逻辑运算符





可以使用二元 AND 运算符“ `&`”组合查询：

```
>>> rows = db((db.person.name=='Alex') & (db.person.id > 3)).select()
>>> for row in rows: print row.id, row.name
>>> len(rows)
0
```

和二元或运算符“ `|`”：

```
>>> rows = db((db.person.name == 'Alex') | (db.person.id > 3)).select()
>>> for row in rows: print row.id, row.name
1 Alex
```

您可以否定一个反转其运算符的子查询：

```
>>> rows = db((db.person.name != 'Alex') | (db.person.id > 3)).select()
>>> for row in rows: print row.id, row.name
2 Bob
3 Carl
```

或通过“ `~`”一元运算符的显式否定：

```
>>> rows = db(~(db.person.name == 'Alex') | (db.person.id > 3)).select()
>>> for row in rows: print row.id, row.name
2 Bob
3 Carl
```

> 由于 Python 在重载 " `and`" 和 " `or`" 运算符方面的限制，它们不能用于形成查询。必须使用二元运算符“ `&`”和“ ”。`|`请注意，这些运算符（与“ `and`”和“ `or`”不同）比比较运算符具有更高的优先级，因此上述示例中的“额外”括号是强制性的。同样，一元运算符 " `~`" 的优先级高于比较运算符，因此`~`- 否定比较也必须加上括号。

也可以使用就地逻辑运算符构建查询：

```
>>> query = db.person.name != 'Alex'
>>> query &= db.person.id > 3
>>> query |= db.person.name == 'John'
```



#### `count`, `isempty`, `delete`, `update`

您可以计算集合中的记录：





```
>>> db(db.person.name != 'William').count()
3
```

请注意，它`count`接受一个默认为 False 的可选`distinct`参数，它的工作方式非常类似于`select`. `count`还有一个`cache`参数与方法的等效参数非常相似`select`。

有时您可能需要检查表是否为空。比计数更有效的方法是使用以下`isempty`方法：

```
>>> db(db.person).isempty()
False
```

您可以删除集合中的记录：

```
>>> db(db.person.id > 3).delete()
0
```

该`delete`方法返回已删除的记录数。

您可以通过传递与需要更新的字段相对应的命名参数来更新集合中的所有记录：

```
>>> db(db.person.id > 2).update(name='Ken')
1
```

该`update`方法返回已更新的记录数。



#### 表达式

分配给更新语句的值可以是表达式。例如考虑这个模型

```
db.define_table('person',
                Field('name'),
                Field('visits', 'integer', default=0))

db(db.person.name == 'Massimo').update(visits = db.person.visits + 1)
```

查询中使用的值也可以是表达式

```
db.define_table('person',
                Field('name'),
                Field('visits', 'integer', default=0),
                Field('clicks', 'integer', default=0))

db(db.person.visits == db.person.clicks + 1).delete()
```



#### `case`

表达式可以包含 case 子句，例如：

```
>>> condition = db.person.name.startswith('B')
>>> yes_or_no = condition.case('Yes', 'No')
>>> for row in db().select(db.person.name, yes_or_no):
...     print row.person.name, row[yes_or_no]  # could be row(yes_or_no) too
... 
Alex No
Bob Yes
Ken No
```



#### `update_record`

web2py 还允许使用更新已在内存中的单个记录`update_record`

```
>>> row = db(db.person.id == 2).select().first()
>>> row.update_record(name='Curt')
<Row {'id': 2L, 'name': 'Curt'}>
```

`update_record`不应与

```
>>> row.update(name='Curt')
```

因为对于单行，该方法`update`更新行对象但不更新数据库记录，如`update_record`.

也可以更改一行的属性（一次一个），然后`update_record()`不带参数调用以保存更改：

```
>>> row = db(db.person.id > 2).select().first()
>>> row.name = 'Philip'
>>> row.update_record() # saves above change
<Row {'id': 3L, 'name': 'Philip'}>
 
```

> 请注意，当对象包含具有属性的字段（例如，）时，您应该避免使用`row.update_record()`不带参数的字段。调用将保留对象中的*所有*现有值，因此任何具有属性的字段在这种情况下都不起作用。对于包含.`row``update``Field('modified_on', update=request.now)``row.update_record()``row``update``auth.signature`

`update_record`仅当表的`id`字段包含在选择`cacheable`中且未设置为 时，该方法才可用`True`。



#### 从字典中插入和更新

一个常见问题包括需要在表中插入或更新记录，其中表的名称、要更新的字段和字段的值都存储在变量中。例如：`tablename`、`fieldname`和`value`。

可以使用以下语法完成插入：

```
db[tablename].insert(**{fieldname:value})
```

可以通过以下方式更新具有给定 ID 的记录：



```
db(db[tablename]._id == id).update(**{fieldname:value})
```

请注意，我们使用`table._id`而不是`table.id`. 通过这种方式，即使对于具有非“id”类型的主键字段的表，查询也有效。



#### `first`和`last`





给定一个包含记录的 Rows 对象：

```
rows = db(query).select()
first_row = rows.first()
last_row = rows.last()
```

相当于

```
first_row = rows[0] if len(rows) else None
last_row = rows[-1] if len(rows) else None
```

注意，`first()`并`last()`允许您获得查询中出现的第一条和最后一条记录，但这并不意味着这些记录将是第一条或最后一条插入的记录。如果您想在给定表中输入第一条或最后一条记录，请不要忘记使用`orderby=db.table_name.id`. 如果你忘记了，你只会得到查询返回的第一条和最后一条记录，这些记录通常是由后端查询优化器确定的随机顺序。



#### `as_dict`和`as_list`





可以使用该方法将 Row 对象序列化为常规字典，`as_dict()`并且可以使用该方法将 Rows 对象序列化为字典列表`as_list()`。这里有些例子：

```
rows = db(query).select()
rows_list = rows.as_list()
first_row_dict = rows.first().as_dict()
```

这些方法便于将 Rows 传递给通用视图或将 Rows 存储在会话中（因为 Rows 对象本身不能被序列化，因为包含对打开的 DB 连接的引用）：

```
rows = db(query).select()
session.rows = rows  # not allowed!
session.rows = rows.as_list()  # allowed!
```



#### 组合行

行对象可以在 Python 级别组合。这里我们假设：

```
>>> print rows1
person.name
Max
Tim

>>> print rows2
person.name
John
Tim
```

您可以合并两组行中的记录：

```
>>> rows3 = rows1 + rows2
>>> print rows3
person.name
Max
Tim
John
Tim
```

您可以合并删除重复的记录：

```
>>> rows3 = rows1 | rows2
>>> print rows3
person.name
Max
Tim
John
```

您可以对两组行中的记录进行交集：

```
>>> rows3 = rows1 & rows2
>>> print rows3
person.name
Tim
```



#### `find`, `exclude`, `sort`





有时您需要执行两次选择，其中一次包含先前选择的子集。在这种情况下，再次访问数据库是没有意义的。`find`和对象允许您在不访问数据库的情况下操作 Rows 对象`exclude`并`sort`生成另一个对象。进一步来说：

- `find`返回由条件过滤的一组新行，并保持原始行不变。
- `exclude`返回由条件过滤的一组新行，并将它们从原始行中删除。
- `sort`返回一组按条件排序的新行，并使原始行保持不变。

所有这些方法都采用一个参数，一个作用于每一行的函数。

这是一个使用示例：

```
>>> db.define_table('person', Field('name'))
<Table person (id, name)>
>>> db.person.insert(name='John')
1
>>> db.person.insert(name='Max')
2
>>> db.person.insert(name='Alex')
3
>>> rows = db(db.person).select()
>>> for row in rows.find(lambda row: row.name[0]=='M'):
...     print row.name
... 
Max
>>> len(rows)
3
>>> for row in rows.exclude(lambda row: row.name[0]=='M'):
...     print row.name
... 
Max
>>> len(rows)
2
>>> for row in rows.sort(lambda row: row.name):
...     print row.name
... 
Alex
John
```

它们可以组合：

```
>>> rows = db(db.person).select()
>>> rows = rows.find(lambda row: 'x' in row.name).sort(lambda row: row.name)
>>> for row in rows:
...     print row.name
... 
Alex
Max
   
     
```

Sort 接受一个`reverse=True`具有明显含义的可选参数。

该`find`方法有一个可选`limitby`参数，其语法和功能与 Set`select`方法相同。



### 其他方法



#### `update_or_insert`

有时，只有在没有与被插入的记录具有相同值的记录时，您才需要执行插入。这可以通过

```
db.define_table('person',
                Field('name'),
                Field('birthplace'))

db.person.update_or_insert(name='John', birthplace='Chicago')
```

仅当没有其他名为 John 的用户出生在芝加哥时，才会插入该记录。

您可以指定使用哪些值作为键来确定记录是否存在。例如：

```
db.person.update_or_insert(db.person.name == 'John',
                           name='John',
                           birthplace='Chicago')
```

如果有约翰，他的出生地将被更新，否则将创建一个新记录。

上例中的选择标准是单个字段。也可以是查询，比如

```
db.person.update_or_insert((db.person.name == 'John') & (db.person.birthplace == 'Chicago'),
                           name='John',
                           birthplace='Chicago',
                           pet='Rover')
                           
```



#### `validate_and_insert`,`validate_and_update`





功能

```
ret = db.mytable.validate_and_insert(field='value')
```

非常喜欢

```
id = db.mytable.insert(field='value')
```

除了它在执行插入之前调用字段的验证器并在验证未通过时退出。如果验证未通过，则可以在 中找到错误`ret.errors`。`ret.errors`保存一个键值映射，其中每个键是验证失败的字段名称，键的值是验证错误的结果（很像`form.errors`）。如果通过，则新记录的 id 在`ret.id`. 请注意，通常验证是由表单处理逻辑完成的，因此很少需要此功能。

相似地

```
ret = db(query).validate_and_update(field='value')
```

工作原理与

```
num = db(query).update(field='value')
```

除了它在执行更新之前调用字段的验证器。请注意，它仅在查询涉及单个表时才有效。更新记录的数量可以在 中找到`ret.updated`，错误会在 中找到`ret.errors`。



#### `smart_query`（实验性）

有时您需要使用自然语言解析查询，例如

```
name contains m and age greater than 18
```

DAL 提供了一种解析此类查询的方法：

```
search = 'name contains m and age greater than 18'
rows = db.smart_query([db.person], search).select()
```

第一个参数必须是应在搜索中允许的表或字段的列表。`RuntimeError`如果搜索字符串无效，它会引发 a 。此功能可用于构建 RESTful 接口（请参阅第 10 章），并由`SQLFORM.grid`and内部使用`SQLFORM.smartgrid`。

在 smart_query 搜索字符串中，字段只能由 fieldname 和/或 tablename.fieldname 标识。如果字符串包含空格，则字符串可以用双引号分隔。



### 计算字段

DAL 字段可能有一个`compute`属性。这必须是一个函数（或 lambda），它接受一个 Row 对象并返回该字段的值。当一条新记录被修改时，包括插入和更新，如果没有提供该字段的值，web2py 会尝试使用该`compute`函数从其他字段值进行计算。这是一个例子：

```
>>> db.define_table('item',
...                 Field('unit_price', 'double'),
...                 Field('quantity', 'integer'),
...                 Field('total_price',
...                       compute=lambda r: r['unit_price'] * r['quantity']))
<Table item (id, unit_price, quantity, total_price)>
>>> rid = db.item.insert(unit_price=1.99, quantity=5)
>>> db.item[rid]
<Row {'total_price': '9.95', 'unit_price': 1.99, 'id': 1L, 'quantity': 5L}>
```

请注意，计算值存储在 db 中，而不是在检索时计算，就像下面描述的虚拟字段的情况一样。计算域的两个典型应用是：

- 在 wiki 应用程序中，将处理后的输入 wiki 文本存储为 HTML，以避免对每个请求进行重新处理
- 用于搜索，计算字段的标准化值，用于搜索。

计算字段按照它们在表定义中定义的顺序进行评估。计算域可以引用先前定义的计算域（v 2.5.1 后新增）



### 虚拟字段

虚拟字段也是计算字段（如上一小节），但它们与那些不同，因为它们是*虚拟*的，因为它们不存储在数据库中，并且每次从数据库中提取记录时都会计算它们。它们可用于简化用户的代码，而无需使用额外的存储空间，但它们不能用于搜索。



#### 新型虚拟场（实验性）

web2py 提供了一种新的更简单的方法来定义虚拟字段和惰性虚拟字段。此部分标记为实验性的，因为 API 可能仍会与此处描述的内容有所不同。

在这里，我们将考虑与上一小节相同的示例。我们特别考虑以下模型：

```
db.define_table('item',
                Field('unit_price', 'double'),
                Field('quantity', 'integer'))
                 
                 
```

可以将`total_price`虚拟字段定义为

```
db.item.total_price = Field.Virtual(lambda row: row.item.unit_price * row.item.quantity)
```

即通过简单地将新字段定义`total_price`为`Field.Virtual`. 构造函数的唯一参数是一个接受一行并返回计算值的函数。

定义的虚拟字段根据选择记录时自动计算上面的虚拟字段：

```
for row in db(db.item).select():
    print row.total_price
     
```

还可以定义在调用时按需计算的方法字段。例如：

```
db.item.discounted_total = \
    Field.Method(lambda row, discount=0.0:
                 row.item.unit_price * row.item.quantity * (100.0 - discount / 100))
```

在这种情况下`row.discounted_total`，不是一个值，而是一个函数。`Method`该函数采用与传递给构造函数的函数相同的参数，但`row`它是隐式的（将其视为`self`对象）。

上例中的惰性字段允许计算每个的总价格`item`：

```
for row in db(db.item).select(): print row.discounted_total()   
```

它还允许传递一个可选的`discount`百分比（比如 15%）：

```
for row in db(db.item).select(): print row.discounted_total(15) 
```

定义表时，也可以就地定义虚拟和方法字段：

```
db.define_table('item',
                Field('unit_price', 'double'),
                Field('quantity', 'integer'),
                Field.Virtual('total_price', lambda row: ...),
                Field.Method('discounted_total', lambda row, discount=0.0: ...))
```

> 请注意，虚拟字段不具有与常规字段相同的属性（长度、默认值、必需等）。它们不会出现在 web2py 的列表中，`db.table.fields`并且在旧版本的 web2py 中它们需要一种特殊的方法来显示在 SQLFORM.grid 和 SQLFORM.smartgrid 中。[请参阅第 7 章](http://web2py.com/books/default/chapter/29/07#Virtual-fields-in-SQLFORM-grid-and-smartgrid)中关于网格和虚拟场的讨论。



#### 旧式虚拟字段

为了定义一个或多个虚拟字段，您还可以定义一个容器类，将其实例化并将其链接到表或选择。例如，考虑下表：

```
db.define_table('item',
                Field('unit_price', 'double'),
                Field('quantity', 'integer'))
                 
```

可以将`total_price`虚拟字段定义为

```
class MyVirtualFields(object):
    def total_price(self):
        return self.item.unit_price * self.item.quantity

db.item.virtualfields.append(MyVirtualFields())
```

请注意，接受单个参数（self）的类的每个方法都是一个新的虚拟字段。`self`指的是每一行的select。字段值由完整路径引用，如`self.item.unit_price`. 通过将类的实例附加到表的`virtualfields`属性，表链接到虚拟字段。

虚拟字段也可以访问递归字段，如

```
db.define_table('item',
                Field('unit_price', 'double'))

db.define_table('order_item',
                Field('item', 'reference item'),
                Field('quantity', 'integer'))

class MyVirtualFields(object):
    def total_price(self):
        return self.order_item.item.unit_price * self.order_item.quantity

db.order_item.virtualfields.append(MyVirtualFields())
```

注意循环记录`self.order_item.item.unit_price`在哪里的递归字段访问。`self`

他们还可以对 JOIN 的结果采取行动

```
rows = db(db.order_item.item == db.item.id).select()

class MyVirtualFields(object):
    def total_price(self):
        return self.item.unit_price * self.order_item.quantity

rows.setvirtualfields(order_item=MyVirtualFields())

for row in rows:
    print row.order_item.total_price
```

注意在这种情况下语法是如何不同的。虚拟字段同时访问`self.item.unit_price`属于`self.order_item.quantity`连接选择的两者。`setvirtualfields`使用rows 对象的方法将虚拟字段附加到表的行。此方法采用任意数量的命名参数，可用于设置多个虚拟字段，定义在多个类中，并将它们附加到多个表：

```
class MyVirtualFields1(object):
    def discounted_unit_price(self):
        return self.item.unit_price * 0.90

class MyVirtualFields2(object):
    def total_price(self):
        return self.item.unit_price * self.order_item.quantity
    def discounted_total_price(self):
        return self.item.discounted_unit_price * self.order_item.quantity

rows.setvirtualfields(item=MyVirtualFields1(),
                      order_item=MyVirtualFields2())

for row in rows:
    print row.order_item.discounted_total_price
```

虚拟字段可以是*惰性*的；他们需要做的就是返回一个函数并通过调用该函数来访问它：

```
db.define_table('item',
                Field('unit_price', 'double'),
                Field('quantity', 'integer'))

class MyVirtualFields(object):
    def lazy_total_price(self):
        def lazy(self=self):
            return self.item.unit_price * self.item.quantity
        return lazy

db.item.virtualfields.append(MyVirtualFields())

for item in db(db.item).select():
    print item.lazy_total_price()
     
```

使用 lambda 函数或更短：

```
class MyVirtualFields(object):
    def lazy_total_price(self):
        return lambda self=self: self.item.unit_price * self.item.quantity
             
```



### 一对多关系

为了说明如何使用 DAL 实现一对多关系，定义另一个表“事物”，它引用我们在这里重新定义的表“人”：

```
>>> db.define_table('person',
...                 Field('name'))
<Table person (id, name)>
>>> db.person.insert(name='Alex')
1
>>> db.person.insert(name='Bob')
2
>>> db.person.insert(name='Carl')
3
>>> db.define_table('thing',
...                 Field('name'),
...                 Field('owner_id', 'reference person'))
<Table thing (id, name, owner_id)>
```

表“事物”有两个字段，事物的名称和事物的所有者。“owner_id”字段是一个引用字段，该字段旨在通过其 id 引用另一个表。可以通过两种等效的方式指定引用类型，或者：

```
Field('owner_id', 'reference person')
```

或者：

```
Field('owner_id', db.person)
```

后者总是转换为前者。它们是等价的，除非在惰性表、自引用或其他类型的循环引用的情况下，前一种表示法是唯一允许的表示法。

现在，插入三件东西，两件属于 Alex，一件属于 Bob：

```
>>> db.thing.insert(name='Boat', owner_id=1)
1
>>> db.thing.insert(name='Chair', owner_id=1)
2
>>> db.thing.insert(name='Shoes', owner_id=2)
3
```

您可以像对任何其他表格一样选择：

```
>>> for row in db(db.thing.owner_id == 1).select():
...     print row.name
... 
Boat
Chair
     
```

因为一个事物有一个人的引用，一个人可以有很多事物，所以表person的记录现在获得了一个新的属性事物，它是一个Set，它定义了那个人的事物。这允许遍历所有人并轻松获取他们的东西：

```
>>> for person in db().select(db.person.ALL):
...     print person.name
...     for thing in person.thing.select():
...         print '    ', thing.name
...
Alex
     Boat
     Chair
Bob
     Shoes
Carl
       
          
```



#### 内连接

实现类似结果的另一种方法是使用连接，特别是 INNER JOIN。当查询链接两个或多个表时，web2py 会自动且透明地执行连接，如下例所示：





```
>>> rows = db(db.person.id == db.thing.owner_id).select()
>>> for row in rows:
...     print row.person.name, 'has', row.thing.name
... 
Alex has Boat
Alex has Chair
Bob has Shoes
```

观察 web2py 进行了连接，因此行现在包含两条记录，每个表中的一条记录链接在一起。因为这两条记录可能存在名称冲突的字段，所以在从行中提取字段值时需要指定表。这意味着在您可以这样做之前：

```
row.name
```

很明显这是人名还是事物的名称，在连接的结果中，您必须更明确地说：

```
row.person.name
```

或者：

```
row.thing.name
```

INNER JOINS 有另一种语法：

```
>>> rows = db(db.person).select(join=db.thing.on(db.person.id == db.thing.owner_id))
>>> for row in rows:
...     print row.person.name, 'has', row.thing.name
... 
Alex has Boat
Alex has Chair
Bob has Shoes
```

虽然输出相同，但两种情况下生成的 SQL 可能不同。当同一个表被连接两次并别名时，后一种语法消除了可能的歧义：

```
db.define_table('thing',
                Field('name'),
                Field('owner_id1', 'reference person'),
                Field('owner_id2', 'reference person'))

rows = db(db.person).select(
        join=[db.person.with_alias('owner_id1').on(db.person.id == db.thing.owner_id1),
              db.person.with_alias('owner_id2').on(db.person.id == db.thing.owner_id2)])
```

的值`join`可以是`db.table.on(...)`要加入的列表。



#### 左外连接





请注意，卡尔没有出现在上面的列表中，因为他没有任何东西。如果您打算选择人（无论他们是否有东西）和他们的东西（如果他们有的话），那么您需要执行 LEFT OUTER JOIN。这是使用选择的参数“left”完成的。这是一个例子：

```
>>> rows = db().select(db.person.ALL, db.thing.ALL,
...                    left=db.thing.on(db.person.id == db.thing.owner_id))
>>> for row in rows:
...     print row.person.name, 'has', row.thing.name
... 
Alex has Boat
Alex has Chair
Bob has Shoes
Carl has None
```

在哪里：

```
left = db.thing.on(...)
```

执行左连接查询。这里的参数`db.thing.on`是连接所需的条件（与上面用于内部连接的相同）。在左连接的情况下，有必要明确选择哪些字段。

可以通过将列表或元组传递`db.mytable.on(...)`给 `left`参数来组合多个左连接。



#### 分组和计数

在进行联接时，有时您希望根据某些条件对行进行分组并对其进行计数。例如，计算每个人拥有的东西的数量。web2py 也允许这样做。首先，您需要一个计数运算符。其次，您想通过所有者将人员表与事物表连接起来。第三，要选择所有行（人+事物），按人分组，分组时计数：

```
>>> count = db.person.id.count()
>>> for row in db(db.person.id == db.thing.owner_id
...               ).select(db.person.name, count, groupby=db.person.name):
...     print row.person.name, row[count]
... 
Alex 2
Bob 1
```

请注意，`count`运算符（内置）用作字段。这里唯一的问题是如何检索信息。每一行都清楚地包含一个人和计数，但计数不是一个人的字段，也不是一个表。那么它会去哪里呢？它进入表示记录的存储对象，其键等于查询表达式本身。

Field 对象的`count`方法有一个可选`distinct`参数。当设置为`True`它指定只计算相关字段的不同值。



### 多对多

在前面的示例中，我们允许一个事物拥有一个所有者，但一个人可以拥有许多事物。如果 Boat 由 Alex 和 Curt 拥有怎么办？这需要多对多关系，并且它是通过一个中间表来实现的，该表通过所有权关系将人与事物联系起来。

这是如何做到的：

```
>>> db.define_table('person',
...                 Field('name'))
<Table person (id, name)>
>>> db.person.bulk_insert([dict(name='Alex'), dict(name='Bob'), dict(name='Carl')])
[1, 2, 3]
>>> db.define_table('thing',
...                 Field('name'))
<Table thing (id, name)>
>>> db.thing.bulk_insert([dict(name='Boat'), dict(name='Chair'), dict(name='Shoes')])
[1, 2, 3]
>>> db.define_table('ownership',
...                 Field('person', 'reference person'),
...                 Field('thing', 'reference thing'))
<Table ownership (id, person, thing)>
```

现有的所有权关系现在可以改写为：

```
>>> db.ownership.insert(person=1, thing=1)  # Alex owns Boat
1
>>> db.ownership.insert(person=1, thing=2)  # Alex owns Chair
2
>>> db.ownership.insert(person=2, thing=3)  # Bob owns Shoes
3
```

现在您可以添加 Curt 共同拥有的新关系：

```
>>> db.ownership.insert(person=3, thing=1)  # Curt owns Boat too
4
```

因为您现在在表之间具有三向关系，所以定义一个新的集合来执行操作可能会很方便：

```
>>> persons_and_things = db((db.person.id == db.ownership.person) &
...                         (db.thing.id == db.ownership.thing))    
```

现在很容易从新集合中选择所有人和他们的东西：

```
>>> for row in persons_and_things.select():
...     print row.person.name, 'has', row.thing.name
... 
Alex has Boat
Alex has Chair
Bob has Shoes
Curt has Boat
       
```

同样，您可以搜索 Alex 拥有的所有东西：

```
>>> for row in persons_and_things(db.person.name == 'Alex').select():
...     print row.thing.name
... 
Boat
Chair
```

和所有船主：

```
>>> for row in persons_and_things(db.thing.name == 'Boat').select():
...     print row.person.name
... 
Alex
Curt
```

多对多关系的一个更轻松的替代方法是标记，您可以在下一节中找到一个示例。[在第 7 章的IS_IN_DB](http://web2py.com/books/default/chapter/29/07#IS_IN_DB-and-Tagging)和[IS_IN_SET](http://web2py.com/books/default/chapter/29/07#IS_IN_SET-and-Tagging)验证器的上下文中也讨论了标记。标记甚至可以在不支持 JOIN 的数据库后端（如 Google App Engine NoSQL）上工作。





### `list:<type>`和`contains`





web2py 提供以下特殊字段类型：

```
list:string
list:integer
list:reference <table>
```

它们可以分别包含字符串列表、整数列表和引用列表。

在 Google App Engine 上 NoSQL`list:string`被映射成`StringListProperty`，另外两个被映射成`ListProperty(int)`。在关系数据库中，它们被映射到包含以 . 分隔的项目列表的文本字段中`|`。例如`[1, 2, 3]`映射到`|1|2|3|`.

对于字符串列表，项目被转义，以便项目中的任何`|`内容都被替换为`||`. 无论如何，这是一个内部表示，它对用户是透明的。

`list:string`例如，您可以通过以下方式使用：

```
>>> db.define_table('product',
...                 Field('name'),
...                 Field('colors', 'list:string'))
<Table product (id, name, colors)>
>>> db.product.colors.requires = IS_IN_SET(('red', 'blue', 'green'))
>>> db.product.insert(name='Toy Car', colors=['red', 'green'])
1
>>> products = db(db.product.colors.contains('red')).select()
>>> for item in products:
...     print item.name, item.colors
... 
Toy Car ['red', 'green']
```

`list:integer`以相同的方式工作，但项目必须是整数。

像往常一样，这些要求是在表单级别执行的，而不是在`insert`.

> 对于`list:<type>`字段，`contains(value)`运算符映射到一个不平凡的查询，该查询检查包含`value`. 该`contains`运算符也适用于常规`string`和`text`字段，它映射到`LIKE '%value%'`.

`list:reference`和运算符对于`contains(value)`反规范化多对多关系特别有用。这是一个例子：

```
>>> db.define_table('tag',
...                 Field('name'),
...                 format='%(name)s')
<Table tag (id, name)>
>>> db.define_table('product',
...                 Field('name'),
...                 Field('tags', 'list:reference tag'))
<Table product (id, name, tags)>
>>> a = db.tag.insert(name='red')
>>> b = db.tag.insert(name='green')
>>> c = db.tag.insert(name='blue')
>>> db.product.insert(name='Toy Car', tags=[a, b, c])
1
>>> products = db(db.product.tags.contains(b)).select()
>>> for item in products:
...     print item.name, item.tags
... 
Toy Car [1, 2, 3]
>>> for item in products:
...     print item.name, db.product.tags.represent(item.tags)
... 
Toy Car red, green, blue
```

请注意，`list:reference tag`字段获得默认约束

```
requires = IS_IN_DB(db, db.tag._id, db.tag._format, multiple=True)
```

在表单中生成`SELECT/OPTION`多个下拉框。

另请注意，此字段有一个默认`represent`属性，该属性将引用列表表示为以逗号分隔的格式化引用列表。这用于读取表单和`SQLTABLE`s。

> 虽然`list:reference`有一个默认验证器和一个默认表示，`list:integer`但`list:string`没有。因此，如果您想在表单中使用它们，这两个需要一个`IS_IN_SET`或一个验证器。`IS_IN_DB`



### 其他operators

web2py 有其他操作符提供 API 来访问等效的 SQL 操作符。让我们定义另一个表“日志”来存储安全事件、它们的 event_time 和严重性，其中严重性是一个整数。





```
>>> db.define_table('log', Field('event'),
...                        Field('event_time', 'datetime'),
...                        Field('severity', 'integer'))
<Table log (id, event, event_time, severity)>
```

和以前一样，插入一些事件，“端口扫描”、“xss 注入”和“未经授权的登录”。出于示例的目的，您可以记录具有相同 event_time 但具有不同严重性（分别为 1、2 和 3）的事件。

```
>>> import datetime
>>> now = datetime.datetime.now()
>>> db.log.insert(event='port scan', event_time=now, severity=1)
1
>>> db.log.insert(event='xss injection', event_time=now, severity=2)
2
>>> db.log.insert(event='unauthorized login', event_time=now, severity=3)
3
```



#### `like`, `ilike`, `regexp`, `startswith`, `endswith`, `contains`, `upper`, `lower`





字段具有`like`可用于匹配字符串的运算符：

```
>>> for row in db(db.log.event.like('port%')).select():
...     print row.event
... 
port scan
```

这里“port%”表示以“port”开头的字符串。百分号字符“%”是一个通配符，表示“任何字符序列”。

`like`运算符映射到 ANSI-SQL 中的 LIKE 字。LIKE 在大多数数据库中是区分大小写的，并且取决于数据库本身的排序规则。因此，该`like`方法区分大小写，但可以使用以下方法使其不区分大小写

```
db.mytable.myfield.like('value', case_sensitive=False)
```

这与使用相同`ilike`

```
db.mytable.myfield.ilike('value')
```

web2py 还提供了一些快捷方式：

```
db.mytable.myfield.startswith('value')
db.mytable.myfield.endswith('value')
db.mytable.myfield.contains('value')
```

分别大致相当于

```
db.mytable.myfield.like('value%')
db.mytable.myfield.like('%value')
db.mytable.myfield.like('%value%')
```

请记住，`contains`这对字段有特殊含义`list:<type>`，如前面的[列表中所述： 和包含](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#list-type-and-contains)部分。

该`contains`方法还可以传递一个值列表和一个可选的布尔参数`all`来搜索包含所有值的记录：

```
db.mytable.myfield.contains(['value1', 'value2'], all=True)
```

或列表中的任何值

```
db.mytable.myfield.contains(['value1', 'value2'], all=False)
```

还有一种`regexp`方法与该`like`方法类似，但允许查找表达式的正则表达式语法。仅 MySQL、Oracle、PostgreSQL、SQLite 和 MongoDB 支持（支持程度不同）。

`upper`and方法允许您将`lower`字段的值转换为大写或小写，您还可以将它们与 like 运算符结合使用：

```
>>> for row in db(db.log.event.upper().like('PORT%')).select():
...     print row.event
... 
port scan
     
```



#### `year`, `month`, `day`, `hour`, `minutes`, `seconds`





日期和日期时间字段有`day`,`month`和`year`方法。datetime 和 time 字段有`hour`,`minutes`和`seconds`方法。这是一个例子：

```
>>> for row in db(db.log.event_time.year() > 2018).select():
...     print row.event
... 
port scan
xss injection
unauthorized login   
     
```



#### `belongs`

SQL IN 运算符通过`belongs`当字段值属于指定集合（列表或元组）时返回 true 的方法实现：

```
>>> for row in db(db.log.severity.belongs((1, 2))).select():
...     print row.event
... 
port scan
xss injection
     
```

DAL 还允许嵌套选择作为属于运算符的参数。唯一需要注意的是嵌套选择必须是 a `_select`，而不是 a `select`，并且必须明确选择一个字段，即定义集合的字段。

```
>>> bad_days = db(db.log.severity == 3)._select(db.log.event_time)
>>> for row in db(db.log.event_time.belongs(bad_days)).select():
...     print row.severity, row.event
... 
1 port scan
2 xss injection
3 unauthorized login
```

在需要嵌套选择并且查找字段是引用的情况下，我们也可以使用查询作为参数。例如：

```
db.define_table('person', Field('name'))
db.define_table('thing',
                Field('name'),
                Field('owner_id', 'reference person'))

db(db.thing.owner_id.belongs(db.person.name == 'Jonathan')).select() 
```

在这种情况下，很明显嵌套选择只需要该字段引用的`db.thing.owner_id`字段，因此我们不需要更冗长的`_select`符号。

嵌套选择也可以用作插入/更新值，但在这种情况下，语法不同：

```
lazy = db(db.person.name == 'Jonathan').nested_select(db.person.id)

db(db.thing.id == 1).update(owner_id = lazy)    
```

在这种情况下`lazy`，是一个计算`id`人“乔纳森”的嵌套表达式。这两行生成一个 SQL 查询。



#### `sum`, `avg`,和`min`_`max``len`





以前，您已使用`count`运算符对记录进行计数。同样，您可以使用`sum`运算符从一组记录中添加（求和）特定字段的值。与计数的情况一样，总和的结果是通过存储对象检索的：

```
>>> sum = db.log.severity.sum()
>>> print db().select(sum).first()[sum]
6
```

您还可以使用`avg`、`min`和`max`分别表示所选记录的平均值、最小值和最大值。例如：

```
>>> max = db.log.severity.max()
>>> print db().select(max).first()[max]
3
```

`len`计算字段值的长度。它通常用于字符串或文本字段，但取决于后端，它也可能适用于其他类型（布尔值、整数等）。

```
>>> for row in db(db.log.event.len() > 13).select():
...     print row.event
... 
unauthorized login
```

表达式可以组合成更复杂的表达式。例如，这里我们计算日志中事件字符串长度的总和加一：

```
>>> exp = (db.log.event.len() + 1).sum()
>>> db().select(exp).first()[exp]
43
```



#### 子串

可以构建一个表达式来引用子字符串。例如，我们可以将名称以相同三个字符开头的事物分组，并从每个组中仅选择一个：

```
db(db.thing).select(distinct = db.thing.name[:3])
```



#### `coalesce`带有和的默认值`coalesce_zero`

有时您需要从数据库中提取值，但如果记录的值设置为 NULL，则还需要默认值。在 SQL 中有一个函数`COALESCE`, 用于此。web2py 有一个等效的`coalesce`方法：

```
>>> db.define_table('sysuser', Field('username'), Field('fullname'))
<Table sysuser (id, username, fullname)>
>>> db.sysuser.insert(username='max', fullname='Max Power')
1
>>> db.sysuser.insert(username='tim', fullname=None)
2
>>> coa = db.sysuser.fullname.coalesce(db.sysuser.username)
>>> for row in db().select(coa):
...     print row[coa]
... 
Max Power
tim
```

有时您需要计算数学表达式，但某些字段的值设置为 None 而它应该为零。`coalesce_zero`通过在查询中将 None 默认为零来进行救援：

```
>>> db.define_table('sysuser', Field('username'), Field('points'))
<Table sysuser (id, username, points)>
>>> db.sysuser.insert(username='max', points=10)
1
>>> db.sysuser.insert(username='tim', points=None)
2
>>> exp = db.sysuser.points.coalesce_zero().sum()
>>> db().select(exp).first()[exp]
10
>>> type(exp)
<class 'pydal.objects.Expression'>
>>> print exp
SUM(COALESCE("sysuser"."points",'0'))
```



### 生成原始 sql

有时您需要生成 SQL 但不执行它。使用 web2py 很容易做到这一点，因为执行数据库 IO 的每个命令都有一个不执行的等效命令，并且只返回本应执行的 SQL。这些命令与功能命令具有相同的名称和语法，但它们以下划线开头：

这是`_insert`

```
>>> print db.person._insert(name='Alex')
INSERT INTO "person"("name") VALUES ('Alex');
```

这是`_count`

```
>>> print db(db.person.name == 'Alex')._count()
SELECT COUNT(*) FROM "person" WHERE ("person"."name" = 'Alex');  
```

这是`_select`

```
>>> print db(db.person.name == 'Alex')._select()
SELECT "person"."id", "person"."name" FROM "person" WHERE ("person"."name" = 'Alex');  
```

这是`_delete`

```
>>> print db(db.person.name == 'Alex')._delete()
DELETE FROM "person" WHERE ("person"."name" = 'Alex'); 
```

最后，这里是`_update`

```
>>> print db(db.person.name == 'Alex')._update(name='Susan')
UPDATE "person" SET "name"='Susan' WHERE ("person"."name" = 'Alex');  
```

> 此外，您始终可以使用`db._lastsql`返回最新的 SQL 代码，无论它是使用 executesql 手动执行的，还是由 DAL 生成的 SQL。



### 导出和导入数据







#### CSV（一次一张表）

当 Rows 对象转换为字符串时，它会自动在 CSV 中序列化：

```
>>> rows = db(db.person.id == db.thing.owner_id).select()
>>> print rows
person.id,person.name,thing.id,thing.name,thing.owner_id
1,Alex,1,Boat,1
1,Alex,2,Chair,1
2,Bob,3,Shoes,2
 
```

您可以在 CSV 中序列化单个表并将其存储在文件“test.csv”中：

```
with open('test.csv', 'wb') as dumpfile:
    dumpfile.write(str(db(db.person).select()))
    
```

或者在 Python 3 中：

```
>>> open('test.csv', 'w', encoding='utf-8', newline='').write(str(db(db.person.id).select()))
```

这相当于

```
rows = db(db.person).select()
with open('test.csv', 'wb') as dumpfile:
    rows.export_to_csv_file(dumpfile)
```

您可以使用以下命令读取 CSV 文件：

```
with open('test.csv', 'rb') as dumpfile:
    db.person.import_from_csv_file(dumpfile)
    
```

或者在 Python 3 中：

```
>>> rows = db(db.person.id).select()
>>> rows.export_to_csv_file(open('test.csv', 'w', encoding='utf-8', newline=''))
```

您可以使用以下命令读取 CSV 文件：

```
>>> db.person.import_from_csv_file(open('test.csv', 'r', encoding='utf-8', newline=''))
```

导入时，web2py 会在 CSV 标头中查找字段名称。在此示例中，它找到两列：“person.id”和“person.name”。它忽略了“人”。前缀，它忽略“id”字段。然后所有记录都被附加并分配新的ID。这两个操作都可以通过 appadmin Web 界面执行。



#### CSV（一次所有表）

在 web2py 中，您可以使用两个命令备份/恢复整个数据库：

要导出：

```
with open('somefile.csv', 'wb') as dumpfile:
    db.export_to_csv_file(dumpfile)  
    
```

要导入：

```
with open('somefile.csv', 'rb') as dumpfile:
    db.import_from_csv_file(dumpfile)  
    
```

或者在 Python 3 中：

要导出：

```
>>> db.export_to_csv_file(open('test.csv', 'w', encoding='utf-8', newline=''))
```

要导入：

```
>>> db.import_from_csv_file(open('test.csv', 'r', encoding='utf-8', newline=''))
```

即使导入数据库与导出数据库的类型不同，也可以使用此机制。

数据作为 CSV 文件存储在“somefile.csv”中，其中每个表以一行表示表名开始，另一行表示字段名：

```
TABLE tablename
field1,field2,field3,...
```

两个表由`\r\n\r\n`（即两个空行）分隔。文件以该行结尾

```
END
```

如果这些文件未存储在数据库中，则该文件不包括上传的文件。存储在文件系统上的上传文件必须单独转储，“上传”文件夹的 zip 在大多数情况下就足够了。

导入时，如果数据库不为空，新记录将被追加到数据库中。一般来说，新导入的记录不会与原始（保存的）记录具有相同的记录 id，但 web2py 将恢复引用，因此它们不会被破坏，即使 id 值可能会更改。

如果表包含名为 的字段`uuid`，则该字段将用于标识重复项。此外，如果导入的记录与`uuid`现有记录相同，则将更新以前的记录。



#### CSV 和远程数据库同步

再次考虑以下模型：

```
db.define_table('person',
                Field('name'))

db.define_table('thing',
                Field('name'),
                Field('owner_id', 'reference person'))

# usage example
if db(db.person).isempty():
    nid = db.person.insert(name='Massimo')
    db.thing.insert(name='Chair', owner_id=nid)
     
```

每条记录都由一个标识符标识并由该 id 引用。如果您有两个不同的 web2py 安装使用的数据库副本，则 id 仅在每个数据库中是唯一的，而不是跨数据库。当合并来自不同数据库的记录时，这是一个问题。

为了使记录在数据库中具有唯一性，它们必须：

- 具有唯一 ID (UUID)，
- 有一个最后修改时间来跟踪多个副本中的最新的，
- 引用 UUID 而不是 id。

这可以通过将上述模型更改为：

```
import uuid

db.define_table('person',
                Field('uuid', length=64),
                Field('modified_on', 'datetime', default=request.now, update=request.now),
                Field('name'))

db.define_table('thing',
                Field('uuid', length=64),
                Field('modified_on', 'datetime', default=request.now, update=request.now),
                Field('name'),
                Field('owner_id', length=64))

db.person.uuid.default = db.thing.uuid.default = lambda:str(uuid.uuid4())

db.thing.owner_id.requires = IS_IN_DB(db, 'person.uuid', '%(name)s')

# usage example
if db(db.person).isempty():
    nid = str(uuid.uuid4())
    db.person.insert(uuid=nid, name='Massimo')
    db.thing.insert(name='Chair', owner_id=nid)
     
```

> 请注意，在上面的表定义中，两个`uuid`字段的默认值设置为一个 lambda 函数，该函数返回一个 UUID（转换为字符串）。每次插入的记录都会调用一次 lambda 函数，以确保每条记录都获得唯一的 UUID，即使在单个事务中插入了多条记录也是如此。

创建一个控制器动作来导出数据库：

```
def export():
    s = StringIO.StringIO()
    db.export_to_csv_file(s)
    response.headers['Content-Type'] = 'text/csv'
    return s.getvalue()
```

创建一个控制器操作以导入其他数据库的已保存副本并同步记录：

```
def import_and_sync():
    form = FORM(INPUT(_type='file', _name='data'), INPUT(_type='submit'))
    if form.process().accepted:
        db.import_from_csv_file(form.vars.data.file, unique=False)
        # for every table
        for tablename in db.tables:
            table = db[tablename]
            # for every uuid, delete all but the latest
            items = db(table).select(table.id, table.uuid,
                                     orderby=~table.modified_on,
                                     groupby=table.uuid)
            for item in items:
                db((table.uuid == item.uuid) & (table.id != item.id)).delete()
    return dict(form=form)
```

或者，您应该手动创建索引以使 uuid 的搜索速度更快。

或者，您可以使用 XML-RPC 导出/导入文件。

如果记录引用上传的文件，您还需要导出/导入上传文件夹的内容。请注意，其中的文件已由 UUID 标记，因此您无需担心命名冲突和引用。



#### HTML 和 XML（一次一个表）

Rows 对象也有一个`xml`将其序列化为 XML/HTML 的方法（如帮助程序）：

```
>>> rows = db(db.person.id == db.thing.owner_id).select()
>>> print rows.xml()
```

```
<table>
<thead>
<tr><th>person.id</th><th>person.name</th><th>thing.id</th><th>thing.name</th><th>thing.owner_id</th></tr>
</thead>
<tbody>
<tr class="w2p_odd odd"><td>1</td><td>Alex</td><td>1</td><td>Boat</td><td>1</td></tr>
<tr class="w2p_even even"><td>1</td><td>Alex</td><td>2</td><td>Chair</td><td>1</td></tr>
<tr class="w2p_odd odd"><td>2</td><td>Bob</td><td>3</td><td>Shoes</td><td>2</td></tr>
</tbody>
</table>
```



如果您需要使用自定义标记以任何其他 XML 格式序列化 Rows，您可以使用通用`TAG`帮助器（在[第 5 章](http://web2py.com/books/default/chapter/29/05#TAG)`*<iterable>`中描述）和函数调用中允许的 Python 语法轻松地做到这一点：

```
>>> rows = db(db.person).select()
>>> print TAG.result(*[TAG.row(*[TAG.field(r[f], _name=f) for f in db.person.fields]) for r in rows])
```

```
<result>
<row><field name="id">1</field><field name="name">Alex</field></row>
<row><field name="id">2</field><field name="name">Bob</field></row>
<row><field name="id">3</field><field name="name">Carl</field></row>
</result>
```



#### 数据表示

该`Rows.export_to_csv_file`方法接受一个名为 的关键字参数`represent`。何时在导出数据而不是原始数据时`True`使用列函数。`represent`

该函数还接受一个名为的关键字参数`colnames`，该参数应包含一个希望导出的列名列表。它默认为所有列。

`export_to_csv_file`和接受关键字参数，告诉csv`import_from_csv_file`解析器保存/加载文件的格式：

- `delimiter`: 分隔值的分隔符（默认为 ','）
- `quotechar`: 用于引用字符串值的字符（默认为双引号）
- `quoting`: 报价系统（默认`csv.QUOTE_MINIMAL`）

这是一些示例用法：

```
import csv
rows = db(query).select()
with open('/tmp/test.txt', 'wb') as oufile:
    rows.export_to_csv_file(oufile,
                            delimiter='|',
                            quotechar='"',
                            quoting=csv.QUOTE_NONNUMERIC)       
                            
                            
```

这将呈现类似于

```
"hello"|35|"this is the text description"|"2013-03-03"
```

有关更多信息，请参阅官方 Python 文档[ [quoteall](http://web2py.com/books/default/reference/29/quoteall) ]



### 缓存选择

select 方法也有一个`cache`参数，默认为 None。出于缓存目的，它应该设置为一个元组，其中第一个元素是缓存模型（`cache.ram`、`cache.disk`等），第二个元素是过期时间（以秒为单位）。

在下面的示例中，您会看到一个控制器，它在先前定义的 db.log 表上缓存了一个选择。实际的 select 从后端数据库获取数据的频率不超过每 60 秒一次，并将结果存储在内存中。如果自上次数据库 IO 后不到 60 秒内发生对该控制器的下一次调用，则它只是从内存中获取先前的数据。

```
def cache_db_select():
    logs = db().select(db.log.ALL, cache=(cache.ram, 60))
    return dict(logs=logs)
```

该`select`方法有一个可选`cacheable`参数，通常设置为 False。当`cacheable=True`结果`Rows`是可序列化的但`Row`缺少 s`update_record`和`delete_record`方法时。

如果您不需要这些方法，您可以通过设置`cacheable`属性来加快选择速度：

```
rows = db(query).select(cacheable=True)
```

如果`cache`设置了参数，但`cacheable=False`（默认）仅缓存数据库结果，而不是实际的 Rows 对象。当`cache`参数与`cacheable=True`整个 Rows 对象结合使用时，缓存会得到更快的缓存：

```
rows = db(query).select(cache=(cache.ram, 3600), cacheable=True)
```



### 自引用和别名



可以使用引用自身的字段定义表，这是一个示例：



```
db.define_table('person',
                Field('name'),
                Field('father_id', 'reference person'),
                Field('mother_id', 'reference person'))
```

请注意，在这种情况下，使用表对象作为字段类型的替代表示法将失败，因为它在定义之前使用了表：

```
db.define_table('person',
                Field('name'),
                Field('father_id', db.person),  # wrong!
                Field('mother_id', db['person']))  # wrong!
```

通常`db.tablename`和`'reference tablename'`是等效的字段类型，但后者是唯一允许自引用的字段类型。

当一个表有自引用并且您必须进行连接时，例如选择一个人和他的父亲，您需要一个表的别名。在 SQL 中，别名是一个临时替代名称，可用于将表/列引用到查询（或其他 SQL 语句）中。

使用 web2py，您可以使用该`with_alias`方法为表创建别名。这也适用于表达式，这意味着也适用于字段，因为`Field`它派生自`Expression`.

这是一个例子：

```
>>> fid, mid = db.person.bulk_insert([dict(name='Massimo'), dict(name='Claudia')])
>>> db.person.insert(name='Marco', father_id=fid, mother_id=mid)
3
>>> Father = db.person.with_alias('father')
>>> Mother = db.person.with_alias('mother')
>>> type(Father)
<class 'pydal.objects.Table'>
>>> str(Father)
'person AS father'
>>> rows = db().select(db.person.name, Father.name, Mother.name,
...                    left=(Father.on(Father.id == db.person.father_id),
...                          Mother.on(Mother.id == db.person.mother_id)))
>>> for row in rows:
...     print row.person.name, row.father.name, row.mother.name
... 
Massimo None None
Claudia None None
Marco Massimo Claudia
```

请注意，我们选择区分：

- “father_id”：“person”表中使用的字段名；
- “father”：我们要为上述字段引用的表使用的别名；这被传达给数据库；
- “父亲”：web2py 用来引用该别名的变量。

区别是微妙的，对它们三个使用相同的名称并没有错：

```
>>> db.define_table('person',
...                 Field('name'),
...                 Field('father', 'reference person'),
...                 Field('mother', 'reference person'))
<Table person (id, name, father, mother)>
>>> fid, mid = db.person.bulk_insert([dict(name='Massimo'), dict(name='Claudia')])
>>> db.person.insert(name='Marco', father=fid, mother=mid)
3
>>> father = db.person.with_alias('father')
>>> mother = db.person.with_alias('mother')
>>> rows = db().select(db.person.name, father.name, mother.name,
...                    left=(father.on(father.id==db.person.father),
...                          mother.on(mother.id==db.person.mother)))
>>> for row in rows:
...     print row.person.name, row.father.name, row.mother.name
... 
Massimo None None
Claudia None None
Marco Massimo Claudia
```

但重要的是要明确区分以构建正确的查询。



### 高级功能



#### 表继承

可以创建一个包含另一个表中的所有字段的表。将另一个表代替字段传递给 就足够了`define_table`。例如

```
>>> db.define_table('person', Field('name'), Field('gender'))
<Table person (id, name, gender)>
>>> db.define_table('doctor', db.person, Field('specialization'))
<Table doctor (id, name, gender, specialization)>
```

也可以定义一个未存储在数据库中的虚拟表，以便在多个其他地方重用它。例如：

```
signature = db.Table(db, 'signature',
                     Field('is_active', 'boolean', default=True),
                     Field('created_on', 'datetime', default=request.now),
                     Field('created_by', db.auth_user, default=auth.user_id),
                     Field('modified_on', 'datetime', update=request.now),
                     Field('modified_by', db.auth_user, update=auth.user_id))

db.define_table('payment', Field('amount', 'double'), signature)
```

此示例假定启用了标准 web2py 身份验证。

请注意，如果您使用`Auth`web2py 已经为您创建了一个这样的表：

```
auth = Auth(db)
db.define_table('payment', Field('amount', 'double'), auth.signature)
```

使用表继承时，如果希望继承表继承验证器，请务必在定义继承表之前定义父表的验证器。



#### `filter_in`和`filter_out`





可以在为该字段将值插入数据库之前以及从数据库中检索到值之后，为要调用的每个字段定义一个过滤器。

例如，假设您希望将可序列化的 Python 数据结构存储在 json 格式的字段中。这是如何实现的：

```
>>> import json
>>> db.define_table('anyobj',
...                 Field('name'),
...                 Field('data', 'text'))
<Table anyobj (id, name, data)>
>>> db.anyobj.data.filter_in = lambda obj: json.dumps(obj)
>>> db.anyobj.data.filter_out = lambda txt: json.loads(txt)
>>> myobj = ['hello', 'world', 1, {2: 3}]
>>> aid = db.anyobj.insert(name='myobjname', data=myobj)
>>> row = db.anyobj[aid]
>>> row.data
['hello', 'world', 1, {'2': 3}]
```

另一种实现相同目的的方法是使用类型的字段`SQLCustomType`，如下一个[自定义`Field`类型](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#Custom-Field-types)部分所述。



#### 记录插入、删除和更新的回调





Web2py 提供了一种机制来注册回调，以便在插入、更新和删除记录之前和/或之后调用。

每个表存储六个回调列表：

```
db.mytable._before_insert
db.mytable._after_insert
db.mytable._before_update
db.mytable._after_update
db.mytable._before_delete
db.mytable._after_delete
```

您可以通过将回调函数附加到相应的列表来注册它。需要注意的是，根据功能，回调具有不同的签名。

这最好通过一些例子来解释。

```
>>> db.define_table('person', Field('name'))
<Table person (id, name)>
>>> def pprint(callback, *args):
...     print "%s%s" % (callback, args)
... 
>>> db.person._before_insert.append(lambda f: pprint('before_insert', f))
>>> db.person._after_insert.append(lambda f, i: pprint('after_insert', f, i))
>>> db.person.insert(name='John')
before_insert(<OpRow {'name': 'John'}>,)
after_insert(<OpRow {'name': 'John'}>, 1L)
1L
>>> db.person._before_update.append(lambda s, f: pprint('before_update', s, f))
>>> db.person._after_update.append(lambda s, f: pprint('after_update', s, f))
>>> db(db.person.id == 1).update(name='Tim')
before_update(<Set ("person"."id" = 1)>, <OpRow {'name': 'Tim'}>)
after_update(<Set ("person"."id" = 1)>, <OpRow {'name': 'Tim'}>)
1
>>> db.person._before_delete.append(lambda s: pprint('before_delete', s))
>>> db.person._after_delete.append(lambda s: pprint('after_delete', s))
>>> db(db.person.id == 1).delete()
before_delete(<Set ("person"."id" = 1)>,)
after_delete(<Set ("person"."id" = 1)>,)
1
```

如你看到的：

- `f`获取`OpRow`带有数据的对象以进行插入或更新。
- `i`传递新插入记录的 id。
- `s`获取`Set`用于更新或删除的对象。

`OpRow`是一个专门用于存储 (field, value) 对的辅助对象，您可以将其视为普通字典，即使使用属性表示法的语法（即`f.name`和`f['name']`等价）也可以使用。

这些回调的返回值应该是`None`or `False`。如果任何`_before_*`回调返回一个`True`值，它将中止实际的插入/更新/删除操作。

有时回调可能需要在同一个或不同的表中执行更新，并且希望避免触发其他回调，这可能导致无限循环。

为此，`Set`对象有一个`update_naive`方法，其工作方式`update`与回调前后类似，但忽略了回调。

##### 数据库级联

数据库模式可以定义触发删除相关记录的关系，称为级联。当记录由于级联而被删除时，不会通知 DAL。因此，不会将 *_delete 回调称为级联删除的后果。





#### 记录版本控制

当记录被单独修改时，可以要求 web2py 保存记录的每个副本。有不同的方法可以做到这一点，可以使用以下语法一次对所有表完成：

```
auth.enable_record_versioning(db)
```

这需要`Auth`并在[第 9 章](http://web2py.com/books/default/chapter/29/09#Record-versioning)中讨论。它也可以为每个单独的表完成，如下所述。

考虑下表：

```
db.define_table('stored_item',
                Field('name'),
                Field('quantity', 'integer'),
                Field('is_active', 'boolean',
                      writable=False, readable=False, default=True))
```

请注意隐藏的布尔字段调用`is_active`并默认为 True。

我们可以告诉 web2py 创建一个新表（在相同或不同的数据库中），并在修改时将每条记录的所有先前版本存储在表中。

这是通过以下方式完成的：

```
db.stored_item._enable_record_versioning()
```

或更详细的语法：

```
db.stored_item._enable_record_versioning(archive_db=db,
                                         archive_name='stored_item_archive',
                                         current_record='current_record',
                                         is_active='is_active')
```

告诉 web2py将`archive_db=db`存档表存储在与表相同的数据库`stored_item`中。`archive_name`设置存档表的名称。存档表具有与原始表相同的字段，`stored_item`除了唯一字段不再唯一（因为它需要存储多个版本）并且有一个额外的字段，该字段的名称由指定并且是对表`current_record`中当前记录的引用`stored_item`.

当记录被删除时，它们并没有真正被删除。将删除的记录复制到`stored_item_archive`表中（就像修改时一样），并且该`is_active`字段设置为 False。通过启用记录版本控制，web2py`common_filter`在该表上设置了一个隐藏表`stored_item`中`is_active`字段设置为 False 的所有记录。该方法中的`is_active`参数`_enable_record_versioning`允许指定字段的名称，`common_filter`以确定该字段是否被删除。

`common_filter`s 将在下一个[通用过滤器](http://web2py.com/books/default/chapter/29/06/the-database-abstraction-layer#Common-filters)部分讨论。



#### 公共字段和多租户





`db._common_fields`是应属于所有表的字段列表。该列表还可以包含表格，它被理解为表格中的所有字段。

例如，有时您发现自己需要为所有表格添加签名，但`Auth`表格除外。在这种情况下，在您`auth.define_tables()`定义任何其他表之前，插入：

```
db._common_fields.append(auth.signature)
```

一个字段是特殊的：`request_tenant`，您可以在 中设置不同的名称`db._request_tenant`。此字段不存在，但您可以创建它并将其添加到您的任何表（或所有表）中：

```
db._common_fields.append(Field('request_tenant',
                               default=request.env.http_host,
                               writable=False))
```

对于具有此类字段的每个表，所有查询的所有记录始终自动过滤：

```
db.table.request_tenant == db.table.request_tenant.default
```

并且对于插入的每条记录，该字段都设置为默认值。在上面的示例中，我们选择了：

```
default = request.env.http_host
```

这意味着我们选择让我们的应用程序过滤所有查询中的所有表：

```
db(query, ignore_common_filters=True)
```

这个简单的技巧允许我们将任何应用程序变成多租户应用程序。即使我们运行应用程序的一个实例并使用一个数据库，当在两个或多个域下访问应用程序时，访问者将看到不同的数据，具体取决于域（在示例中，域名是从 检索的`request.env.http_host`）。

`ignore_common_filters=True`您可以在`Set`创建时使用以下方法关闭多租户过滤器：

```
db(query, ignore_common_filters=True)
```



#### 常用过滤器

通用过滤器是上述多租户思想的推广。它提供了一种简单的方法来防止重复相同的查询。例如考虑下表：

```
db.define_table('blog_post',
                Field('subject'),
                Field('post_text', 'text'),
                Field('is_public', 'boolean'),
                common_filter = lambda query: db.blog_post.is_public == True)
```

此表中的任何选择、删除或更新都将仅包括公共博客文章。该属性也可以在运行时修改：

```
db.blog_post._common_filter = lambda query: ...
```

它既可以作为避免在每个博客帖子搜索中重复“db.blog_post.is_public==True”短语的一种方式，也可以作为一种安全增强功能，防止您忘记禁止查看非公开帖子。

如果您确实希望公共过滤器遗漏的项目（例如，允许管理员查看非公开帖子），您可以删除过滤器：

```
db.blog_post._common_filter = None
```

或忽略它：

```
db(query, ignore_common_filters=True)
```

> 请注意，appadmin 界面会忽略 common_filters。



#### 自定义`Field`类型

除了使用`filter_in`and之外`filter_out`，还可以定义新的/自定义字段类型。例如，假设您要定义一个自定义类型来存储 IP 地址：

```
>>> def ip2int(sv):
...     "Convert an IPV4 to an integer."
...     sp = sv.split('.'); assert len(sp) == 4 # IPV4 only
...     iip = 0
...     for i in map(int, sp): iip = (iip<<8) + i
...     return iip
... 
>>> def int2ip(iv):
...     "Convert an integer to an IPV4."
...     assert iv > 0
...     iv = (iv,); ov = []
...     for i in range(3):
...         iv = divmod(iv[0], 256)
...         ov.insert(0, iv[1])
...     ov.insert(0, iv[0])
...     return '.'.join(map(str, ov))
... 
>>> from gluon.dal import SQLCustomType
>>> ipv4 = SQLCustomType(type='string', native='integer',
...                      encoder=lambda x : str(ip2int(x)), decoder=int2ip)
>>> db.define_table('website',
...                 Field('name'),
...                 Field('ipaddr', type=ipv4))
<Table website (id, name, ipaddr)>
>>> db.website.insert(name='wikipedia', ipaddr='91.198.174.192')
1
>>> db.website.insert(name='google', ipaddr='172.217.11.174')
2
>>> db.website.insert(name='youtube', ipaddr='74.125.65.91')
3
>>> db.website.insert(name='github', ipaddr='207.97.227.239')
4
>>> rows = db(db.website.ipaddr > '100.0.0.0').select(orderby=~db.website.ipaddr)
>>> for row in rows:
...     print row.name, row.ipaddr
... 
github 207.97.227.239
google 172.217.11.174
      
```

`SQLCustomType`是一个字段类型工厂。它的`type`参数必须是标准的 web2py 类型之一。它告诉 web2py 如何在 web2py 级别处理字段值。`native`就数据库而言，是字段的类型。允许的名称取决于数据库引擎。`encoder`是存储数据时应用的可选变换函数，`decoder`是可选的反向变换函数。

> 此功能被标记为实验性的。实际上，它已经在 web2py 中使用了很长时间并且可以工作，但是它会使代码不可移植，例如当本机类型是特定于数据库的时候。
>
> 它不适用于 Google App Engine NoSQL。



#### 在没有定义表的情况下使用 DAL

只需执行以下操作，即可在任何 Python 程序中使用 DAL：

```
from gluon import DAL
db = DAL('sqlite://storage.sqlite', folder='path/to/app/databases')
```

即导入 DAL，连接并指定包含 .table 文件的文件夹（app/databases 文件夹）。

要访问数据及其属性，我们仍然必须定义要访问的所有表`db.define_table`。

如果我们只需要访问数据而不需要访问 web2py 表属性，我们无需重新定义表，而只是要求 web2py 从 .table 文件中的元数据中读取必要的信息：

```
from gluon import DAL
db = DAL('sqlite://storage.sqlite', folder='path/to/app/databases', auto_import=True)
```

这允许我们访问任何 db.table 而无需重新定义它。



#### PostGIS、SpatiaLite 和 MS Geo（实验性）





DAL 支持使用 PostGIS（用于 PostgreSQL）、SpatiaLite（用于 SQLite）以及 MSSQL 和空间扩展的地理 API。这是由 Sahana 项目赞助并由 Denes Lengyel 实施的功能。

DAL 提供几何和地理字段类型以及以下功能：





```
st_asgeojson (PostGIS only)
st_astext
st_contains
st_distance
st_equals
st_intersects
st_overlaps
st_simplify (PostGIS only)
st_touches
st_within
st_x
st_y
```

这里有些例子：

```
>>> from gluon.dal import DAL, Field, geoPoint, geoLine, geoPolygon
>>> db = DAL("mssql://user:pass@host:db")
>>> sp = db.define_table('spatial', Field('loc', 'geometry()'))
```

下面我们插入一个点、一条线和一个多边形：

```
>>> sp.insert(loc=geoPoint(1, 1))
1
>>> sp.insert(loc=geoLine((100, 100), (20, 180), (180, 180)))
2
>>> sp.insert(loc=geoPolygon((0, 0), (150, 0), (150, 150), (0, 150), (0, 0)))
3
```

请注意

```
rows = db(sp).select()
```

始终返回序列化为文本的几何数据。您也可以更明确地使用`st_astext()`：

```
>>> print db(sp).select(sp.id, sp.loc.st_astext())
spatial.id,spatial.loc.STAsText()
1,"POINT (1 2)"
2,"LINESTRING (100 100, 20 180, 180 180)"
3,"POLYGON ((0 0, 150 0, 150 150, 0 150, 0 0))"
```

`st_asgeojson()`您可以使用（仅在 PostGIS 中）请求本机表示：

```
>>> print db(sp).select(sp.id, sp.loc.st_asgeojson().with_alias('loc'))
spatial.id,loc
1,[1, 2]
2,[[100, 100], [20 180], [180, 180]]
3,[[[0, 0], [150, 0], [150, 150], [0, 150], [0, 0]]]
```

（注意一个数组是一个点，一个数组数组是一条线，一个数组数组是一个多边形）。

以下是如何使用地理功能的示例：

```
>>> query = sp.loc.st_intersects(geoLine((20, 120), (60, 160)))
>>> query = sp.loc.st_overlaps(geoPolygon((1, 1), (11, 1), (11, 11), (11, 1), (1, 1)))
>>> query = sp.loc.st_contains(geoPoint(1, 1))
>>> print db(query).select(sp.id, sp.loc)
spatial.id,spatial.loc
3,"POLYGON ((0 0, 150 0, 150 150, 0 150, 0 0))"
```

计算的距离也可以作为浮点数检索：

```
>>> dist = sp.loc.st_distance(geoPoint(-1,2)).with_alias('dist')
>>> print db(sp).select(sp.id, dist)
spatial.id,dist
1,2.0
2,140.714249456
3,1.0
```



#### 将数据从一个数据库复制到另一个数据库

考虑您一直在使用以下数据库的情况：

```
db  =  DAL ( 'sqlite://storage.sqlite' )
```

并且您希望使用不同的连接字符串移动到另一个数据库：

```
db  =  DAL ( 'postgres://username:password@localhost/mydb' )
```

在切换之前，您希望移动数据并为新数据库重建所有元数据。我们假设新数据库存在，但我们也假设它是空的。

Web2py 提供了一个脚本来为你工作：

```
cd web2py
python scripts/cpdb.py \
   -f applications/app/databases \
   -y 'sqlite://storage.sqlite' \
   -Y 'postgres://username:password@localhost/mydb' \
   -d ../gluon
```

运行脚本后，您可以简单地切换模型中的连接字符串，一切都应该开箱即用。新数据应该在那里。

该脚本提供了各种命令行选项，允许您将数据从一个应用程序移动到另一个应用程序、移动所有表或仅移动部分表、清除表中的数据。有关更多信息，请尝试：

```
python scripts/cpdb.py -h
```



#### 关于新 DAL 和适配器的注意事项

数据库抽象层的源代码在 2010 年被完全重写。虽然它保持向后兼容，但重写使其更加模块化并且更容易扩展。这里我们解释一下主要逻辑。

文件“gluon/dal.py”除其他外定义了以下类。

```
ConnectionPool
BaseAdapter extends ConnectionPool
Row
DAL
Reference
Table
Expression
Field
Query
Set
Rows
```

除了`BaseAdapter`. 当一个`Table`或`Set`对象的方法需要与数据库通信时，它们将任务委托给适配器的方法以生成 SQL 和/或函数调用。

例如：

```
db.mytable.insert(myfield='myvalue')
```

来电

```
Table.insert(myfield='myvalue')
```

它通过返回来委托适配器：

```
db._adapter.insert(db.mytable, db.mytable._listify(dict(myfield='myvalue')))
```

这里`db.mytable._listify`将参数的 dict 转换为列表并`(field,value)`调用`insert`. 或多或少做了以下事情：`adapter``db._adapter`

```
query = db._adapter._insert(db.mytable, list_of_fields)
db._adapter.execute(query) 
```

第一行构建查询，第二行执行它。

`BaseAdapter`定义所有适配器的接口。

在编写本书时，“gluon/dal.py”包含以下适配器：

```
SQLiteAdapter extends BaseAdapter
JDBCSQLiteAdapter extends SQLiteAdapter
MySQLAdapter extends BaseAdapter
PostgreSQLAdapter extends BaseAdapter
JDBCPostgreSQLAdapter extends PostgreSQLAdapter
OracleAdapter extends BaseAdapter
MSSQLAdapter extends BaseAdapter
MSSQL2Adapter extends MSSQLAdapter
MSSQL3Adapter extends MSSQLAdapter
MSSQL4Adapter extends MSSQLAdapter
FireBirdAdapter extends BaseAdapter
FireBirdEmbeddedAdapter extends FireBirdAdapter
InformixAdapter extends BaseAdapter
DB2Adapter extends BaseAdapter
IngresAdapter extends BaseAdapter
IngresUnicodeAdapter extends IngresAdapter
GoogleSQLAdapter extends MySQLAdapter
NoSQLAdapter extends BaseAdapter
GoogleDatastoreAdapter extends NoSQLAdapter
CubridAdapter extends MySQLAdapter (experimental)
TeradataAdapter extends DB2Adapter (experimental)
SAPDBAdapter extends BaseAdapter (experimental)
CouchDBAdapter extends NoSQLAdapter (experimental)
IMAPAdapter extends NoSQLAdapter (experimental)
MongoDBAdapter extends NoSQLAdapter (experimental)
VerticaAdapter extends MSSQLAdapter (experimental)
SybaseAdapter extends MSSQLAdapter (experimental)
```

它覆盖了`BaseAdapter`.

每个适配器或多或少都有这样的结构：

```
class MySQLAdapter(BaseAdapter):

    # specify a driver to use
    driver = globals().get('pymysql', None)

    # map web2py types into database types
    types = {
        'boolean': 'CHAR(1)',
        'string': 'VARCHAR(%(length)s)',
        'text': 'LONGTEXT',
        ...
        }

    # connect to the database using driver
    def __init__(self, db, uri, pool_size=0, folder=None, db_codec ='UTF-8',
                 credential_decoder=lambda x:x, driver_args={},
                 adapter_args={}):
        # parse uri string and store parameters in driver_args
        ...
        # define a connection function
        def connect(driver_args=driver_args):
            return self.driver.connect(**driver_args)
        # place it in the pool
        self.pool_connection(connect)
        # set optional parameters (after connection)
        self.execute('SET FOREIGN_KEY_CHECKS=1;')
        self.execute("SET sql_mode='NO_BACKSLASH_ESCAPES';")

   # override BaseAdapter methods as needed
   def lastrowid(self, table):
        self.execute('select last_insert_id();')
        return int(self.cursor.fetchone()[0])
```

以各种适配器为例，编写新的适配器应该很容易。

创建`db`实例时：

```
db  =  DAL ( 'mysql://...' )
```

uri 字符串中的前缀定义了适配器。映射在以下字典中也定义在“gluon/dal.py”中：

```
ADAPTERS = {
    'sqlite': SQLiteAdapter,
    'spatialite': SpatiaLiteAdapter,
    'sqlite:memory': SQLiteAdapter,
    'spatialite:memory': SpatiaLiteAdapter,
    'mysql': MySQLAdapter,
    'postgres': PostgreSQLAdapter,
    'postgres:psycopg2': PostgreSQLAdapter,
    'postgres2:psycopg2': NewPostgreSQLAdapter,
    'oracle': OracleAdapter,
    'mssql': MSSQLAdapter,
    'mssql2': MSSQL2Adapter,
    'mssql3': MSSQL3Adapter,
    'mssql4' : MSSQL4Adapter,
    'vertica': VerticaAdapter,
    'sybase': SybaseAdapter,
    'db2': DB2Adapter,
    'teradata': TeradataAdapter,
    'informix': InformixAdapter,
    'informix-se': InformixSEAdapter,
    'firebird': FireBirdAdapter,
    'firebird_embedded': FireBirdAdapter,
    'ingres': IngresAdapter,
    'ingresu': IngresUnicodeAdapter,
    'sapdb': SAPDBAdapter,
    'cubrid': CubridAdapter,
    'jdbc:sqlite': JDBCSQLiteAdapter,
    'jdbc:sqlite:memory': JDBCSQLiteAdapter,
    'jdbc:postgres': JDBCPostgreSQLAdapter,
    'gae': GoogleDatastoreAdapter, # discouraged, for backward compatibility
    'google:datastore': GoogleDatastoreAdapter,
    'google:datastore+ndb': GoogleDatastoreAdapter,
    'google:sql': GoogleSQLAdapter,
    'couchdb': CouchDBAdapter,
    'mongodb': MongoDBAdapter,
    'imap': IMAPAdapter
}
```

然后适配器本身会更详细地解析 uri 字符串。

对于任何适配器，您都可以用不同的驱动程序替换驱动程序：

```
import MySQLdb as mysqldb
from gluon.dal import MySQLAdapter
MySQLAdapter.driver = mysqldb
```

即`mysqldb`必须是具有 .connect() 方法的*模块。*您可以指定可选的驱动程序参数和适配器参数：

```
db  = DAL ( ... ,  driver_args = {},  adapter_args = {})
```



### Gotchas



#### SQLite

SQLite 不支持删除和更改列。这意味着 web2py 迁移将在一定程度上起作用。如果从表中删除一个字段，该列将保留在数据库中，但对 web2py 是不可见的。如果您决定恢复该列，web2py 将尝试重新创建它并失败。在这种情况下，您必须进行设置`fake_migrate=True`，以便在不尝试再次添加列的情况下重建元数据。此外，出于同样的原因，**SQLite**不知道列类型的任何变化。如果您在字符串字段中插入一个数字，它将被存储为字符串。如果您稍后更改模型并将类型“字符串”替换为“整数”类型，SQLite 将继续将数字保留为字符串，这可能会在您尝试提取数据时导致问题。

SQLite 没有布尔类型。web2py 在内部将布尔值映射到 1 个字符串，其中 'T' 和 'F' 代表 True 和 False。DAL 完全处理这个问题；真正的布尔值的抽象效果很好。但是，如果您直接使用 SQL 更新 SQLite 表，请注意 web2py 实现，并避免使用 0 和 1 值。



#### MySQL

MySQL 不支持单个事务中的多个 ALTER TABLE。这意味着任何迁移过程都会分成多个提交。如果发生导致失败的事情，则可能会中断迁移（web2py 元数据不再与数据库中的实际表结构同步）。这是不幸的，但它可以被阻止（一次迁移一个表）或者它可以被修复（将 web2py 模型恢复为与数据库中的表结构相对应的模型，设置`fake_migrate=True`和在重建元数据后，设置`fake_migrate=False`和迁移再次上表）。



#### 谷歌 SQL

Google SQL 有与 MySQL 相同的问题等等。特别是表元数据本身必须存储在数据库中的一个不被 web2py 迁移的表中。这是因为 Google App Engine 具有只读文件系统。Google SQL 中的 Web2py 迁移与上述 MySQL 问题相结合可能会导致元数据损坏。同样，这可以防止（通过一次迁移表然后设置 migrate=False 以便不再访问元数据表）或者它可以修复后验（通过使用谷歌仪表板访问数据库并删除任何损坏的条目从表中称`web2py_filesystem`。



#### MSSQL（微软 SQL 服务器）

MSSQL < 2012 不支持 SQL OFFSET 关键字。因此数据库无法进行分页。当执行`limitby=(a, b)`web2py 时，将获取第一`a + b`行并丢弃第一行`a`。与其他数据库引擎相比，这可能会导致相当大的开销。如果您使用的是 MSSQL >= 2005，建议使用的前缀是`mssql3://`它提供了一种方法来避免获取整个非分页结果集的问题。如果您使用的是 MSSQL >= 2012，请使用`mssql4://`该`OFFSET ... ROWS ... FETCH NEXT ... ROWS ONLY`构造来支持本机分页，而不会像其他后端那样影响性能。`mssql://`uri 还强制（出于历史原因）使用列，这些`text`列在最近的版本（从 2005 年起）中被`varchar(max)`. `mssql3://`和`mssql4://`如果您不想面对 - 正式弃用 -`text`列的一些限制，则应该使用它。

MSSQL 在具有 ONDELETE CASCADE 的表中存在循环引用问题。这是一个 MSSQL 错误，您可以通过将所有引用字段的 ondelete 属性设置为“NO ACTION”来解决它。您也可以在定义表之前一劳永逸地执行此操作：

```
db = DAL('mssql://....')
for key in db._adapter.types:
    if ' ON DELETE %(on_delete_action)s' in db._adapter.types[key]:
        db._adapter.types[key] = db._adapter.types[key].replace('%(on_delete_action)s', 'NO ACTION')
```

MSSQL 在传递给 DISTINCT 关键字的参数方面也存在问题，因此虽然这可行，

```
db(query).select(distinct=True)
```

这不

```
db(query).select(distinct=db.mytable.myfield)
```



#### 甲骨文

Oracle 也不支持分页。它既不支持 OFFSET 也不支持 LIMIT 关键字。Web2py 通过将 a`db(...).select(limitby=(a, b))`转换为复杂的三向嵌套选择来实现分页（正如 Oracle 官方文档所建议的那样）。这适用于简单的选择，但可能会破坏涉及别名字段和/或连接的复杂选择。



#### 谷歌 NoSQL（数据存储）

Google NoSQL (Datastore) 不允许连接、左连接、聚合、表达式或涉及多个表，'like' 运算符在“文本”字段中搜索。

事务是有限的，并且不会由 web2py 自动提供（您需要使用`run_in_transaction`可以在 Google App Engine 在线文档中查找的 Google API）。

Google 还限制了您在每个查询中可以检索的记录数（在撰写本文时为 1000 条）。在 Google 数据存储中，记录 ID 是整数，但不是连续的。在 SQL 中，“list:string”类型被映射为“text”类型，而在 Google 数据存储中，它被映射为`ListStringProperty`. 类似地，“list:integer”和“list:reference”被映射到`ListProperty`. 这使得在 Google NoSQL 上搜索这些字段类型内的内容比在 SQL 数据库上更有效。