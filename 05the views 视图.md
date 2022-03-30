## 视图





web2py 在其模型、控制器和视图中使用 Python，尽管它在视图中使用了稍微修改的 Python 语法以允许更易读的代码，而不会对正确使用 Python 施加任何限制。

视图的目的是在 HTML 文档中嵌入代码 (Python)。一般来说，这会带来一些问题：

- 嵌入式代码应该如何转义？
- 缩进应该基于 Python 还是 HTML 规则？

web2py 用于`{{ ... }}`转义嵌入在 HTML 中的 Python 代码。使用大括号代替尖括号的优点是它对所有常见的 HTML 编辑器都是透明的。这允许开发人员使用这些编辑器来创建 web2py 视图。这些分隔符可以更改，例如

```
response.delimiters = ('<?', '?>')
```

如果这条线在模型中，它将被应用到任何地方，如果在控制器中仅适用于控制器操作的视图，如果在操作内仅适用于该操作的视图。

由于开发人员将 Python 代码嵌入到 HTML 中，因此文档应该根据 HTML 规则而不是 Python 规则缩进。`{{ ... }}`因此，我们允许在标签内使用未缩进的 Python 。由于 Python 通常使用缩进来分隔代码块，因此我们需要一种不同的方式来分隔它们；这就是 web2py 模板语言使用 Python 关键字的原因`pass`。

> **代码块以冒号结尾的行开始，以 . 开头的行结束`pass`。**当从上下文中可以明显看出块的结尾时，关键字`pass`不是必需的。

这是一个例子：

```
{{
if i == 0:
response.write('i is 0')
else:
response.write('i is not 0')
pass
}}
```

请注意，这`pass`是一个 Python 关键字，而不是 web2py 关键字。一些 Python 编辑器，例如 Emacs，使用关键字`pass`来表示块的划分，并使用它来自动重新缩进代码。

web2py 模板语言完全一样。当它发现类似的东西时：

```
<html><body>
{{for x in range(10):}}{{=x}}hello<br />{{pass}}
</body></html>
```

它将它翻译成一个程序：

```
response.write("""<html><body>""", escape=False)
for x in range(10):
    response.write(x)
    response.write("""hello<br />""", escape=False)
response.write("""</body></html>""", escape=False)
```

`response.write`写入`response.body`.

当 web2py 视图出现错误时，错误报告会显示生成的视图代码，而不是开发人员编写的实际视图。这有助于开发人员通过突出显示实际执行的代码来调试代码（可以使用 HTML 编辑器或浏览器的 DOM 检查器进行调试）。

另请注意：

```
{{=x}}
```

生成



```
response.write(x)
```

以这种方式注入 HTML 的变量默认会被转义。`x`如果是对象，则忽略转义`XML`，即使将转义设置为`True`。

这是一个介绍`H1`助手的示例：

```
{{=H1(i)}}
```

翻译为：

```
response.write(H1(i))
```

在评估时，`H1`对象及其组件被递归序列化、转义并写入响应主体。`H1`由HTML 和内部 HTML生成的标签不会被转义。这种机制保证了网页上显示的所有文本——并且只有文本——总是被转义，从而防止XSS漏洞。同时，代码简单易调试。

该方法`response.write(obj, escape=True)`有两个参数，要写入的对象以及是否必须转义（`True`默认设置为）。如果`obj`有`.xml()`方法，则调用它并将结果写入响应正文（`escape`忽略参数）。否则，它使用对象的`__str__`方法来序列化它，如果转义参数是`True`，则转义它。所有内置的辅助对象（`H1`在示例中）都是知道如何通过该`.xml()`方法序列化自己的对象。

这一切都是透明地完成的。您永远不需要（也不应该）`response.write`显式调用该方法。



### 基本语法

web2py 模板语言支持所有 Python 控制结构。在这里，我们提供了它们中的每一个的一些示例。它们可以根据通常的编程实践进行嵌套。



#### `for...in`

在模板中，您可以遍历任何可迭代对象：

```
{{items = ['a', 'b', 'c']}}
<ul>
{{for item in items:}}<li>{{=item}}</li>{{pass}}
</ul>
```

产生：

```
<ul>
<li>a</li>
<li>b</li>
<li>c</li>
</ul>
```

这`items`是任何可迭代对象，例如 Python 列表、Python 元组或 Rows 对象，或任何实现为迭代器的对象。显示的元素首先被序列化和转义。



#### `while`

您可以使用 while 关键字创建循环：

```
{{k = 3}}
<ul>
{{while k > 0:}}<li>{{=k}}{{k = k - 1}}</li>{{pass}}
</ul>
```

产生：

```
<ul>
<li>3</li>
<li>2</li>
<li>1</li>
</ul>
```



#### `if...elif...else`





您可以使用条件子句：

```
{{
import random
k = random.randint(0, 100)
}}
<h2>
{{=k}}
{{if k % 2:}}is odd{{else:}}is even{{pass}}
</h2>
```

产生：

```
<h2>
45 is odd
</h2>
```

由于很明显`else`关闭了第一个`if`块，因此不需要`pass`语句，使用一个将是不正确的。但是，您必须使用 . 显式关闭`else`块`pass`。

回想一下，在 Python 中，“else if”的写法`elif`如下例所示：

```
{{
import random
k = random.randint(0, 100)
}}
<h2>
{{=k}}
{{if k % 4 == 0:}}is divisible by 4
{{elif k % 2 == 0:}}is even
{{else:}}is odd
{{pass}}
</h2>
```

它产生：

```
<h2>
64 is divisible by 4
</h2>
```



#### `try...except...else...finally`





也可以`try...except`在带有一个警告的视图中使用语句。考虑以下示例：

```
{{try:}}
Hello {{= 1 / 0}}
{{except:}}
division by zero
{{else:}}
no division by zero
{{finally:}}
<br />
{{pass}}
```

它将产生以下输出：

```
Hello division by zero
<br />
```

此示例说明了在异常发生之前生成的所有输出（包括异常之前的输出）都在 try 块内呈现。写“Hello”是因为它在异常之前。



#### `def...return`





web2py 模板语言允许开发人员定义和实现可以返回任何 Python 对象或文本/html 字符串的函数。这里我们考虑两个例子：

```
{{def itemize1(link): return LI(A(link, _href="http://" + link))}}
<ul>
{{=itemize1('www.google.com')}}
</ul>
```

产生以下输出：

```
<ul>
<li><a href="http:/www.google.com">www.google.com</a></li>
</ul>
```

该函数`itemize1`返回一个辅助对象，该对象插入到调用该函数的位置。

现在考虑以下代码：

```
{{def itemize2(link):}}
<li><a href="http://{{=link}}">{{=link}}</a></li>
{{return}}
<ul>
{{itemize2('www.google.com')}}
</ul>
```

它产生与上面完全相同的输出。在这种情况下，该函数`itemize2`代表一段 HTML，它将替换调用该函数的 web2py 标记。请注意，对 的调用前面没有“=” `itemize2`，因为该函数不返回文本，而是将其直接写入响应中。

有一个警告：在视图中定义的函数必须以`return`语句结束，否则自动缩进将失败。



### HTML 助手

在视图中考虑以下代码：

```
{{=DIV('this', 'is', 'a', 'test', _id='123', _class='myclass')}}
```

它呈现为：

```
<div id="123" class="myclass">thisisatest</div>
```

`DIV`是一个辅助类，即可以用来以编程方式构建 HTML 的东西。它对应于 HTML`<div>`标签。

位置参数被解释为包含在打开和关闭标记之间的对象。以下划线开头的命名参数被解释为 HTML 标记属性（不带下划线）。一些助手还命名了不以下划线开头的参数；这些参数是特定于标签的。

除了一组未命名的参数之外，帮助程序还可以使用符号将单个列表或元组作为其组件集，`*`并且可以使用 将单个字典作为其属性集`**`，例如：

```
{{
contents = ['this', 'is', 'a', 'test']
attributes = {'_id':'123', '_class':'myclass'}
=DIV(*contents, **attributes)
}}
```

（产生与以前相同的输出）。

以下一组助手：

```
A`, `ASSIGNJS`, `B`, `BEAUTIFY`, `BODY`, `BR`, `CAT`, `CENTER`, `CODE`, `COL`, `COLGROUP`, `DIV`, `EM`, `EMBED`, `FIELDSET`, `FORM`, `H1`, `H2`, `H3`, `H4`, `H5`, `H6`, `HEAD`, `HR`, `HTML`, `I`, `IFRAME`, `IMG`, `INPUT`, `LABEL`, `LEGEND`, `LI`, `LINK`, `MARKMIN`, `MENU`, `META`, `OBJECT`, `ON`, `OL`, `OPTGROUP`, `OPTION`, `P`, `PRE`, `SCRIPT`, `SELECT`, `SPAN`, `STYLE`, `TABLE`, `TAG`, `TBODY`, `TD`, `TEXTAREA`, `TFOOT`, `TH`, `THEAD`, `TITLE`, `TR`, `TT`, `UL`, `URL`, `XHTML`, `XML`, `embed64`, `xmlescape
```

可用于构建复杂的表达式，然后可以将其序列化为 XML [ [xml-w](http://web2py.com/books/default/reference/29/xml-w) ] [ [xml-o](http://web2py.com/books/default/reference/29/xml-o) ]。例如：

```
{{=DIV(B(I("hello ", "<world>")), _class="myclass")}}
```

呈现：

```
<div class="myclass"><b><i>hello &lt;world&gt;</i></b></div>
```

Helpers 也可以被序列化为字符串，等效地，使用`__str__`和`xml`方法：

```
>>> print str(DIV("hello world"))
<div>hello world</div>
>>> print DIV("hello world").xml()
<div>hello world</div>
```

web2py 中的助手机制不仅仅是一个无需连接字符串即可生成 HTML 的系统。它提供了文档对象模型 (DOM) 的服务器端表示。

帮助器的组件可以通过它们的位置来引用，并且帮助器就其组件而言充当列表：

```
>>> a = DIV(SPAN('a', 'b'), 'c')
>>> print a
<div><span>ab</span>c</div>
>>> del a[1]
>>> a.append(B('x'))
>>> a[0][0] = 'y'
>>> print a
<div><span>yb</span><b>x</b></div>
```

助手的属性可以通过名称来引用，助手就其属性充当字典：

```
>>> a = DIV(SPAN('a', 'b'), 'c')
>>> a['_class'] = 's'
>>> a[0]['_class'] = 't'
>>> print a
<div class="s"><span class="t">ab</span>c</div>
```

请注意，可以通过名为 的列表访问完整的组件集`a.components`，并且可以通过名为 的字典访问完整的属性集`a.attributes`。所以，`a[i]`相当于`a.components[i]`when`i`是一个整数，又`a[s]`相当于`a.attributes[s]`when`s`是一个字符串。

请注意，助手属性作为关键字参数传递给助手。然而，在某些情况下，属性名称包含 Python 标识符中不允许的特殊字符（例如，连字符），因此不能用作关键字参数名称。例如：

```
DIV('text', _data-role='collapsible')
```

将不起作用，因为“_data-role”包含连字符，这将产生 Python 语法错误。

在这种情况下，您有几个选择。您可以使用`data`参数（这次没有前导下划线）传递相关属性的字典，而不使用前导连字符，并且输出将具有所需的组合，例如

```
>>> print DIV('text', data={'role': 'collapsible'})
<div data-role="collapsible">text</div>
```

或者您可以将属性作为字典传递并使用 Python 的`**`函数参数表示法，它将 (key:value) 对的字典映射到一组关键字参数：

```
>>> print DIV('text', **{'_data-role': 'collapsible'})
<div data-role="collapsible">text</div>
```

请注意，更详细的条目将引入 HTML 字符实体，但它们仍然可以工作，例如

```
>>> print DIV('text', data={'options':'{"mode":"calbox", "useNewStyle":true}'})
<div data-options="{&quot;mode&quot;:&quot;calbox&quot;, &quot;useNewStyle&quot;:true}">text</div>
```

您还可以动态创建特殊标签：

```
>>> print TAG['soap:Body']('whatever', **{'_xmlns:m':'http://www.example.org'})
<soap:Body xmlns:m="http://www.example.org">whatever</soap:Body>
```



#### `XML`

`XML`是用于封装不应转义的文本的对象。文本可能包含也可能不包含有效的 XML。例如，它可能包含 JavaScript。

此示例中的文本已转义：

```
>>> print DIV("<b>hello</b>")
<div>&lt;b&gt;hello&lt;/b&gt;</div>
```

通过使用`XML`，您可以防止转义：

```
>>> print DIV(XML("<b>hello</b>"))
<div><b>hello</b></div>
```

有时您想渲染存储在变量中的 HTML，但 HTML 可能包含不安全的标签，例如脚本：

```
>>> print XML('<script>alert("unsafe!")</script>')
<script>alert("unsafe!")</script>
```

诸如此类的未转义的可执行输入（例如，在博客的评论正文中输入）是不安全的，因为它可用于生成针对页面的其他访问者的跨站脚本 (XSS) 攻击。

web2py`XML`助手可以清理我们的文本以防止注入并转义除您明确允许的标签之外的所有标签。这是一个例子：

```
>>> print XML('<script>alert("unsafe!")</script>', sanitize=True)
&lt;script&gt;alert(&quot;unsafe!&quot;)&lt;/script&gt;
```

默认情况下`XML`，构造函数认为某些标签的内容和它们的某些属性是安全的。`permitted_tags`您可以使用可选参数和参数覆盖默认值`allowed_attributes`。以下是帮助程序的可选参数的默认值`XML`。

```
XML(text, sanitize=False,
    permitted_tags=['a', 'b', 'blockquote', 'br/', 'i', 'li',
       'ol', 'ul', 'p', 'cite', 'code', 'pre', 'img/'],
    allowed_attributes={'a':['href', 'title'],
       'img':['src', 'alt'], 'blockquote':['type']})
```



### 内置助手



#### `A`

该助手用于建立链接。

```
>>> print A('<click>', XML('<b>me</b>'),
            _href='http://www.web2py.com')
<a href='http://www.web2py.com'>&lt;click&gt;<b>me</b></a>
```

而不是`_href`您可以使用参数传递 URL `callback`。例如在一个视图中：

```
{{=A('click me', callback=URL('myaction'))}}
```

并且按下链接的效果将是对“myaction”的ajax调用而不是重定向。在这种情况下，您可以选择指定另外两个参数：`target`和`delete`：

```
{{=A('click me', callback=URL('myaction'), target='t')}}
<div id="t"><div>
```

并且 ajax 回调的响应将替换 id 等于“t”的 DIV 的内容（内部 HTML）。

```
<div id="b">{{=A('click me', callback=URL('myaction'), delete='div#b')}}</div>
```

并在响应后，将删除与“div#b”匹配的最接近的标签。在这种情况下，带有链接的整个 DIV 将被删除。

一个典型的应用是：

```
{{=A('click me', callback=URL('myaction'), delete='tr')}}
```

在一张桌子上。使用该链接将执行回调并删除表格行。

`target`并且`delete`可以组合。

A 助手接受一个名为 的特殊参数`cid`。它的工作原理如下：

```
{{=A('linked page', _href='http://example.com', cid='myid')}}
<div id="myid"></div>
```

单击链接会导致将内容加载到 div 中。这与上述语法类似但更强大，因为它旨在刷新页面组件。我们将在[第 12 章](http://web2py.com/books/default/chapter/29/12#trapped_ajax_links)`cid`中更详细地讨论组件的应用。

这些 ajax 功能需要 jQuery 和“static/js/web2py_ajax.js”，它们通过放置自动包含

```
{{include 'web2py_ajax.html'}}
```

在布局头中。“views/web2py_ajax.html”定义了一些基于`request`并包含所有必要的 js 和 css 文件的变量。





#### `ASSIGNJS`

ASSIGNJS 允许将服务器端值用作客户端 javascript 值。

例如，如果您在控制器中编写

```
return dict(stra='abcd', obj=alist)
```

并在视图中写

```
<script>
{{=ASSIGNJS(o=obj)}}
...
```

然后 javascript 变量 o 就会有传入 obj 的值，也就是 alist。

下面的[Javascript In Views](http://web2py.com/books/default/chapter/29/05/the-views#Javascriptinviews)下显示了另一个示例。



#### `B`

这个助手使它的内容加粗。

```
>>> print B('<hello>', XML('<i>world</i>'), _class='test', _id=0)
<b id="0" class="test">&lt;hello&gt;<i>world</i></b>
```



#### `BODY`

这个助手制作页面的主体。

```
>>> print BODY('<hello>', XML('<b>world</b>'), _bgcolor='red')
<body bgcolor="red">&lt;hello&gt;<b>world</b></body>
```



#### `BR`

这个助手创建一个换行符。

```
>>> print BR()
<br />
```

请注意，可以使用乘法运算符重复助手：

```
>>> print BR()*5
<br /><br /><br /><br /><br />
```



#### `CAT`

此助手连接其他助手，与 TAG[''] 相同。

```
>>> print CAT('Here is a ', A('link', _href=URL()), ', and here is some ', B('bold text'), '.')
Here is a <a href="/app/default/index">link</a>, and here is some <b>bold text</b>.
```



#### `CENTER`

该助手将其内容居中。

```
>>> print CENTER('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<center id="0" class="test">&lt;hello&gt;<b>world</b></center>
```



#### `CODE`

这个帮助器为 Python、C、C++、HTML 和 web2py 代码执行语法高亮，并且比`PRE`代码列表更可取。`CODE`还可以创建指向 web2py API 文档的链接。

这是一个突出显示 Python 代码部分的示例。

```
>>> print CODE('print "hello"', language='python').xml()
```



```
<table><tr style="vertical-align:top;">
  <td style="min-width:40px; text-align: right;"><pre style="
        font-size: 11px;
        font-family: Bitstream Vera Sans Mono,monospace;
        background-color: transparent;
        margin: 0;
        padding: 5px;
        border: none;
        color: #A0A0A0;
    ">1.</pre></td><td><pre style="
        font-size: 11px;
        font-family: Bitstream Vera Sans Mono,monospace;
        background-color: transparent;
        margin: 0;
        padding: 5px;
        border: none;
        overflow: auto;
        white-space: pre !important;
"><span style="color:#185369; font-weight: bold">print </span>
  <span style="color: #FF9966">"hello"</span></pre></td></tr></table>
```

这是 HTML 的类似示例

```
>>> print CODE('<html><body>{{=request.env.remote_add}}</body></html>',
...     language='html')
```

```
<table>...<code>...
<html><body>{{=request.env.remote_add}}</body></html>
...</code>...</table>
```

这些是`CODE`帮助程序的默认参数：

```
CODE("print 'hello world'", language='python', link=None, counter=1, styles={})
```

支持的`language`参数值为“python”、“html_plain”、“c”、“cpp”、“web2py”和“html”。“html”语言将 {{ 和 }} 标签解释为“web2py”代码，而“html_plain”则没有。

如果`link`指定了一个值，例如“/examples/global/vars/”，则代码中的 web2py API 引用将链接到链接 URL 处的文档。例如，“请求”将链接到“/examples/global/vars/request”。在上面的示例中，链接 URL 由“global.py”控制器中的“vars”操作处理，该控制器作为 web2py“示例”应用程序的一部分分发。

该`counter`参数用于行号。它可以设置为三个不同值中的任何一个。它可以是`None`无行号、指定起始编号的数值或字符串。如果计数器设置为字符串，则将其解释为提示，并且没有行号。

`styles`争论有点棘手。如果您查看上面生成的 HTML，它包含一个包含两列的表格，并且每一列都有自己的样式，使用 CSS 内联声明。这些`styles`属性允许您覆盖这两种 CSS 样式。例如：

```
CODE(..., styles={'CODE':'margin: 0;padding: 5px;border: none;'})
```

该`styles`属性必须是一个字典，并且它允许两个可能的键：`CODE`用于实际代码的样式，以及`LINENUMBERS`用于包含行号的左列的样式。请注意，这些样式完全取代了默认样式，而不是简单地添加到其中。



#### `COL`

```
>>> print COL('a', 'b')
<col>ab</col>
```



#### `COLGROUP`

```
>>> print COLGROUP('a', 'b')
<colgroup>ab</colgroup>
```



#### `DIV`

除此以外的所有助手`XML`都派生`DIV`并继承其基本方法。

```
>>> print DIV('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<div id="0" class="test">&lt;hello&gt;<b>world</b></div>
```



#### `EM`

强调其内容。

```
>>> print EM('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<em id="0" class="test">&lt;hello&gt;<b>world</b></em>
```



#### `FIELDSET`

这用于创建一个输入字段及其标签。

```
>>> print FIELDSET('Height:', INPUT(_name='height'), _class='test')
<fieldset class="test">Height:<input name="height" /></fieldset>
```



#### `FORM`

这是最重要的帮手之一。在其简单的形式中，它只是创建一个`<form>...</form>`标签，但因为助手是对象并且知道它们包含的内容，所以它们可以处理提交的表单（例如，执行字段验证）。这将在第 7 章中详细讨论。

```
>>> print FORM(INPUT(_type='submit'), _action='', _method='post')
<form enctype="multipart/form-data" action="" method="post">
<input type="submit" /></form>
```

默认情况下，“enctype”是“multipart/form-data”。

`FORM`a和 of的构造函数`SQLFORM`也可以采用称为 的特殊参数`hidden`。当字典作为 传递时`hidden`，它的项目被翻译成“隐藏的” INPUT 字段。例如：

```
>>> print FORM(hidden=dict(a='b'))
<form enctype="multipart/form-data" action="" method="post">
<input value="b" type="hidden" name="a" /></form>
```



#### `H1`, `H2`, `H3`, `H4`, `H5`, `H6`

这些助手用于段落标题和副标题：

```
>>> print H1('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<h1 id="0" class="test">&lt;hello&gt;<b>world</b></h1>
```



#### `HEAD`

用于标记 HTML 页面的 HEAD。

```
>>> print HEAD(TITLE('<hello>', XML('<b>world</b>')))
<head><title>&lt;hello&gt;<b>world</b></title></head>
```



#### `HTML`





这个助手有点不同。除了制作`<html>`标签之外，它还在标签前面加上一个 doctype 字符串[ [xhtml-w,xhtml-o,xhtml-school](http://web2py.com/books/default/reference/29/xhtml-w%2Cxhtml-o%2Cxhtml-school) ]。

```
>>> print HTML(BODY('<hello>', XML('<b>world</b>')))
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html><body>&lt;hello&gt;<b>world</b></body></html>
```

HTML 助手还接受一些额外的可选参数，这些参数具有以下默认值：

```
HTML(..., lang='en', doctype='transitional')
```

其中 doctype 可以是“strict”、“transitional”、“frameset”、“html5”或完整的 doctype 字符串。



#### `XHTML`

XHTML 类似于 HTML，但它创建了一个 XHTML 文档类型。

```
XHTML(..., lang='en', doctype='transitional', xmlns='http://www.w3.org/1999/xhtml')
```

其中 doctype 可以是“strict”、“transitional”、“frameset”或完整的 doctype 字符串。



#### `HR`

这个助手在 HTML 页面中创建一条水平线

```
>>> print HR()
<hr />
```



#### `I`

该助手将其内容变为斜体。

```
>>> print I('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<i id="0" class="test">&lt;hello&gt;<b>world</b></i>
```



#### `IFRAME`

此帮助程序在当前页面中包含另一个网页。另一个页面的 url 通过“_src”属性指定。

```
>>> print IFRAME(_src='http://www.web2py.com')
<iframe src="http://www.web2py.com"></iframe>
```



#### `IMG`

它可用于将图像嵌入 HTML：

```
>>> print IMG(_src='http://example.com/image.png', _alt='test')
<img src="http://example.com/image.ong" alt="rest" />
```

下面是 A、IMG 和 URL 助手的组合，用于包含带有链接的静态图像：

```
>>> print A(IMG(_src=URL('static', 'logo.png'), _alt="My Logo"),
...   _href=URL('default', 'index'))
... 
<a href="/myapp/default/index">
  <img src="/myapp/static/logo.png" alt="My Logo" />
</a>
```



#### `INPUT`

创建一个`<input.../>`标签。输入标签可能不包含其他标签，并且由`/>`而不是关闭`>`。输入标签有一个可选属性`_type`，可以设置为“text”（默认）、“submit”、“checkbox”或“radio”。

```
>>> print INPUT(_name='test', _value='a')
<input value="a" name="test" />
```

它还接受一个名为“value”的可选特殊参数，与“_value”不同。后者设置输入字段的默认值；前者设置其当前值。对于“文本”类型的输入，前者会覆盖后者：

```
>>> print INPUT(_name='test', _value='a', value='b')
<input value="b" name="test" />
```

对于单选按钮，`INPUT`有选择地设置“checked”属性：

```
>>> for v in ['a', 'b', 'c']:
...     print INPUT(_type='radio', _name='test', _value=v, value='b'), v
... 
<input value="a" type="radio" name="test" /> a
<input value="b" type="radio" checked="checked" name="test" /> b
<input value="c" type="radio" name="test" /> c
```

同样对于复选框：

```
>>> print INPUT(_type='checkbox', _name='test', _value='a', value=True)
<input value="a" type="checkbox" checked="checked" name="test" />
>>> print INPUT(_type='checkbox', _name='test', _value='a', value=False)
<input value="a" type="checkbox" name="test" />
```



#### `LABEL`

它用于为 INPUT 字段创建 LABEL 标签。

```
>>> print LABEL('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<label id="0" class="test">&lt;hello&gt;<b>world</b></label>
```



#### `LEGEND`

它用于为表单中的字段创建图例标签。

```
>>> print LEGEND('Name', _for='myfield')
<legend for="myfield">Name</legend>
```



#### `LI`

它是一个列表项，应该包含在一个`UL`or`OL`标记中。

```
>>> print LI('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<li id="0" class="test">&lt;hello&gt;<b>world</b></li>
```



#### `META`

用于在头部构建`META`标签。`HTML`例如：

```
>>> print META(_name='security', _content='high')
<meta name="security" content="high" />
```





#### `MARKMIN`

实现 markmin wiki 语法。它根据以下示例中描述的 markmin 规则将输入文本转换为输出 html：

```
>>> print MARKMIN("this is **bold** or ''italic'' and this [[a link http://web2py.com]]")
<p>this is <b>bold</b> or <i>italic</i> and
this <a href="http://web2py.com">a link</a></p>
```

markmin 语法在web2py 附带的[gluon/contrib/markmin/markmin.html](http://web2py.com/examples/static/markmin.html)中描述。

您可以使用 markmin 生成 HTML、LaTeX 和 PDF 文档：

```
m = "Hello **world** [[link http://web2py.com]]"
from gluon.contrib.markmin.markmin2html import markmin2html
print markmin2html(m)
from gluon.contrib.markmin.markmin2latex import markmin2latex
print markmin2latex(m)
from gluon.contrib.markmin.markmin2pdf import markmin2pdf
print markmin2pdf(m) # requires pdflatex
```

（`MARKMIN`助手是 的快捷方式`markmin2html`）

这是一个基本的语法入门：

| **来源**                    | **输出**                                                     |
| --------------------------- | ------------------------------------------------------------ |
| `# title`                   | **标题**                                                     |
| `## section`                | **部分**                                                     |
| `### subsection`            | **小节**                                                     |
| `**bold**`                  | **胆大**                                                     |
| `''italic''`                | *斜体*                                                       |
| ```verbatim```              | `verbatim`                                                   |
| `http://google.com`         | [http://google.com](http://google.com/)                      |
| `http://...`                | `<a href="http://...">http:...</a>`                          |
| `http://...png`             | `<img src="http://...png" />`                                |
| `http://...mp3`             | `<audio src="http://...mp3"></audio>`                        |
| `http://...mp4`             | `<video src="http://...mp4"></video>`                        |
| `qr:http://...`             | `<a href="http://..."><img src="qr code"/></a>`              |
| `embed:http://...`          | `<iframe src="http://..."></iframe>`                         |
| `[[click me #myanchor]]`    | [点我](http://web2py.com/books/default/chapter/29/05/the-views#myanchor) |
| `[[myanchor]]`              | 为链接创建锚点                                               |
| `$``$\int_a^b sin(x)dx$``$` | ![img](http://chart.apis.google.com/chart?cht=tx&chl=%5Cint_a%5Eb%20sin%28x%29dx) |

##### MARKMIN 链接

链接采用这种形式：

```
[[link display text <link>]]
```

其中 <link> 可以是：

- 一个锚（例如`#myanchor`），
- 一个 URI（例如`http://www.web2py.com`），或
- 相对引用（如 in`[[See Chapter 8 ../08]]`或`[[See Chapter 8 ../08#myanchor]]`）。

简单地包含指向图像、视频或音频文件的链接而没有标记会导致自动包含相应的图像、视频或音频文件（对于音频和视频，它使用 html <audio> 和 <video> 标签）。

添加带有`qr:`前缀的链接，例如

```
qr:http://web2py.com
```

导致嵌入相应的 QR 码并链接所述 URL。

添加带有`embed:`前缀的链接，例如

```
embed:http://www.youtube.com/embed/x1w8hKTJ2Co
```

导致页面被嵌入，在这种情况下嵌入了 youtube 视频。

图像也可以使用以下语法嵌入：

```
[[image-description http://.../image.png right 200px]]
```

##### MARKMIN 列表和表格

无序列表：

```
- one
- two
- three
```

有序列表：

```
+ one
+ two
+ three
```

和表格：

```
----------
 X | 0 | 0
 0 | X | 0
 0 | 0 | 1
----------
```

##### 扩展 MARKMIN

MARKMIN 语法还支持块引用、HTML5 音频和视频标签、图像对齐、自定义 css，并且可以扩展：

```
MARKMIN("``abab``:custom", extra=dict(custom=lambda text: text.replace('a', 'c'))
```

生成

```
'cbcb'
```

\```...``:<key>`自定义块由MARKMIN 的额外字典参数中作为相应键的值传递的函数分隔并呈现。请注意，该函数可能需要转义输出以防止 XSS。



#### `OBJECT`

用于在 HTML 中嵌入对象（例如，Flash 播放器）。

```
>>> print OBJECT('<hello>', XML('<b>world</b>'), _src='http://www.web2py.com')
<object src="http://www.web2py.com">&lt;hello&gt;<b>world</b></object>
```



#### `OL`

它代表有序列表。该列表应包含 LI 标签。`OL`不是`LI`对象的参数会自动包含在`<li>...</li>`标签中。

```
>>> print OL('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<ol id="0" class="test"><li>&lt;hello&gt;</li><li><b>world</b></li></ol>
```



#### `ON`

这是为了向后兼容，它只是`True`. 它专门用于复选框并且不推荐使用，因为`True`它更 Pythonic。

```
>>> print INPUT(_type='checkbox', _name='test', _checked=ON)
<input checked="checked" type="checkbox" name="test" />
```



#### `OPTGROUP`

允许您在 SELECT 中对多个选项进行分组，并且使用 CSS 自定义字段很有用。

```
>>> print SELECT('a', OPTGROUP('b', 'c'))
<select>
<option value="a">a</option>
<optgroup>
<option value="b">b</option>
<option value="c">c</option>
</optgroup>
</select>
```



#### `OPTION`

这应该只用作`SELECT`/`OPTION`组合的一部分。

```
>>> print OPTION('<hello>', XML('<b>world</b>'), _value='a')
<option value="a">&lt;hello&gt;<b>world</b></option>
```

与 的情况一样`INPUT`，web2py 区分“_value”（OPTION 的值）和“value”（封闭选择的当前值）。如果它们相等，则该选项被“选中”。

```
>>> print SELECT('a', 'b', value='b'):
<select>
<option value="a">a</option>
<option value="b" selected="selected">b</option>
</select>
```



#### `P`

这是用于标记段落。

```
>>> print P('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<p id="0" class="test">&lt;hello&gt;<b>world</b></p>
```



#### `PRE`

生成`<pre>...</pre>`用于显示预格式化文本的标签。帮助器`CODE`通常更适合于代码清单。

```
>>> print PRE('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<pre id="0" class="test">&lt;hello&gt;<b>world</b></pre>
```



#### `SCRIPT`

这是包含或链接脚本，例如 JavaScript。标记之间的内容被呈现为 HTML 注释，以使真正的旧浏览器受益。

```
>>> print  SCRIPT ( 'alert("hello world");' ,  _type = 'text/javascript' ) 
<script type="text/javascript"><!-- 
alert("hello world"); 
//--></script>
```



#### `SELECT`

做一个`<select>...</select>`标签。这与`OPTION`助手一起使用。那些`SELECT`不是`OPTION`对象的参数会自动转换为选项。

```
>>> print SELECT('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<select id="0" class="test">
<option value="&lt;hello&gt;">&lt;hello&gt;</option>
<option value="&lt;b&gt;world&lt;/b&gt;"><b>world</b></option>
</select>
```



#### `SPAN`

类似于`DIV`但用于标记内联（而不是块）内容。

```
>>> print SPAN('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<span id="0" class="test">&lt;hello&gt;<b>world</b></span>
```



#### `STYLE`

类似于脚本，但用于包含或链接 CSS 代码。这里包括 CSS：

```
>>> print STYLE(XML('body {color: white}'))
<style><!--
body { color: white }
//--></style>
```

这里是链接的：

```
>>> print STYLE(_src='style.css')
<style src="style.css"><!--
//--></style>
```



#### `TABLE`, `TR`, `TD`





这些标签（连同可选的`THEAD`,`TBODY`和`TFOOTER`助手）用于构建 HTML 表格。

```
>>> print TABLE(TR(TD('a'), TD('b')), TR(TD('c'), TD('d')))
<table><tr><td>a</td><td>b</td></tr><tr><td>c</td><td>d</td></tr></table>
```

`TR`期待`TD`内容；不是`TD`对象的参数会自动转换。

```
>>> print TABLE(TR('a', 'b'), TR('c', 'd'))
<table><tr><td>a</td><td>b</td></tr><tr><td>c</td><td>d</td></tr></table>
```

使用 Python 的函数参数表示法很容易将 Python 数组转换为 HTML 表`*`，它将列表元素映射到位置函数参数。

在这里，我们将逐行进行：

```
>>> table = [['a', 'b'], ['c', 'd']]
>>> print TABLE(TR(*table[0]), TR(*table[1]))
<table><tr><td>a</td><td>b</td></tr><tr><td>c</td><td>d</td></tr></table>
```

在这里，我们一次完成所有行：

```
>>> table = [['a', 'b'], ['c', 'd']]
>>> print TABLE(*[TR(*rows) for rows in table])
<table><tr><td>a</td><td>b</td></tr><tr><td>c</td><td>d</td></tr></table>
```



#### `TBODY`

这用于标记表格正文中包含的行，而不是页眉或页脚行。它是可选的。

```
>>> print TBODY(TR('<hello>'), _class='test', _id=0)
<tbody id="0" class="test"><tr><td>&lt;hello&gt;</td></tr></tbody>
```



#### `TEXTAREA`

这个助手制作一个`<textarea>...</textarea>`标签。

```
>>> print TEXTAREA('<hello>', XML('<b>world</b>'), _class='test')
<textarea class="test" cols="40" rows="10">&lt;hello&gt;<b>world</b></textarea>
```

唯一需要注意的是它的可选“值”覆盖了它的内容（内部 HTML）

```
>>> print TEXTAREA(value="<hello world>", _class="test")
<textarea class="test" cols="40" rows="10">&lt;hello world&gt;</textarea>
```



#### `TFOOT`

这用于标记表格页脚行。

```
>>> print TFOOT(TR(TD('<hello>')), _class='test', _id=0)
<tfoot id="0" class="test"><tr><td>&lt;hello&gt;</td></tr></tfoot>
```



#### `TH`

这用于代替`TD`表头。

```
>>> print TH('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<th id="0" class="test">&lt;hello&gt;<b>world</b></th>
```



#### `THEAD`

这用于标记表标题行。

```
>>> print THEAD(TR(TH('<hello>')), _class='test', _id=0)
<thead id="0" class="test"><tr><th>&lt;hello&gt;</th></tr></thead>
```



#### `TITLE`

这用于在 HTML 标头中标记页面的标题。

```
>>> print TITLE('<hello>', XML('<b>world</b>'))
<title>&lt;hello&gt;<b>world</b></title>
```



#### `TR`

标记表格行。它应该在表格内呈现并包含`<td>...</td>`标签。`TR`不是`TD`对象的参数将被自动转换。

```
>>> print TR('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<tr id="0" class="test"><td>&lt;hello&gt;</td><td><b>world</b></td></tr>
```



#### `TT`

将文本标记为打字机（等宽）文本。

```
>>> print TT('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<tt id="0" class="test">&lt;hello&gt;<b>world</b></tt>
```



#### `UL`

表示无序列表并应包含`LI`项目。如果其内容未标记为`LI`，`UL`则自动执行。

```
>>> print UL('<hello>', XML('<b>world</b>'), _class='test', _id=0)
<ul id="0" class="test"><li>&lt;hello&gt;</li><li><b>world</b></li></ul>
```



#### `URL`

URL helper详见在[第 4 章 URL](http://web2py.com/books/default/chapter/29/04#URL)



#### `embed64`

```
embed64(filename=None, file=None, data=None, extension='image/gif')
```

将提供的（二进制）数据编码为 base64，它采用以下可选参数：

- `filename`: 如果提供，则以 'rb' 模式打开并读取此文件。
- `file`: 如果提供，则读取此文件。
- `data`：如果提供，则使用提供的数据。



#### `xmlescape`

```
xmlescape(data, quote=True)
```

返回所提供数据的转义字符串。

```
>>> print xmlescape('<hello>')
&lt;hello&gt;
```



### 自定义助手



#### `TAG`

有时您需要生成自定义 XML 标记。web2py 提供`TAG`了一个通用的标签生成器。

```
{{=TAG.name('a', 'b', _c='d')}}
```

生成以下 XML

```
<name c="d">ab</name>
```

参数“a”、“b”和“d”被自动转义；使用`XML`助手来抑制这种行为。使用`TAG`您可以生成 API 尚未提供的 HTML/XML 标记。TAG 可以嵌套，并使用序列化`str().`等效语法为：

```
{{=TAG['name']('a', 'b', c='d')}}
```

如果 TAG 对象是使用空名称创建的，它可以用于将多个字符串和 HTML 助手连接在一起，而无需将它们插入到周围的标签中，但这种用法已被弃用。请改用`CAT`助手。

可以使用 TAG 助手生成自闭合标签。标签名称必须以“/”结尾。

```
{{=TAG['link/'](_href='http://web2py.com')}}
```

生成以下 XML：

```
<link ref="http://web2py.com"/>
```

请注意，它`TAG`是一个对象，`TAG.name`或者`TAG['name']`是一个返回临时帮助类的函数。



#### `MENU`

MENU 帮助器采用列表形式的列表或元组列表`response.menu`（如第 4 章所述），并使用表示菜单的无序列表生成树状结构。例如：

```
>>> print MENU([['One', False, 'link1'], ['Two', False, 'link2']])
<ul class="web2py-menu web2py-menu-vertical">
<li><a href="link1">One</a></li>
<li><a href="link2">Two</a></li>
</ul>
```

> 每个列表/元组中的第一项是要为给定菜单项显示的文本。
>
> 每个列表/元组中的第二项是一个布尔值，指示该特定菜单项是否处于活动状态（即，当前选定的项）。当设置为 True 时，`MENU`助手将为该项目添加一个“web2py-menu-active”类`<li>`（您可以通过“li_active”参数将该类的名称更改为`MENU`）。指定活动 url 的另一种方法是`MENU`通过其“active_url”参数直接将其传递给。
>
> 每个列表/元组中的第三项可以是 HTML 帮助器（可能包括嵌套的帮助器），并且`MENU`帮助器将简单地呈现该帮助器而不是创建自己的`<a>`标记。

每个菜单项可以有第四个参数，它是一个嵌套的子菜单（以此类推）：

```
>>> print MENU([['One', False, 'link1', [['Two', False, 'link2']]]])
<ul class="web2py-menu web2py-menu-vertical">
<li class="web2py-menu-expand">
<a href="link1">One</a>
<ul class="web2py-menu-vertical">
<li><a href="link2">Two</a></li>
</ul>
</li>
</ul>
```

菜单项也可以有一个可选的第 5 个元素，它是一个布尔值。当为 false 时，菜单项将被 MENU 助手忽略。

帮助程序采用以下`MENU`可选参数：

- `_class`：默认为“web2py-menu web2py-menu-vertical”并设置外部 UL 元素的类。
- `ul_class`：默认为“web2py-menu-vertical”并设置内部 UL 元素的类。
- `li_class`：默认为“web2py-menu-expand”并设置内部 LI 元素的类。
- `li_first`: 允许向第一个列表元素添加一个类。
- `li_last`: 允许向最后一个列表元素添加一个类。

`MENU`接受一个可选参数`mobile`。当设置为`True`而不是构建递归`UL`菜单结构时，它会返回一个`SELECT`包含所有菜单选项的下拉菜单和一个`onchange`重定向到与所选选项对应的页面的属性。这是一种替代菜单表示形式，可提高手机等小型移动设备的可用性。

通常，菜单用于具有以下语法的布局中：

```
{{=MENU(response.menu, mobile=request.user_agent().is_mobile)}}
```

以这种方式，自动检测到移动设备并相应地呈现菜单。



### `BEAUTIFY`

`BEAUTIFY`用于构建复合对象的 HTML 表示，包括列表、元组和字典：

```
{{=BEAUTIFY({"a": ["hello", XML("world")], "b": (1, 2)})}}
```

`BEAUTIFY`返回一个可序列化为 XML 的类 XML 对象，其构造函数参数具有漂亮的外观。在这种情况下，XML 表示为：

```
{"a": ["hello", XML("world")], "b": (1, 2)}
```

将呈现为：

```
<table>
<tr><td>a</td><td>:</td><td>hello<br />world</td></tr>
<tr><td>b</td><td>:</td><td>1<br />2</td></tr>
</table>
```



### 服务器端*DOM*和解析



#### `elements`





DIV 帮助器和所有派生的帮助器提供搜索方法`element`和`elements`.

`element`返回匹配指定条件的第一个子元素（如果不匹配，则返回 None）。

`elements`返回所有匹配子项的列表。

**element**和**elements**使用相同的语法来指定匹配条件，这允许三种可以混合匹配的可能性：类 jQuery 表达式、通过精确属性值匹配、使用正则表达式匹配。

这是一个简单的例子：

```
>>> a = DIV(DIV(DIV('a', _id='target', _class='abc')))
>>> d = a.elements('div#target')
>>> d[0][0] = 'changed'
>>> print a
<div><div><div id="target" class="abc">changed</div></div></div>
```

的未命名参数`elements`是一个字符串，它可能包含：标签的名称、标签的 id 前面有一个井号、类前面有一个点、方括号中属性的显式值。

以下是通过 id 搜索前一个标签的 4 种等效方法：

```
d = a.elements('#target')
d = a.elements('div#target')
d = a.elements('div[id=target]')
d = a.elements('div', _id='target')
```

以下是按类别搜索前一个标签的 4 种等效方法：

```
d = a.elements('.abc')
d = a.elements('div.abc')
d = a.elements('div[class=abc]')
d = a.elements('div', _class='abc')
```

任何属性都可以用来定位一个元素（不仅仅是`id`and `class`），包括多个属性（函数元素可以接受多个命名参数），但只会返回第一个匹配的元素。

使用 jQuery 语法“div#target”可以指定多个搜索条件，用逗号分隔：

```
a = DIV(SPAN('a', _id='t1'), DIV('b', _class='c2'))
d = a.elements('span#t1, div.c2')
```

或等效地

```
a = DIV(SPAN('a', _id='t1'), DIV('b', _class='c2'))
d = a.elements('span#t1', 'div.c2')
```

如果使用名称参数指定属性的值，则它可以是字符串或正则表达式：

```
a = DIV(SPAN('a', _id='test123'), DIV('b', _class='c2'))
d = a.elements('span', _id=re.compile('test\d{3}')  
   
```

DIV（和派生的）助手的一个特殊命名参数是`find`. 它可用于在标签的文本内容中指定搜索值或搜索正则表达式。例如：

```
>>> a = DIV(SPAN('abcde'), DIV('fghij'))
>>> d = a.elements(find='bcd')
>>> print d[0]
<span>abcde</span>
```

或者

```
>>> a = DIV(SPAN('abcde'), DIV('fghij'))
>>> d = a.elements(find=re.compile('fg\w{3}'))
>>> print d[0]
<div>fghij</div>
```



#### `components`

这是一个列出 html 字符串中所有元素的示例：

```
>>> html = TAG('<a>xxx</a><b>yyy</b>')
>>> for item in html.components:
...     print item
... 
<a>xxx</a>
<b>yyy</b>
     
```



#### `parent`和`siblings`





`parent`返回当前元素的父元素。

```
>>> a = DIV(SPAN('a'), DIV('b'))
>>> s = a.element('span')
>>> d = s.parent
>>> d['_class']='abc'
>>> print a
<div class="abc"><span>a</span><div>b</div></div>
>>> for e in s.siblings(): print e
<div>b</div>  
```



#### 更换元素

匹配的元素也可以通过指定`replace`参数来替换或删除。请注意，仍然照常返回原始匹配元素的列表。

```
>>> a = DIV(SPAN('x'), DIV(SPAN('y'))
>>> b = a.elements('span', replace=P('z')
>>> print a
<div><p>z</p><div><p>z</p></div>
```

`replace`可以是可调用的。在这种情况下，它将传递原始元素，并期望返回替换元素：

```
>>> a = DIV(SPAN('x'), DIV(SPAN('y'))
>>> b = a.elements('span', replace=lambda t: P(t[0])
>>> print a
<div><p>x</p><div><p>y</p></div>
```

如果`replace=None`，匹配的元素将被完全删除。

```
>>> a = DIV(SPAN('x'), DIV(SPAN('y'))
>>> b = a.elements('span', replace=None)
>>> print a
<div></div>
```



#### `flatten`

flatten 方法递归地将给定元素的子元素的内容序列化为常规文本（不带标签）：

```
>>> a = DIV(SPAN('this', DIV('is', B('a'))), SPAN('test'))
>>> print a.flatten()
thisisatest
```

Flatten 可以传递一个可选参数，`render`即使用不同协议呈现/展平内容的函数。这是一个将一些标签序列化为 Markmin wiki 语法的示例：

```
>>> a = DIV(H1('title'), P('example of a ', A('link', _href='#test')))
>>> from gluon.html import markmin_serializer
>>> print a.flatten(render=markmin_serializer)
# titles

example of [[a link #test]]
```

在撰写本文时，我们提供`markmin_serializer`和`markdown_serializer`.



#### Parsing解析

TAG 对象也是一个 XML/HTML 解析器。它可以读取文本并转换为助手的树形结构。这允许使用上面的 API 进行操作：

```
>>> html = '<h1>Title</h1><p>this is a <span>test</span></p>'
>>> parsed_html = TAG(html)
>>> parsed_html.element('span')[0]='TEST'
>>> print parsed_html
<h1>Title</h1><p>this is a <span>TEST</span></p>
```



### 页面布局





视图可以扩展并包含树状结构中的其他视图。

例如，我们可以考虑扩展“layout.html”并包含“body.html”的视图“index.html”。同时，“layout.html”可能包括“header.html”和“footer.html”。

树的根是我们所说的布局视图。就像任何其他 HTML 模板文件一样，您可以使用 web2py 管理界面对其进行编辑。文件名“layout.html”只是一个约定。

这是一个扩展“layout.html”视图并包含“page.html”视图的极简页面：

```
{{extend 'layout.html'}}
<h1>Hello World</h1>
{{include 'page.html'}}
```

扩展布局文件必须包含一个`{{include}}`指令，例如：

```
<html>
  <head>
    <title>Page Title</title>
  </head>
  <body>
    {{include}}
  </body>
</html>
```

当视图被调用时，扩展（布局）视图被加载，调用视图替换`{{include}}`布局内的指令。`extend`处理以递归方式继续，直到处理完所有`include`指令。然后将生成的模板翻译成 Python 代码。请注意，当应用程序被字节码编译时，编译的是这个 Python 代码，而不是原始视图文件本身。因此，给定视图的字节码编译版本是单个 .pyc 文件，其中不仅包含原始视图文件的 Python 代码，还包含扩展和包含视图的整个树。

> `extend`, `include`,`block`和`super`是特殊的模板指令，而不是 Python 命令。

该指令之前的任何内容或代码`{{extend ...}}`都将插入（并因此执行）扩展视图的内容/代码的开头之前。尽管这通常不用于在扩展视图的内容之前插入实际的 HTML 内容，但它可以用作定义要使扩展视图可用的变量或函数的方法。例如，考虑一个视图“index.html”：

```
{{sidebar_enabled=True}}
{{extend 'layout.html'}}
<h1>Home Page</h1>
```

以及“layout.html”的摘录：

```
{{if sidebar_enabled:}}
    <div id="sidebar">
        Sidebar Content
    </div>
{{pass}}
```

因为`sidebar_enabled`"index.html" 中的赋值在 之前`extend`，所以该行被插入到 "layout.html" 的开头之前，`sidebar_enabled`在 "layout.html" 代码中的任何地方都可用（在**欢迎**应用程序）。

还值得指出的是，控制器函数返回的变量不仅在函数的主视图中可用，而且在其所有扩展视图和包含视图中也可用。

`extend`or的参数`include`（即扩展或包含的视图名称）可以是 Python 变量（尽管不是 Python 表达式）。但是，这有一个限制——在`extend`or`include`语句中使用变量的视图不能被字节码编译。如上所述，字节码编译的视图包括扩展视图和包含视图的整个树，因此在编译时必须知道具体的扩展视图和包含视图，如果视图名称是变量（其值直到运行才确定），这是不可能的时间）。因为字节码编译视图可以提供显着的速度提升，所以如果可能的话`extend`，`include`通常应该避免使用变量。

在某些情况下，在 an 中使用变量的替代方法`include`是将常规`{{include ...}}`指令放置在`if...else`块中。

```
{{if some_condition:}}
{{include 'this_view.html'}}
{{else:}}
{{include 'that_view.html'}}
{{pass}}
```

上面的代码对字节码编译没有任何问题，因为不涉及任何变量。但是请注意，字节码编译视图实际上将包含“this_view.html”和“that_view.html”的 Python 代码，尽管根据`some_condition`.

请记住，这仅适用于`include`- 您不能将`{{extend ...}}`指令放在`if...else`块内。





布局用于封装页面通用性（页眉、页脚、菜单），尽管它们不是强制性的，但它们将使您的应用程序更易于编写和维护。特别是，我们建议编写利用以下可在控制器中设置的变量的布局。使用这些众所周知的变量将有助于使您的布局可互换：

```
response.title
response.subtitle
response.meta.author
response.meta.keywords
response.meta.description
response.flash
response.menu
response.files
```

除了`menu`and `files`，这些都是字符串，它们的含义应该是显而易见的。

`response.menu`menu 是 3 元组或 4 元组的列表。这三个元素是：链接名称、表示链接是否处于活动状态（是当前链接）的布尔值以及链接页面的 URL。例如：

```
response.menu = [('Google', False, 'http://www.google.com', []),
                 ('Index',  True,  URL('index'), [])]
```

第四个元组元素是可选的子菜单。

`response.files`是您的页面所需的 CSS 和 JS 文件的列表。

我们还建议您使用：

```
{{include 'web2py_ajax.html'}}
```

在 HTML 头中，因为这将包含 jQuery 库并为特殊效果和 Ajax 定义一些向后兼容的 JavaScript 函数。“web2py_ajax.html”包括`response.meta`视图中的标签、jQuery 库、日历日期选择器以及所有必需的 CSS 和 JS `response.files`。



#### 默认页面布局

web2py 脚手架应用程序 Welcome 附带的“views/layout.html” （去掉了一些可选部分）**非常**复杂，但它具有以下结构：

```
<!DOCTYPE html>
<head>
  <meta charset="utf-8" />
  <title>{{=response.title or request.application}}</title>
  ...
  <script src="{{=URL('static', 'js/modernizr.custom.js')}}"></script>

  {{
  response.files.append(URL('static', 'css/web2py.css'))
  response.files.append(URL('static', 'css/bootstrap.min.css'))
  response.files.append(URL('static', 'css/bootstrap-responsive.min.css'))
  response.files.append(URL('static', 'css/web2py_bootstrap.css'))
  }}

  {{include 'web2py_ajax.html'}}

  {{
  # using sidebars need to know what sidebar you want to use
  left_sidebar_enabled = globals().get('left_sidebar_enabled', False)
  right_sidebar_enabled = globals().get('right_sidebar_enabled', False)
  middle_columns = {0:'span12', 1:'span9', 2:'span6'}[
    (left_sidebar_enabled and 1 or 0)+(right_sidebar_enabled and 1 or 0)]
  }}

  {{block head}}{{end}}
</head>

<body>
  <!-- Navbar ================================================== -->
  <div class="navbar navbar-inverse navbar-fixed-top">
    <div class="flash">{{=response.flash or ''}}</div>
    <div class="navbar-inner">
      <div class="container">
        {{=response.logo or ''}}
        <ul id="navbar" class="nav pull-right">
          {{='auth' in globals() and auth.navbar(mode="dropdown") or ''}}
        </ul>
        <div class="nav-collapse">
          {{if response.menu:}}
          {{=MENU(response.menu)}}
          {{pass}}
        </div><!--/.nav-collapse -->
      </div>
    </div>
  </div><!--/top navbar -->

  <div class="container">
    <!-- Masthead ================================================== -->
    <header class="mastheader row" id="header">
        <div class="span12">
            <div class="page-header">
                <h1>
                    {{=response.title or request.application}}
                    <small>{{=response.subtitle or ''}}</small>
                </h1>
            </div>
        </div>
    </header>

    <section id="main" class="main row">
        {{if left_sidebar_enabled:}}
        <div class="span3 left-sidebar">
            {{block left_sidebar}}
            <h3>Left Sidebar</h3>
            <p></p>
            {{end}}
        </div>
        {{pass}}

        <div class="{{=middle_columns}}">
            {{block center}}
            {{include}}
            {{end}}
        </div>

        {{if right_sidebar_enabled:}}
        <div class="span3">
            {{block right_sidebar}}
            <h3>Right Sidebar</h3>
            <p></p>
            {{end}}
        </div>
        {{pass}}
    </section><!--/main-->

    <!-- Footer ================================================== -->
    <div class="row">
        <footer class="footer span12" id="footer">
            <div class="footer-content">
                {{block footer}} <!-- this is default footer -->
                ...
                {{end}}
            </div>
        </footer>
    </div>

  </div> <!-- /container -->

  <!-- The javascript =============================================
       (Placed at the end of the document so the pages load faster) -->
  <script src="{{=URL('static', 'js/bootstrap.min.js')}}"></script>
  <script src="{{=URL('static', 'js/web2py_bootstrap.js')}}"></script>
  {{if response.google_analytics_id:}}
    <script src="{{=URL('static', 'js/analytics.js')}}"></script>
    <script type="text/javascript">
    analytics.initialize({
      'Google Analytics':{trackingId:'{{=response.google_analytics_id}}'}
    });</script>
  {{pass}}
</body>
</html>
```

此默认布局的一些功能使其非常易于使用和自定义：

- 它是用 HTML5 编写的，并使用“modernizr” [ [modernizr](http://web2py.com/books/default/reference/29/modernizr) ]库来实现向后兼容性。实际布局包括 IE 所需的一些额外条件语句，为简洁起见将其省略。
- 它显示两者`response.title`，`response.subtitle`可以在模型或控制器中设置。如果未设置，则采用应用程序名称作为标题。
- 它`web2py_ajax.html`在生成所有链接和脚本导入语句的标题中包含该文件。
- 它使用 Twitter Bootstrap 的修改版本来实现灵活的布局，该布局适用于移动设备并重新排列列以适应小屏幕。
- 它使用“analytics.js”连接到 Google Analytics。
- 根据`{{=auth.navbar(...)}}`上下文显示对当前用户的欢迎并链接到登录、注销、注册、更改密码等身份验证功能。`auth.navbar`是一个助手工厂，它的输出可以像任何其他助手一样被操纵。它被放置在一个表达式中以检查 auth 定义，如果 auth 未定义，表达式的计算结果为 ''。
- 将`{{=MENU(response.menu)}}`菜单结构显示为`<ul>...</ul>`。
- `{{include}}`被渲染页面时被扩展视图的内容所取代。
- 默认情况下，它使用有条件的三列（可以通过扩展视图关闭左右侧边栏）
- 它使用以下类：页眉、主目录、页脚。
- 它包含以下块：head、left_sidebar、center、right_sidebar、footer。

在视图中，您可以打开和自定义侧边栏，如下所示：

```
{{left_sidebar_enabled=True}}
{{extend 'layout.html'}}

This text goes in center

{{block left_sidebar}}
This text goes in sidebar
{{end}}
```



#### 自定义默认布局

无需编辑即可自定义默认布局很容易，因为欢迎应用程序基于 Twitter Bootstrap，它有很好的文档并支持主题。在 web2py 中与样式相关的四个静态文件：

- "css/web2py.css" 包含 web2py 特定的样式
- "css/bootstrap.min.css" 包含 Twitter Bootstrap CSS 样式[ [bootstrap](http://web2py.com/books/default/reference/29/bootstrap) ]
- “css/web2py_bootstrap.css”覆盖了一些 Bootstrap 样式以符合 web2py 的需求。
- “js/bootstrap.min.js”，其中包括用于菜单效果、模式、面板的库。

要更改颜色和背景图像，请尝试将以下代码附加到 layout.html 标头：

```
<style>
body { background: url('images/background.png') repeat-x #3A3A3A; }
a { color: #349C01; }
.page-header h1 { color: #349C01; }
.page-header h2 { color: white; font-style: italic; font-size: 14px;}
.statusbar { background: #333333; border-bottom: 5px #349C01 solid; }
.statusbar a { color: white; }
.footer { border-top: 5px #349C01 solid; }
</style>
```

当然你也可以用你自己的完全替换“layout.html”和“web2py.css”文件。



#### 移动开发

尽管默认 layout.html 被设计为对移动设备友好，但当移动设备访问页面时，有时可能需要使用不同的视图。

为了使桌面和移动设备的开发更容易，web2py 包含了`@mobilize`装饰器。此装饰器应用于应具有普通视图和移动视图的操作。这在此处演示：

```
from gluon.contrib.user_agent_parser import mobilize
@mobilize
def index():
    return dict()
```

请注意，必须先导入装饰器，然后才能在控制器中使用它。当从常规浏览器（桌面计算机）调用“index”函数时，web2py 将使用视图“[controller]/index.html”呈现返回的字典。但是，当它被移动设备调用时，字典将由“[controller]/index.mobile.html”呈现。请注意，移动视图具有“mobile.html”扩展名。

或者，您可以应用以下逻辑使所有视图对移动设备友好：

```
if request.user_agent().is_mobile:
    response.view.replace('.html', '.mobile.html')
```

创建“*.mobile.html”视图的任务留给开发人员，但我们强烈建议使用“jQuery Mobile”插件，这使得任务变得非常简单。



### 视图中的函数

考虑这个“layout.html”：

```
<html>
  <body>
    {{include}}
    <div class="sidebar">
      {{if 'mysidebar' in globals():}}{{mysidebar()}}{{else:}}
        my default sidebar
      {{pass}}
    </div>
  </body>
</html>
```

和这个扩展视图

```
{{def mysidebar():}}
my new sidebar!!!
{{return}}
{{extend 'layout.html'}}
Hello World!!!
```

请注意，函数是在`{{extend...}}`语句之前定义的——这导致函数在“layout.html”代码执行之前创建，因此可以在“layout.html”中的任何位置调用该函数，甚至在`{{include}}`. 另请注意，该功能包含在没有`=`前缀的扩展视图中。

该代码生成以下输出：

```
<html>
  <body>
    Hello World!!!
    <div class="sidebar">
      my new sidebar!!!
    </div>
  </body>
</html>
```

请注意，该函数是在 HTML 中定义的（尽管它也可能包含 Python 代码），因此`response.write`用于编写其内容（该函数不返回内容）。`{{mysidebar()}}`这就是为什么布局使用而不是调用视图函数的原因`{{=mysidebar()}}`。以这种方式定义的函数可以接受参数。



### 视图中的块

使视图更加模块化的主要方法是使用`{{block ...}}`s，这种机制是上一节中讨论的机制的替代方案。

要了解这是如何工作的，请考虑基于脚手架应用程序 Welcome 的应用程序，该应用程序具有视图 layout.html。该视图通过视图`default/index.html`扩展`{{extend 'layout.html'}}`。layout.html 的内容预先定义了具有某些默认内容的某些块，因此这些块包含在 default/index.html 中。

您可以通过将新内容包含在相同的块名称中来覆盖这些默认内容块。layout.html 中块的位置没有改变，但内容改变了。

这是一个简化版本。想象这是“layout.html”：

```
<html>
  <body>
    {{include}}
    <div class="sidebar">
      {{block mysidebar}}
        my default sidebar (this content to be replaced)
      {{end}}
    </div>
  </body>
</html>
```

这是一个简单的扩展视图`default/index.html`：

```
{{extend 'layout.html'}}
Hello World!!!
{{block mysidebar}}
my new sidebar!!!
{{end}}
```

它生成以下输出，其中内容由扩展视图中的覆盖块提供，但封闭的 DIV 和类来自 layout.html。这允许视图之间的一致性：

```
<html>
  <body>
    Hello World!!!
    <div class="sidebar">
        my new sidebar!!!
    </div>
  </body>
</html>
```

真正的 layout.html 定义了许多有用的块，您可以轻松添加更多以匹配您想要的布局。

你可以有很多块，如果一个块存在于扩展视图中但不存在于扩展视图中，则使用扩展视图的内容。另外，请注意，与函数不同，不需要在`{{extend ...}}`-- 之前定义块，即使在之后定义`extend`，它们也可以用于在扩展视图中的任何位置进行替换。

在块内，您可以使用表达式`{{super}}`来包含父级的内容。例如，如果我们将上面的扩展视图替换为：

```
{{extend 'layout.html'}}
Hello World!!!
{{block mysidebar}}
{{super}}
my new sidebar!!!
{{end}}
```

我们得到：

```
<html>
  <body>
    Hello World!!!
    <div class="sidebar">
        my default sidebar
        my new sidebar!
    </div>
  </body>
</html>
```





### 视图中的 Javascript

帮助器可以在外部代码中使用，方法是将其放置在模板中，然后在需要的地方包含模板。例如，如果某些 javascript 代码在文件“/views/my.js”中，那么它可以包含在视图文件中：

```
<script>
{{include 'my.js'}}
</script>
```

但是，如果有很多行 javascript 代码但只有几行动态生成的 web2py 内容（例如帮助程序），则效率会很低。另一种方法是在模板中的一个 javascript 块中定义动态生成的 web2py 变量，然后加载一个仅引用这些变量的静态 javascript 文件（这就是“web2py_ajax.html”的工作方式——它定义了几个 JS 变量，然后由“web2py.js”使用）。所以，在视图文件中：

```
<script>
var someVar = "{{=T('some phrase to be translated')}}";
var someURL = "{{=URL('default', 'myfunction')}}";
</script>
<script src="{{=URL('static', 'js/my.js')}}"></script>
```

或等效地使用 web2py`ASSIGNJS`助手：

```
<script>
{{=ASSIGNJS(someVar = T('some phrase to be translated'),
            someURL = URL('default', 'myfunction'))}};
</script>
<script src="{{=URL('static', 'js/my.js')}}"></script>
```

然后在“my.js”中，`someVar`可以`someURL`作为普通的javascript变量使用。