# Flask基础介绍 #

Flask 是一个轻量级的 Web 应用框架, 使用 Python 编写。基于 [WerkzeugWSGI](http://werkzeug.pocoo.org/) 工具箱和 [Jinja2](http://jinja.pocoo.org/) 模板引擎。使用 BSD 授权。Flask 也被称为 `microframework` ，因为它使用简单的核心，用 extension 增加其它功能。Flask 没有默认使用的数据库、窗体验证工具。然而，Flask 保留了扩增的弹性，可以用 Flask-extension 加入这些功能：ORM、窗体验证工具、文件上传、各种开放式身份验证技术。

# Flask搭建博客步骤 #

## 博客功能 ##

 1. 根据配置文件中的认证允许用户登录以及注销。仅仅支持一个用户。
 2. 当用户登录后，他们可以添加新的条目，这些条目是由纯文本的标题和 HTML 的正文构成。因为我们信任用户这里的 HTML 是安全的。
 3. 页面倒序显示所有条目（新的条目在前），并且用户登入后可以在此添加新条目。

----

## 步骤 ##

### 1.创建文件夹 ###

在开始之前，让我们为这个应用创建需要的文件夹:

```text
/flaskr
    /static
    /templates
```

其中：

- flaskr：项目总文件夹。其中会存放数据库代码文件 `schema.sql` 和 主代码文件 `flaskr.py`。
- static：项目的资源文件。用于存放 `css` 和 `javascript` 文件。
- templates：项目前端页面文件。用于存放前端页面模板。

### 2.数据库模式 ###

首先我们要创建数据库模式。对于这个应用仅一张表就足够了，而且我们只想支持 SQLite ，所以很简单。在 `Code/flaskr` 目录下新建 `schema.sql` 文件并向其中写入如下代码：

```sql
drop table if exists entries;
create table entries (
  id integer primary key autoincrement,
  title string not null,
  text string not null
);
```

这个模式由一个称为 `entries` 的单表构成，在这个表中每行包含一个 `id` ，一个 `title` 和一个 `text`。`id` 是一个自增的整数而且是主键，其余两个为非空的字符串。

### 3.应用设置代码 ###

现在我们已经有了数据库模式了，可以创建应用的模块了。
在 `Code/flaskr` 目录下新建 `flaskr.py` 文件。对于小应用，直接把配置放在主模块里，正如我们现在要做的一样，这是可行的。然而一个更干净的解决方案就是单独创建`.ini`或者`.py`文件接着加载或者导入里面的值。
在 `flaskr.py` 文件中写入如下代码:

```python
# 导入所有的模块
import sqlite3
from flask import Flask, request, session, g, redirect, url_for, abort, render_template, flash
# 配置文件
DATABASE = '/tmp/flaskr.db'
ENV = 'development'
DEBUG = True
SECRET_KEY = 'development key'
USERNAME = 'admin'
PASSWORD = 'default'
```

下一步我们能够创建真正的应用，修改 `flaskr.py` 文件在其中配置初始化:

```python
# 创建应用
app = Flask(__name__)
app.config.from_object(__name__)
```

`from_object()` 将会寻找给定的对象(如果它是一个字符串，则会导入它)，搜寻里面定义的全部大写的变量。在我们的这种情况中，配置文件就是我们上面写的几行代码。 你也可以将他们分别存储到多个文件。
通常从配置文件中加载配置是一个好的主意。这时可以使用 `from_envvar()` 来实现，可以用它替换上面的 `from_object()`：

```python
app.config.from_envvar('FLASKR_SETTINGS', silent=True)
```

这种方法我们可以设置一个名为 `FLASKR_SETTINGS` 的环境变量来设定一个配置文件载入后是否覆盖默认值。静默开关 `silent=True` 告诉 Flask 不去关心这个环境变量键值是否存在。
`SECRET_KEY` 是为了保持客户端的会话安全。明智地选择该键，使得它难以猜测，最好是尽可能复杂。

DEBUG 调试标志启用或禁用交互式调试。决不让调试模式在生产系统中启动，因为它将允许用户在服务器上执行代码！

我们还添加了一个轻松地连接到指定数据库的方法，这个方法用于在请求时打开一个连接，并且在交互式 Python shell 和脚本中也能使用，这将方便后面的使用。

在 `Code/flaskr.py` 文件中继续添加如下代码：

```python
def connect_db():
    return sqlite3.connect(app.config['DATABASE'])
```

最后如果我们想要把这个文件当做独立应用来运行，只需在服务器启动文件也就是 `Code/flaskr.py` 文件的末尾添加这一行:

```python
if __name__ == '__main__':
    app.run()
```

### 4.创建数据库 ###

如前面所述，Flaskr 是一个数据库驱动的应用程序，准确地来说，Flaskr 是一个使用关系数据库系统的应用程序。这样的系统需要一个模式告诉它们如何存储信息。因此在首次启动服务器之前，创建数据库模式是很重要的。

在 `Code/flaskr.py` 文件中添加如下代码:

```python
from contextlib import closing
```

接着我们可以创建一个称为 init_db 函数，该函数用来初始化数据库。为此我们可以使用之前定义的 connect_db 函数。 只要在 `Code/flaskr.py` 文件中的 connect_db 函数下添加这样的函数:

```python
def init_db():
    with closing(connect_db()) as db:
        with app.open_resource('schema.sql') as f:
            db.cursor().executescript(f.read().decode())
        db.commit()
```
`closing()`函数允许我们在 `with` 块中保持数据库连接可用。应用对象的 `open_resource()` 方法在其方框外也支持这个功能，因此可以在 `with` 块中直接使用。这个函数从当前位置（你的flaskr 文件夹）中打开一个文件，并且允许你读取它。我们在这里用它在数据库连接上执行一个脚本。

当我们连接到数据库时会得到一个数据库连接对象（这里命名它为 `db`），这个对象提供给我们一个数据库指针。指针上有一个可以执行完整脚本的方法。最后我们不显式地提交更改， SQLite 3 或者其它事务数据库不会这么做。

现在可以在 Python shell 里创建数据库，导入并调用刚才的函数:

```python
python3
>>> from flaskr import init_db
>>> init_db()
```

### 5.请求数据库连接 ###

所有我们的函数中都需要数据库连接，因此在请求之前初始化它们，在请求结束后自动关闭它们就很有意义。

Flask 允许我们使用 `before_request()` ，`after_request()` 和 `teardown_request()` 装饰器来实现这个功能，在 `Code/flaskr.py` 文件中添加如下代码:

```python
@app.before_request
def before_request():
    g.db = connect_db()

@app.teardown_request
def teardown_request(exception):
    g.db.close()
```

使用 `before_request()` 装饰器的函数会在请求之前被调用而且不带参数。使用 `after_request()` 装饰器的函数会在请求之后被调用且传入将要发给客户端的响应。

它们必须返回那个响应对象或是不同的响应对象。但当异常抛出时，它们不一定会被执行，这时可以使用 `teardown_request()` 装饰器。它装饰的函数将在响应构造后执行，并不允许修改请求，返回的值会被忽略。如果在请求已经被处理的时候抛出异常，它会被传递到每个函数，否则会传入一个 `None`。

我们把当前的数据库连接保存在 Flask 提供的 g 特殊对象中。这个对象只能保存一次请求的信息，并且在每个函数里都可用。不要用其它对象来保存信息，因为在多线程环境下将不可行。特殊的对象 g 在后台有一些神奇的机制来保证它在做正确的事情。

### 6.建立视图 ###

现在数据库连接已经正常工作，可以开始编写视图函数。我们需要四个视图函数：

#### 显示条目 ####

这个视图显示所有存储在数据库中的条目。它监听着应用的根地址以及将会从数据库中查询标题和内容。 `id` 值最大的条目（最新的条目）将在最前面。从游标返回的行是按 `select` 语句中声明的列组织的元组。对于像我们这样的小应用是足够的，但是你可能要把它们转换成字典，如果你对如何转换成字典感兴趣的话，请查阅[简化查询](http://www.pythondoc.com/flask/patterns/sqlite3.html#easy-querying)例子。

视图函数将会把条目作为字典传入 `show_entries.html` 模板并返回渲染结果。

在 `flaskr/flaskr.py` 文件中添加如下代码:

```python
@app.route('/')
def show_entries():
    cur = g.db.execute('select title, text from entries order by id desc')   # 查询语句
    entries = [dict(title=row[0], text=row[1]) for row in cur.fetchall()]   # 将查询结果转换为字典
    return render_template('show_entries.html', entries=entries)
```

#### 添加新条目 ####

这个视图允许登录的用户添加新的条目。它只回应 `POST` 请求，实际的表单是显示在 `show_entries` 页面。如果正常工作的话，我们用 `flash()` 向下一个请求闪现一条信息并且跳转回 `show_entries` 页面。

在 `flaskr/flaskr.py` 文件中添加如下代码:

```python
@app.route('/add', methods=['POST'])
def add_entry():
    if not session.get('logged_in'):
        abort(401)
    g.db.execute('insert into entries (title, text) values (?, ?)',[request.form['title'], request.form['text']])   # 向数据库中插入数据
    g.db.commit()   # 更新数据
    flash('New entry was successfully posted')   # 闪现一条消息
    return redirect(url_for('show_entries'))
```

注意我们这里检查用户登录情况( `logged_in` 键存在会话中，并且为 `True` )。向 SQL 语句中传递参数，需要在语句中使用问号 `?` 来代替参数，并把参数放在列表中进行传递。不要使用字符串格式化的方式直接把参数传入 SQL 语句中，这样可能会有潜在的 SQL 注入风险。

#### 登录 ####

这些函数是用于用户登录以及注销。登录时依据在配置中的值检查用户名和密码并且在会话中设置 `logged_in` 键值。如果用户成功登录，`logged_in` 键值被设置成 `True` ，并跳转回 `show_entries` 页面。此外，会有消息闪现来提示用户登录成功。

如果发生错误，模板会通知，并提示重新登录。在 `flaskr/flaskr.py` 文件中添加如下代码:

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    error = None
    if request.method == 'POST':
        if request.form['username'] != app.config['USERNAME']:   # 如果用户名不符合
            error = 'Invalid username'
        elif request.form['password'] != app.config['PASSWORD']:   # 如果密码不符合
            error = 'Invalid password'
        else:
            session['logged_in'] = True   # 成功登录，在 session 中添加一个 logged_in 值为 True
            flash('You were logged in')   # 闪现一条消息
            return redirect(url_for('show_entries'))   # 重定向到首页
    return render_template('login.html', error=error)   # 如果没有成功登录则返回登录页面以及错误信息
```

#### 注销 ####

另一方面，注销函数从会话中移除了 `logged_in` 键值。这里我们使用一个大绝招：如果你使用字典的 `pop()` 方法并传入第二个参数（默认），这个方法会从字典中删除这个键，如果这个键不存在则什么都不做。这很有用，因为我们不需要检查用户是否已经登入。

在 `flaskr/flaskr.py` 文件中添加如下代码:

```python
@app.route('/logout')
def logout():
    session.pop('logged_in', None)   # 移除 logged_in 键
    flash('You were logged out')   # 闪现消息
    return redirect(url_for('show_entries'))   # 重定向到首页
```

### 7.模板 ###

现在我们应该开始编写模板。

#### layout.html ####

这个模板包含 HTML 主体结构，标题和一个登录链接（或者当用户已登录则提供注销）。如果有闪现信息的话它也将显示闪现信息。`{% block body %}` 块能够被子模板中的同样名字( `body` )的块替代。

`session` 字典在模板中同样可用的，你能用它检查用户是否登录。注意在 Jinja 中你可以访问不存在的对象/字典属性或成员，如同下面的代码，即便 `logged_in` 键不存在，仍然可以正常工作。

在 `flaskr/templates` 目录下新建 `layout.html` 文件并写入如下代码：

```html
<!doctype html>
<title>Flaskr</title>
<link rel=stylesheet type=text/css href="{{ url_for('static', filename='style.css') }}">
<div class=page>
  <h1>Flaskr</h1>
  <div class=metanav>
  {% if not session.logged_in %}
    <a href="{{ url_for('login') }}">log in</a>
  {% else %}
    <a href="{{ url_for('logout') }}">log out</a>
  {% endif %}
  </div>
  {% for message in get_flashed_messages() %}
    <div class=flash>{{ message }}</div>
  {% endfor %}
  {% block body %}{% endblock %}
</div>
```

#### show_entries.html ####

这个模板继承了上面的`layout.html`模板用来显示信息。注意`for`遍历了所有我们用`render_template()`函数传入的信息。我们同样告诉表单提交到`add_entry`函数通过使用 HTTP 的`POST`方法。

在 `flaskr/templates` 目录下新建 `show_entries.html` 文件并写入如下代码：

```html
{% extends "layout.html" %}
{% block body %}
  {% if session.logged_in %}
    <form action="{{ url_for('add_entry') }}" method=post class=add-entry>
      <dl>
        <dt>Title:
        <dd><input type=text size=30 name=title>
        <dt>Text:
        <dd><textarea name=text rows=5 cols=40></textarea>
        <dd><input type=submit value=Share>
      </dl>
    </form>
  {% endif %}
  <ul class=entries>
  {% for entry in entries %}
    <li><h2>{{ entry.title }}</h2>{{ entry.text|safe }}
  {% else %}
    <li><em>Unbelievable.  No entries here so far</em>
  {% endfor %}
  </ul>
{% endblock %}
```

#### login.html ####

最后是登录模板，基本上只显示一个允许用户登录的表单。

在 `flaskr/templates` 目录下新建 `login.html` 文件并写入如下代码：

```html
{% extends "layout.html" %}
{% block body %}
  <h2>Login</h2>
  {% if error %}<p class=error><strong>Error:</strong> {{ error }}{% endif %}
  <form action="{{ url_for('login') }}" method=post>
    <dl>
      <dt>Username:
      <dd><input type=text name=username>
      <dt>Password:
      <dd><input type=password name=password>
      <dd><input type=submit value=Login>
    </dl>
  </form>
{% endblock %}
```

#### 添加样式 ####

现在其它一切都正常工作，是时候给应用添加些样式。

在 `Code/flaskr/static` 目录下新建 `style.css` 样式文件并写入如下代码：

```css
body            { font-family: sans-serif; background: #eee; }
a, h1, h2       { color: #377BA8; }
h1, h2          { font-family: 'Georgia', serif; margin: 0; }
h1              { border-bottom: 2px solid #eee; }
h2              { font-size: 1.2em; }

.page           { margin: 2em auto; width: 35em; border: 5px solid #ccc;
                  padding: 0.8em; background: white; }
.entries        { list-style: none; margin: 0; padding: 0; }
.entries li     { margin: 0.8em 1.2em; }
.entries li h2  { margin-left: -1em; }
.add-entry      { font-size: 0.9em; border-bottom: 1px solid #ccc; }
.add-entry dl   { font-weight: bold; }
.metanav        { text-align: right; font-size: 0.8em; padding: 0.3em;
                  margin-bottom: 1em; background: #fafafa; }
.flash          { background: #CEE5F5; padding: 0.5em;
                  border: 1px solid #AACBE2; }
.error          { background: #F0D6D6; padding: 0.5em; }
```

### 8.运行应用 ###

这样我们就完成了简单博客应用的代码编写
```bash
$ python3 flaskr.py
 * Serving Flask app "flaskr" (lazy loading)
 * Environment: development
 * Debug mode: on
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 183-564-157
```

访问首页 `http://127.0.0.1:5000` ，即可开启博客
