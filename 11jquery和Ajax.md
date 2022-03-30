> ## jQuery 和 Ajax[¶](http://web2py.com/books/default/chapter/29/11/jquery-and-ajax#jQuery-and-Ajax)
>
> 虽然 web2py 主要用于服务器端开发，但**受欢迎**的脚手架应用程序带有基本的 jQuery 库[ [jquery](http://web2py.com/books/default/reference/29/jquery) ]、jQuery 日历（日期选择器、日期时间选择器和时钟），以及一些基于 jQuery 的附加 JavaScript 函数。
>
> web2py 中没有任何东西阻止您使用其他 Ajax 库，例如 Prototype、ExtJS 或 YUI，但我们决定打包 jQuery，因为我们发现它比其他等效库更易于使用且功能更强大。我们还发现它体现了 web2py 的功能性和简洁性。
>
> 
>
> ### web2py_ajax.html
>
> 脚手架 web2py 应用程序“欢迎”包括一个名为
>
> ```
> views/web2py_ajax.html
> ```
>
> 看起来像这样：
>
> ```
> {{
> response.files.insert(0, URL('static', 'js/jquery.js'))
> response.files.insert(1, URL('static', 'css/calenadar.css'))
> response.files.insert(2, URL('static', 'js/calendar.js'))
> response.include_meta()
> response.include_files()
> }}
> <script type="text/javascript"><!--
>     // These variables are used by the web2py_ajax_init
>         // function in web2py.js (which is loaded below).
>     var w2p_ajax_confirm_message =
>         "{{=T('Are you sure you want to delete this object?')}}";
>     var w2p_ajax_date_format = "{{=T('%Y-%m-%d')}}";
>     var w2p_ajax_datetime_format = "{{=T('%Y-%m-%d %H:%M:%S')}}";
> //--></script>
> <script src="{{=URL('static', 'js/web2py.js')}}"
>         type="text/javascript"></script>
> ```
>
> 该文件包含在默认“layout.html”的 HEAD 中，它提供以下服务：
>
> - 包括“静态/jquery.js”。
> - 包括用于弹出日历的“static/calendar.js”和“static/calendar.css”。
> - 包括所有`response.meta`标题
> - 包括所有`response.files`（需要 CSS 和 JS，如代码中所述）
> - 设置表单变量并包含“static/js/web2y.js”
>
> “web2py.js”执行以下操作：
>
> - 定义一个`ajax`函数（基于 jQuery $.ajax）。
> - 使“error”类的任何 DIV 或“flash”类的任何标记对象向下滑动。
> - 防止在“整数”类的 INPUT 字段中键入无效整数。
> - 防止在“double”类的 INPUT 字段中键入无效的浮点数。
> - 将“日期”类型的 INPUT 字段与弹出日期选择器连接起来。
> - 将“日期时间”类型的 INPUT 字段与弹出日期时间选择器连接起来。
> - 将“时间”类型的 INPUT 字段与弹出时间选择器连接。
> - 定义`web2py_ajax_component`，一个非常重要的工具，将在第 12 章中介绍。
> - 定义`web2py_websocket`，一个可用于 HTML5 websockets 的函数（本书中没有描述，但请阅读“gluon/contrib/websocket__messaging.py”源中的示例）。
> - 定义熵计算和密码字段输入验证的函数。
>
> 它还包括向后兼容的`popup`、`collapse`和`fade`函数。
>
> 这是其他效果如何很好地配合使用的示例。
>
> 考虑一个具有以下模型的**测试应用程序：**
>
> ```
> db = DAL("sqlite://storage.sqlite")
> db.define_table('child',
>      Field('name'),
>      Field('weight', 'double'),
>      Field('birth_date', 'date'),
>      Field('time_of_birth', 'time'))
> 
> db.child.name.requires=IS_NOT_EMPTY()
> db.child.weight.requires=IS_FLOAT_IN_RANGE(0, 100)
> db.child.birth_date.requires=IS_DATE()
> db.child.time_of_birth.requires=IS_TIME()
> ```
>
> 使用这个“default.py”控制器：
>
> ```
> def index():
>     form = SQLFORM(db.child)
>     if form.process().accepted:
>         response.flash = 'record inserted'
>     return dict(form=form)
> ```
>
> 以及以下“default/index.html”视图：
>
> ```
> {{extend 'layout.html'}}
> {{=form}}
> ```
>
> “索引”操作生成以下形式：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7000.png)
>
> 
>
> 如果提交了无效的表单，服务器将返回包含错误消息的修改表单的页面。错误消息是“error”类的DIV，由于上面的web2py.js代码，错误以向下滑动的效果出现：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7100.png)
>
> 
>
> 错误的颜色在“layout.html”的 CSS 代码中给出。
>
> web2py.js 代码可防止您在输入字段中输入无效值。这是在服务器端验证之前和补充，而不是替代服务器端验证。
>
> 当您输入“日期”类的 INPUT 字段时，web2py.js 代码会显示一个日期选择器，当您输入“日期时间”类的 INPUT 字段时，它会显示一个日期时间选择器。这是一个例子：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7200.png)
>
> 
>
> 当您尝试编辑“时间”类的 INPUT 字段时，web2py.js 代码还会显示以下时间选择器：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7300.png)
>
> 
>
> 提交后，控制器操作将响应闪存设置为消息“已插入记录”。默认布局在 id="flash" 的 DIV 中呈现此消息。web2py.js 代码负责使此 DIV 出现并在您单击它时使其消失：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7400.png)
>
> 
>
> 这些和其他效果可以在视图中以编程方式访问，也可以通过控制器中的帮助程序访问。
>
> 
>
> ### jQuery 效果
>
> 这里描述的基本效果不需要任何额外的文件；您需要的一切都已包含在 web2py_ajax.html 中。
>
> HTML/XHTML 对象可以通过它们的类型（例如 DIV）、它们的类或它们的 id 来识别。例如：
>
> ```
> <div class="one" id="a">Hello</div>
> <div class="two" id="b">World</div>
> ```
>
> 它们分别属于“一”和“二”类。它们的 id 分别等于“a”和“b”。
>
> 在 jQuery 中，您可以使用以下类似 CSS 的等效符号来引用前者
>
> ```
> jQuery('.one')    // address object by class "one"
> jQuery('#a')      // address object by id "a"
> jQuery('DIV.one') // address by object of type "DIV" with class "one"
> jQuery('DIV #a')  // address by object of type "DIV" with id "a"
> ```
>
> 并与后者
>
> ```
> jQuery('.two')
> jQuery('#b')
> jQuery('DIV.two')
> jQuery('DIV #b')
> ```
>
> 或者你可以参考两者
>
> ```
> jQuery('DIV')
> ```
>
> 标记对象与事件相关联，例如“onclick”。jQuery 允许将这些事件链接到效果，例如“slideToggle”：
>
> ```
> <div class="one" id="a" onclick="jQuery('.two').slideToggle()">Hello</div>
> <div class="two" id="b">World</div>
> ```
>
> 现在如果你点击“Hello”，“World”就会消失。如果再次单击，“世界”会重新出现。您可以通过给它一个隐藏类来默认隐藏标签：
>
> ```
> <div class="one" id="a" onclick="jQuery('.two').slideToggle()">Hello</div>
> <div class="two hidden" id="b">World</div>
> ```
>
> 您还可以将操作链接到标签本身之外的事件。之前的代码可以改写如下：
>
> ```
> <div class="one" id="a">Hello</div>
> <div class="two" id="b">World</div>
> <script>
> jQuery('.one').click(function(){jQuery('.two').slideToggle()});
> </script>
> ```
>
> 效果返回调用对象，因此它们可以被链接。
>
> 当`click`设置回调函数在点击时被调用。`change`, `keyup`, `keydown`,`mouseover`等类似。
>
> 一种常见的情况是需要在整个文档加载后才执行一些 JavaScript 代码。这通常由`onload`BODY 的属性完成，但 jQuery 提供了一种不需要编辑布局的替代方法：
>
> ```
> <div class="one" id="a">Hello</div>
> <div class="two" id="b">World</div>
> <script>
> jQuery(document).ready(function(){
>    jQuery('.one').click(function(){jQuery('.two').slideToggle()});
> });
> </script>
> ```
>
> 未命名函数的主体仅在文档准备就绪时执行，在它完全加载之后。
>
> 以下是有用的事件名称列表：
>
> ##### 表单事件
>
> - `onchange`: 元素改变时运行的脚本
> - `onsubmit`: 提交表单时运行的脚本
> - `onreset`: 表单重置时运行的脚本
> - `onselect`：选择元素时运行的脚本
> - `onblur`: 当元素失去焦点时运行的脚本
> - `onfocus`: 当元素获得焦点时运行的脚本
>
> ##### 键盘事件
>
> - `onkeydown`: 按下键时运行的脚本
> - `onkeypress`：按下和释放键时运行的脚本
> - `onkeyup`: 释放键时运行的脚本
>
> ##### 鼠标事件
>
> - `onclick`：在鼠标单击时运行的脚本
> - `ondblclick`: 鼠标双击运行的脚本
> - `onmousedown`: 按下鼠标按钮时运行的脚本
> - `onmousemove`: 鼠标指针移动时运行的脚本
> - `onmouseout`：当鼠标指针移出元素时运行的脚本
> - `onmouseover`: 当鼠标指针移动到元素上时运行的脚本
> - `onmouseup`: 释放鼠标按钮时运行的脚本
>
> 以下是 jQuery 定义的有用效果列表：
>
> ##### 效果
>
> - `jQuery(...).show()`：使对象可见
> - `jQuery(...).hide()`：使对象隐藏
> - `jQuery(...).slideToggle(speed, callback)`：使对象向上或向下滑动
> - `jQuery(...).slideUp(speed, callback)`：使对象向上滑动
> - `jQuery(...).slideDown(speed, callback)`: 使对象向下滑动
> - `jQuery(...).fadeIn(speed, callback)`：使对象淡入
> - `jQuery(...).fadeOut(speed, callback)`：使对象淡出
>
> speed 参数通常是“slow”、“fast”或省略（默认）。回调是一个可选函数，在效果完成时调用。
>
> jQuery 效果也可以很容易地嵌入到帮助器中，例如，在视图中：
>
> ```
> {{ = DIV ( '点击我！' ,  _onclick = "jQuery(this).fadeOut()" )}}
> ```
>
> 处理选定元素的其他有用方法和属性
>
> ##### 方法和属性
>
> - `jQuery(...).prop(name)`：返回属性值的名称
> - `jQuery(...).prop(name, value)`：将属性名称设置为值
> - `jQuery(...).html()`: 没有参数，它从被选元素返回内部 html，它接受一个字符串作为替换标签内容的参数。
> - `jQuery(...).text()`：没有参数，它返回所选元素的内部文本（没有标签），如果一个字符串作为参数传递，它用新数据替换内部文本。
> - `jQuery(...).val()`: 不带参数，返回被选元素的第一个元素的当前值，如果一个字符串作为参数传递，它会替换每个匹配元素的值。
> - `jQuery(...).css(name, value)`: 有一个参数，它返回为选定元素指定的样式属性的 CSS 值。使用两个参数，它为指定的 CSS 属性设置一个新值。
> - `jQuery(...).each(function)`：它循环遍历选定的元素集并以每个项目作为参数调用函数。
> - `jQuery(...).index()`：没有参数，它返回与其兄弟相关的第一个选定元素的索引值。（即，LI 元素的索引）。如果一个元素作为参数传递，它返回与所选元素集相关的元素位置。
> - `jQuery(...).length`：此属性返回所选元素的数量。
>
> jQuery 是一个非常紧凑和简洁的 Ajax 库；因此 web2py 不需要在 jQuery 之上额外的抽象层（除了`ajax`下面讨论的函数）。jQuery API 可以在需要时以其本机形式访问并随时可用。
>
> 有关这些效果和其他 jQuery API 的更多信息，请参阅文档。
>
> jQuery 库也可以使用插件和用户界面小部件进行扩展。此处不涉及此主题；见参考文献。[ [jquery-ui](http://web2py.com/books/default/reference/29/jquery-ui) ]了解详情。
>
> 
>
> #### 表单中的条件字段
>
> jQuery 效果的典型应用是根据字段值改变外观的表单。
>
> 这在 web2py 中很容易，因为 SQLFORM 助手生成“CSS 友好”的表单。该表单包含一个带有行的表。每行包含一个标签、一个输入字段和一个可选的第三列。这些项目的 id 严格来自表名和字段名。
>
> 约定是每个 INPUT 字段都有一个 id`tablename_fieldname`并包含在带有 id 的行中`tablename_fieldname__row`。
>
> 例如，创建一个输入表单，要求输入纳税人姓名和纳税人配偶的姓名，但前提是他/她已婚。
>
> 使用以下模型创建一个测试应用程序：
>
> ```
> db = DAL('sqlite://storage.sqlite')
> db.define_table('taxpayer',
>     Field('spouse_name'))
> ```
>
> 以下“default.py”控制器：
>
> ```
> def index():
>     form = SQLFORM(db.taxpayer)
>     if form.process().accepted:
>         response.flash = 'record inserted'
>     return dict(form=form)
> ```
>
> 以及以下“default/index.html”视图：
>
> ```
> {{extend 'layout.html'}}
> {{=form}}
> <script>
> jQuery(document).ready(function(){
>    if(jQuery('#taxpayer_married').prop('checked'))
>         jQuery('#taxpayer_spouse_name__row').show();
>    else jQuery('#taxpayer_spouse_name__row').hide();
>    jQuery('#taxpayer_married').change(function(){
>         if(jQuery('#taxpayer_married').prop('checked'))
>             jQuery('#taxpayer_spouse_name__row').show();
>         else jQuery('#taxpayer_spouse_name__row').hide();});
> });
> </script>
> ```
>
> 视图中的脚本具有隐藏包含配偶姓名的行的效果：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7500.png)
>
> 
>
> 当纳税人勾选“已婚”复选框时，配偶的姓名字段再次出现：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7600.png)
>
> 
>
> 这里“taxpayer_married”是与表“taxpayer”的“boolean”字段“married”关联的复选框。“taxpayer_spouse_name__row”是包含表“taxpayer”的“spouse_name”输入字段的行。
>
> 
>
> #### 确认删除
>
> 另一个有用的应用程序是在检查“删除”复选框时需要确认，例如出现在编辑表单中的删除复选框。
>
> 考虑上面的示例并添加以下控制器操作：
>
> ```
> def edit():
>     row = db.taxpayer[request.args(0)]
>     form = SQLFORM(db.taxpayer, row, deletable=True)
>     if form.process().accepted:
>         response.flash = 'record updated'
>     return dict(form=form)
>           
>      
> ```
>
> 和相应的视图“default/edit.html”
>
> ```
> {{extend 'layout.html'}}
> {{=form}}
> ```
>
> `deletable=True`SQLFORM 构造函数中的参数指示 web2py 在编辑表单中显示“删除”复选框。`False`默认情况下。
>
> web2py 的“web2py.js”包括以下代码：
>
> ```
> jQuery(document).ready(function(){
>    jQuery('input.delete').prop('onclick',
>      'if(this.checked) if(!confirm(
>         "{{=T('Sure you want to delete this object?')}}"))
>       this.checked=False;');
> });
> ```
>
> 按照惯例，此复选框的类等于“删除”。上面的 jQuery 代码将此复选框的 onclick 事件与确认对话框（JavaScript 中的标准）连接起来，如果纳税人不确认，则取消选中该复选框：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7700.png)
>
> 
>
> 
>
> ### `ajax`功能_
>
> 在 web2py.js 中，web2py 定义了一个名为的函数，该函数`ajax`基于但不应与 jQuery 函数混淆`$.ajax`。后者比前者强大得多，关于它的用法，我们建议您参考 ref。[ [jquery](http://web2py.com/books/default/reference/29/jquery) ]和参考。[ [jquery-b](http://web2py.com/books/default/reference/29/jquery-b) ]。但是，前一种功能对于许多复杂的任务来说已经足够了，而且更容易使用。
>
> 该`ajax`函数是一个 JavaScript 函数，具有以下语法：
>
> ```
> ajax ( url ,  [ name1 ,  name2 ,  ... ],  target ,  options )
> ```
>
> 它异步调用 url（第一个参数），传递名称等于列表中的一个名称（第二个参数）的字段输入的值，然后将响应存储在 id 等于 target 的标签的 innerHTML 中（第三个论点）。
>
> 下面是一个`default`控制器的例子：
>
> ```
> def one():
>     return dict()
> 
> def echo():
>     return request.vars.name
> ```
>
> 以及相关的“default/one.html”视图：
>
> ```
> {{ extend  'layout.html' }} 
> < form > 
>    < input  name = "name"  onkeyup = "ajax('{{=URL('default', 'echo')}}', ['name'], ' target')"  /> 
> </ form > 
> < div  id = "target" ></ div >
> ```
>
> 当您在 INPUT 字段中键入内容时，只要您释放一个键 (onkeyup)，`ajax`就会调用该函数，并将该`name="name"`字段的值传递给操作“echo”，该操作会将文本发送回视图。该`ajax`函数接收响应并在“目标”DIV 中显示回波响应。
>
> 您还可以将选项（第四个参数）直接传递给内部`$.ajax`调用。这在需要失败回调或必须指定内容类型的情况下很有用。
>
> 
>
> #### 评估目标
>
> 函数的第三个参数`ajax`可以是字符串 ":eval"。这意味着服务器返回的字符串不会嵌入到文档中，而是会被评估。
>
> 下面是一个`default`控制器的例子：
>
> ```
> def one():
>     return dict()
> 
> def echo():
>     return "jQuery('#target').html(%s);" % repr(request.vars.name)
> ```
>
> 以及相关的“default/one.html”视图：
>
> ```
> {{ extend  'layout.html' }} 
> < form > 
>    < input  name = "name"  onkeyup = "ajax('{{=URL('default', 'echo')}}', ['name'], ' :eval')"  /> 
> </ form > 
> < div  id = "target" ></ div >
> ```
>
> 这允许可以更新多个目标的更复杂的响应。
>
> 
>
> #### 自动完成
>
> Web2py 包含一个内置的自动完成小部件，在 Forms 章节中进行了描述。在这里，我们将从头开始构建一个更简单的。
>
> 上述`ajax`功能的另一个应用是自动完成。在这里，我们希望创建一个需要月份名称的输入字段，并且当访问者键入不完整的名称时，通过 Ajax 请求执行自动完成。作为响应，输入字段下方会出现一个自动完成下拉框。
>
> 这可以通过以下`default`控制器来实现：
>
> ```
> def month_input():
>     return dict()
> 
> def month_selector():
>     if not request.vars.month: return ''
>     months = ['January', 'February', 'March', 'April', 'May',
>               'June', 'July', 'August', 'September' , 'October',
>               'November', 'December']
>     month_start = request.vars.month.capitalize()
>     selected = [m for m in months if m.startswith(month_start)]
>     return DIV(*[DIV(k,
>                      _onclick="jQuery('#month').val('%s')" % k,
>                      _onmouseover="this.style.backgroundColor='yellow'",
>                      _onmouseout="this.style.backgroundColor='white'"
>                      ) for k in selected])
> ```
>
> 和相应的“default/month_input.html”视图：
>
> ```
> {{extend 'layout.html'}}
> <style>
> #suggestions { position: relative; }
> .suggestions { background: white; border: solid 1px #55A6C8; }
> .suggestions DIV { padding: 2px 4px 2px 4px; }
> </style>
> 
> <form>
>  <input type="text" id="month" name="month" style="width: 250px" /><br />
>  <div style="position: absolute;" id="suggestions"
>       class="suggestions"></div>
> </form>
> <script>
> jQuery("#month").keyup(function(){
>       ajax('{{=URL('default', 'month_selector')}}', ['month'], 'suggestions')});
> </script>
> ```
>
> 每次访问者在“月”输入字段中键入内容时，视图中的 jQuery 脚本都会触发 Ajax 请求。输入字段的值与 Ajax 请求一起提交给“month_selector”操作。此操作查找以提交的文本（选定）开头的月份名称列表，构建 DIV 列表（每个都包含建议的月份名称），并返回带有序列化 DIV 的字符串。该视图在“建议”DIV 中显示响应 HTML。“month_selector”操作生成建议和嵌入在 DIV 中的 JavaScript 代码，当访问者单击每个建议时必须执行这些代码。例如，当访问者键入“M”时，回调操作返回：
>
> ```
> <div>
>      <div onclick="jQuery('#month').val('March')"
>           onmouseout="this.style.backgroundColor='white'"
>           onmouseover="this.style.backgroundColor='yellow'">March</div>
>      <div onclick="jQuery('#month').val('May')"
>           onmouseout="this.style.backgroundColor='white'"
>           onmouseover="this.style.backgroundColor='yellow'">May</div>
> </div>
> ```
>
> 这是最终效果：
>
> 
>
> ![图片](http://www.web2py.com/books/default/image/29/en7800.png)
>
> 
>
> 如果月份存储在数据库表中，例如：
>
> ```
> db.define_table('month', Field('name'))
> ```
>
> 然后只需将`month_selector`操作替换为：
>
> ```
> def month_input():
>     return dict()
> 
> def month_selector():
>     if not request.vars.month:
>         return ''
>     pattern = request.vars.month.capitalize() + '%'
>     selected = [row.name for row in db(db.month.name.like(pattern)).select()]
>     return ''.join([DIV(k,
>                  _onclick="jQuery('#month').val('%s')" % k,
>                  _onmouseover="this.style.backgroundColor='yellow'",
>                  _onmouseout="this.style.backgroundColor='white'"
>                  ).xml() for k in selected]) 
> ```
>
> jQuery 提供了一个可选的带有附加功能的自动完成插件，但这里不讨论。
>
> 
>
> #### Ajax 表单提交
>
> 这里我们考虑一个页面，它允许访问者使用 Ajax 提交消息而无需重新加载整个页面。使用 LOAD 帮助器，web2py 提供了比这里描述的更好的机制，这将在第 12 章中描述。这里我们想向您展示如何简单地使用 jQuery 来完成它。
>
> 它包含一个表单“myform”和一个“目标”DIV。当表单提交时，服务器可能会接受它（并执行数据库插入）或拒绝它（因为它没有通过验证）。相应的通知与 Ajax 响应一起返回并显示在“目标”DIV 中。
>
> `test`使用以下模型构建应用程序：
>
> ```
> db = DAL('sqlite://storage.sqlite')
> db.define_table('post', Field('your_message', 'text'))
> db.post.your_message.requires = IS_NOT_EMPTY()
> ```
>
> 请注意，每个帖子都有一个字段“your_message”，该字段必须为非空。
>
> 编辑`default.py`控制器并编写两个动作：
>
> ```
> def index():
>     return dict()
> 
> def new_post():
>     form = SQLFORM(db.post)
>     if form.accepts(request, formname=None):
>         return DIV("Message posted")
>     elif form.errors:
>         return TABLE(*[TR(k, v) for k, v in form.errors.items()])
> ```
>
> 第一个动作只是返回一个视图。
>
> 第二个动作是 Ajax 回调。它期望 中的表单变量`request.vars`，处理它们并`DIV("Message posted")`在成功时返回或`TABLE`在失败时返回错误消息。
>
> 现在编辑“default/index.html”视图：
>
> ```
> {{extend 'layout.html'}}
> 
> <div id="target"></div>
> 
> <form id="myform">
>   <input name="your_message" id="your_message" />
>   <input type="submit" />
> </form>
> 
> <script>
> jQuery('#myform').submit(function() {
>   ajax('{{=URL('new_post')}}',
>        ['your_message'], 'target');
>   return false;
> });
> </script>
> ```
>
> 请注意，在此示例中，表单是如何使用 HTML 手动创建的，但它是由 SQLFORM 在与显示表单的操作不同的操作中处理的。SQLFORM 对象永远不会在 HTML 中序列化。`SQLFORM.accepts`在这种情况下不使用 session 和 sets `formname=None`，因为我们选择在手动 HTML 表单中不设置表单名称和表单键。
>
> 视图底部的脚本将“myform”提交按钮连接到一个内联函数，该函数`id="your_message"`使用 web2py`ajax`函数提交 INPUT，并在 DIV 中显示答案`id="target"`。
>
> 
>
> #### 投票和评级
>
> 另一个 Ajax 应用程序是对页面中的项目进行投票或评级。在这里，我们考虑一个允许访问者对发布的图像进行投票的应用程序。该应用程序由一个页面组成，该页面显示根据投票排序的图像。我们将允许访问者多次投票，尽管如果访问者经过身份验证，通过跟踪数据库中的个人投票并将其与`request.env.remote_addr`投票者相关联，很容易改变这种行为。
>
> 这是一个示例模型：
>
> ```
> db = DAL('sqlite://images.db')
> db.define_table('item',
>     Field('image', 'upload'),
>     Field('votes', 'integer', default=0))
> ```
>
> 这是`default`控制器：
>
> ```
> def list_items():
>     items = db().select(db.item.ALL, orderby=db.item.votes)
>     return dict(items=items)
> 
> def download():
>     return response.download(request, db)
> 
> def vote():
>     item = db.item[request.vars.id]
>     new_votes = item.votes + 1
>     item.update_record(votes=new_votes)
>     return str(new_votes)
> ```
>
> 下载操作是允许 list_items 视图下载存储在“uploads”文件夹中的图像所必需的。投票操作用于 Ajax 回调。
>
> 这是“默认/list_items.html”视图：
>
> ```
> {{extend 'layout.html'}}
> 
> <form><input type="hidden" id="id" name="id" value="" /></form>
> {{for item in items:}}
> <p>
> <img src="{{=URL('download', args=item.image)}}"
>      width="200px" />
> <br />
> Votes=<span id="item{{=item.id}}">{{=item.votes}}</span>
> [<span onclick="jQuery('#id').val('{{=item.id}}');
>        ajax('{{=URL('default', 'vote')}}', ['id'], 'item{{=item.id}}');">vote up</span>]
> </p>
> {{pass}}
> ```
>
> 当访问者单击“[投票]”时，JavaScript 代码将 item.id 存储在隐藏的“id”输入字段中，并通过 Ajax 请求将此值提交给服务器。服务器增加相应记录的投票计数器，并将新的投票计数作为字符串返回。然后将该值插入到目标`item{{=item.id}}`SPAN 中。
>
> > Ajax 回调可用于在后台执行计算，但我们建议使用**cron**或后台进程（在第 4 章中讨论），因为 Web 服务器强制线程超时。如果计算时间过长，Web 服务器会终止它。请参考您的 Web 服务器参数来设置超时值