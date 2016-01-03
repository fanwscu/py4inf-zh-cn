# 第14章 数据库与结构化查询语言SQL

## 14.1 什么是数据库

数据库是经过组织的、存储数据的文件。从这个意义上讲，大多数数据库的组织方式与字典类似，数据库实现键与值之间的映射。数据库与字典的最大区别在于，数据库存储在磁盘（或其他永久存储器）上，程序运行结束后数据会永久存在。正是由于数据库存储在永久存储上，它能存储的数据远远多于字典。字典受到计算机内存大小的限制。

与字典类似，数据库软件被设计为快速保留插入的与访问的数据，即使是大量数据的情况亦如此。数据库软件通过对添加的数据构建索引来维护性能，让计算机可以快速跳转到特定数据项。

广泛应用的数据库系统包括Oracle、MySQL、Microsoft SQL Server、 PostgreSQL和SQLite。本书关注SQLite，因为它是一个非常通用的数据库，而且已经内建在Python。SQLite被设计为嵌入到其他应用程序，提供应用程序内的数据库支持。例如，Firefox浏览器把SQLite数据库作为内部使用，其他很多产品也这样做。

<http://sqlite.org/>

SQLite非常适合信息科学中的一些数据处理问题，比如本章介绍的Twitter爬虫应用。

## 14.2 数据库概念

初次接触数据库，可将其视为多个工作表的电子表格。数据库的主要数据结构包括表、行与列。

![enter image description here](http://www.pythonlearn.com/html-270/book016.png)

在关系型数据库中，表、行与列的专业定义为关系、元组与属性。本章将使用非专业化术语。

## 14.3 SQLite管理器（Firefox插件）

本章重点使用Python对SQLite数据库文件进行操作，许多操作可以用SQLite数据库管理器（一个Firefox插件）更方便地完成。免费下载地址如下：

<https://addons.mozilla.org/en-us/firefox/addon/sqlite-manager/>

使用Firefox浏览器可以在数据库中轻松创建表、插入数据、编辑数据、以及执行简单的SQL查询。

从某种意义上讲，在文本文件的处理方面，数据库管理器与文本编辑器类似。如果要对文本文件进行少量修改操作，你可以在文本编辑器中打开它，并根据需要修改。如果要对文本文件进行大量修改时，通常需要编写一个简单的Python程序。类似的，数据库同样存在相同的模式。在数据库管理器中执行一些简单操作，在Python中可以方便地处理一些复杂操作。

## 14.4 创建一张数据库的表

与Python的列表与字典相比，数据库需要更多的结构定义[^1]。

创建一个数据库的表，我们必须根据每一列存储的数据情况，预先在数据库中定义表的每一列名称和数据类型。数据库软件知道了每一列的数据类型，根据特定数据类型，它可以选择最有效的数据存储与检索方法。

以下网址介绍了SQLite支持的各种数据类型：

<http://www.sqlite.org/datatypes.html>

一开始就定义好数据结构可能不是很方便，但是这样做的好处是，当数据库包含大量数据可以提供快速的数据访问。

以下代码创建了一个数据库文件和带有两列的Tracks表。

```
import sqlite3

conn = sqlite3.connect('music.sqlite3')
cur = conn.cursor()

cur.execute('DROP TABLE IF EXISTS Tracks ')
cur.execute('CREATE TABLE Tracks (title TEXT, plays INTEGER)')

conn.close()
```

connect操作建立了与当前目录中music.sqlite3数据库文件的“连接”。如果文件不存在，则创建它。之所以称为“连接”，因为数据库有时存储在单独的数据库服务器上，与我们运行的应用程序不在同一个服务器上。在这个简单示例中，数据库作为一个本地文件，与Python代码处在同一个目录下。

游标（cursor）类似一个文件句柄，可以对数据库中的数据执行操作。当处理文本文件时，cursor()的调用与open()方法相似。

![enter image description here](http://www.pythonlearn.com/html-270/book017.png)

当有个游标，我们使用execute()方法，开始对数据库的内容执行命令。

数据库命令使用专门的语言，在众多数据库厂商中间已经标准化，用户只需学习一种数据库语言即可。数据库语言称为结构化查询语言，简称为SQL。

<http://en.wikipedia.org/wiki/SQL>

在这个例子中，我们对数据库执行两条SQL命令。按照惯例，我们用大写显示SQL关键词，其他部分如表和列名显示为小写。

如果Track表已经存在，第一条SQL命令就移除Tracks表。这一做法可以让我们反复执行相同的程序来创建Tracks表，而不会导致不错。需要注意的是，DROP TABLE命令会删除表以及数据库中表的所有内容，也就是说没有撤销的可能。

```
cur.execute('DROP TABLE IF EXISTS Tracks ')
```

第二条命令创建Tracks表，包括文本型的title列与整数型的plays列。

```
cur.execute('CREATE TABLE Tracks (title TEXT, plays INTEGER)')
```

现在，我们已经创建好Tracks表，接下来使用SQL的INSERT操作，向表中添加一些数据。我们再次与数据库建立连接，获得游标（cursor）。通过游标执行SQL命令。

SQL的INSERT命令表明所使用的表，通过列举字段来定义新列，(title, plays)后面跟VALUES具体的列值，从而产生一个新行。我们指定值为(?, ?)，这表示实际值通过第二个参数的一个元组(’My Way’, 15)来传递，最后调用execute()方法。

```
import sqlite3

conn = sqlite3.connect('music.sqlite3')
cur = conn.cursor()

cur.execute('INSERT INTO Tracks (title, plays) VALUES ( ?, ? )',
    ( 'Thunderstruck', 20 ) )
cur.execute('INSERT INTO Tracks (title, plays) VALUES ( ?, ? )',
    ( 'My Way', 15 ) )
conn.commit()

print 'Tracks:'
cur.execute('SELECT title, plays FROM Tracks')
for row in cur :
   print row

cur.execute('DELETE FROM Tracks WHERE plays < 100')
conn.commit()

cur.close()
```

首先，我们向表中插入两行，使用commit()提交命令将数据写入数据库文件。

![enter image description here](http://www.pythonlearn.com/html-270/book018.png)

然后，我们用SELECT命令检索刚插入表中的行。SELECT命令首先指定（title, plays）列，之后是数据检索的来源表。执行SELECT语句后，游标可以让我们用for语句进行循环。为了提高效率，当执行SELECT语句时，游标并不会从数据库中读取所有数据。相反，数据是在for循环时按需读取。

程序运行结果如下：

```
Tracks:
(u'Thunderstruck', 20)
(u'My Way', 15)
```

for循环找到两行，每一行是一个Python元组，其中第一个值是歌曲名称（title），第二个值是播放次数（plays）。不用担心，title字符串以u’开头。这说明字符串使用Unicode，即能够存储非拉丁字符集。

在程序末尾，我们执行SQL的DELETE命令，删除刚才创建的行，以便可以反复运行这个程序。DELETE命令使用了WHERE子句，用来表达一个选择条件，这样SQL命令在数据库中只对条件匹配的行进行操作。在本示例中，条件应用于所有行，因此我们可以清空表，反复执行程序。在DELETE命令执行后，使用commit()提交命令将数据从数据库中删除。

## 14.5 结构化查询语言SQL小结

至此，我们在Python示例中使用了结构化查询语言，介绍了一些SQL命令的基本知识。本节专门介绍SQL语言，简要介绍SQL语法。

虽然数据库行业中存在众多数据库厂商，但结构化查询语言SQL的标准化使得不同厂商之间的数据库系统可以进行数据互通与移植。

关系型数据库由表、行与列组成。列的常见字段类型包括文本、数值与日期数据。当创建表时，需要指明列的名称与字段类型：

```
CREATE TABLE Tracks (title TEXT, plays INTEGER)
```

使用SQL的INSERT命令向表中插入一行：

```
INSERT INTO Tracks (title, plays) VALUES ('My Way', 15)
```

INSERT语句指定表的名称，之后是想要插入新行的字段（列）的列表，然后是VALUES关键词及其后面每个字段对应的值列表。

SQL的SELECT命令从数据库中检索行与列。SELECT语句指定想要检索的列，WHERE子句用于筛选出符合条件的行。另外，可选的ORDER BY子句控制返回的行的显示顺序。

```
SELECT * FROM Tracks WHERE title = 'My Way'
```

*星号表示从数据库返回WHERE子句匹配到的行的所有列。

请注意，与Python不同的是，SQL的WHERE子句使用一个等号表示相等，而不是两个等号。WHERE子句的其他逻辑操作符包括<、>、 <=、 >=和!=，以及AND、OR与括号，这些可用于编写逻辑表达式。

根据一个字段对返回的行进行排序的查询如下：

```
SELECT title,plays FROM Tracks ORDER BY title
```

要移除行，需要在SQL的DELETE语句增加一个WHERE子句。WHERE子句决定哪些行可被删除：

```
DELETE FROM Tracks WHERE title = 'My Way'
```

在一个表中可以用SQL的UPDATE语句对一行或多行的一个列或多列进行更新。

```
UPDATE Tracks SET plays = 16 WHERE title = 'My Way'
```

UPDATE语句先指明待更新的表，在SET关键词之后设置修改的字段及其取值，然后可以用WHERE子句（可选的）选择要更新的行。一个UPDATE语句会修改WHERE子句匹配到的所有行，若不指定WHERE子句，它将更新表中所有的行。

以上是数据创建与维护的四个基本SQL命令（INSERT、 SELECT、 UPDATE和DELETE）。

## 14.6 使用数据库爬取Twitter

在本节中，我们编写一个简单的爬虫程序，通过Twitter账号采集数据，然后建立数据库。*请注意：谨慎执行这个程序，不要抓取太多数据或长时间执行程序，这样会导致你的Twitter账号被封。*

任何类型的爬虫程序都面临一个问题，它需要能被停止和重启多次，你也不想丢失已获取到的数据。你不希望总是在一开始重启数据检索，因此把检索到的数据存储起来，让爬虫程序能启动备份，并在它离开的地方继续检索。

我们通过检索一个用户的Twitter朋友及他们的状态，循环朋友列表，向数据库添加每个朋友的信息，以备后续检索。当处理了一个用户的Twitter朋友，我们登录数据库，检索朋友中的一个。重复这个操作，挑选一个“未访问过的”用户，检索他的用户列表，添加列表中没有的朋友，以备下次访问。

我们也追踪数据库中特定朋友的出现次数，以此查看“人气”情况。

通过存储已知账号的列表，不论是否检索这个账号，数据库中账号的人气情况已经存储在计算机磁盘，这样停止或重启程序多少次都没关系。

这个程序有些复杂，它基于前面的Twitter API程序代码。

Twitter爬虫程序源代码如下：

```
import urllib
import twurl
import json
import sqlite3

TWITTER_URL = 'https://api.twitter.com/1.1/friends/list.json'

conn = sqlite3.connect('spider.sqlite3')
cur = conn.cursor()

cur.execute("'
CREATE TABLE IF NOT EXISTS Twitter
(name TEXT, retrieved INTEGER, friends INTEGER)"')

while True:
    acct = raw_input('Enter a Twitter account, or quit: ')
    if ( acct == 'quit' ) : break
    if ( len(acct) < 1 ) :
        cur.execute('SELECT name FROM Twitter WHERE retrieved = 0 LIMIT 1')
        try:
            acct = cur.fetchone()[0]
        except:
            print 'No unretrieved Twitter accounts found'
            continue

    url = twurl.augment(TWITTER_URL,
               {'screen_name': acct, 'count': '20'} )
    print 'Retrieving', url
    connection = urllib.urlopen(url)
    data = connection.read()
    headers = connection.info().dict
    # print 'Remaining', headers['x-rate-limit-remaining']
    js = json.loads(data)
    # print json.dumps(js, indent=4)

    cur.execute('UPDATE Twitter SET retrieved=1 WHERE name = ?', (acct, ) )

    countnew = 0
    countold = 0
    for u in js['users'] :
        friend = u['screen_name']
        print friend
        cur.execute('SELECT friends FROM Twitter WHERE name = ? LIMIT 1',
            (friend, ) )
        try:
            count = cur.fetchone()[0]
            cur.execute('UPDATE Twitter SET friends = ? WHERE name = ?',
                (count+1, friend) )
            countold = countold + 1
        except:
            cur.execute("'INSERT INTO Twitter (name, retrieved, friends)
                VALUES ( ?, 0, 1 )"', ( friend, ) )
            countnew = countnew + 1
    print 'New accounts=',countnew,' revisited=',countold
    conn.commit()

cur.close()
```

数据库存在于spider.sqlite3文件中，包括一个Twitter表。Twitter表的每一行包括账号名、是否检索过这个账号的朋友以及这个账号被加好友的次数。

在程序的主循环中，提示用户输入一个Twitter账号名或退出程序。如果用户输入一个Twitter账号，程序就检索朋友列表和用户状态，如果数据库中没有这个朋友，则添加进去。如果该朋友已经存在于列表中，我们对friends字段值加一。

当用户按下回车键，在数据库中寻找下一个还未检索过的Twitter账号，检索该账号的朋友与状态，把添加他们到数据库，或更新它们，增加friends字段的统计值。

当获取到朋友列表与状态，我们对返回的JSON中所有的user数据项进行循环，检索每个用户的screen_name。然后，使用SELECT语句检查screen_name是否已经存储到数据库中了。如果记录存在的话，检索朋友数（friends字段）。

```
  countnew = 0
    countold = 0
    for u in js['users'] :
        friend = u['screen_name']
        print friend
        cur.execute('SELECT friends FROM Twitter WHERE name = ? LIMIT 1',
            (friend, ) )
        try:
            count = cur.fetchone()[0]
            cur.execute('UPDATE Twitter SET friends = ? WHERE name = ?',
                (count+1, friend) )
            countold = countold + 1
        except:
            cur.execute("'INSERT INTO Twitter (name, retrieved, friends)
                VALUES ( ?, 0, 1 )"', ( friend, ) )
            countnew = countnew + 1
    print 'New accounts=',countnew,' revisited=',countold
    conn.commit()
```

当游标执行SELECT语句，我们必须检索表的行。用for语句来实现，由于只检索了一行（LIMIT 1）,我们使用fetchone()方法获取第一（也是唯一）行，这就是SELECT操作的结果。由于fetchone()以元组返回行，即使仅有一个字段也是如此。我们用[0]取出元组的第一个值，得到变量count的当前朋友数。

如果获取成功，我们使用SQL的UPDATE语句和WHERE子句，对匹配到的朋友账号所在行的friends列值加一。请注意，SQL语句中有两个占位符（即问号）， execute()的第二个参数是两元素元组，其中的值会替换SQL的占位符。

如果try区块的代码失效，可能是因为SELECT语句的WHERE name = ?子句没有匹配到记录。在except区块，我们使用SQL的INSERT语句，向表中添加朋友的screen_name，另外一个指示符表示我们还没有获取到screen_name，这时将朋友数设为0。

第一次执行程序，输入一个Twitter账号，程序运行结果如下：

```
Enter a Twitter account, or quit: drchuck
Retrieving http://api.twitter.com/1.1/friends ...
New accounts= 20  revisited= 0
Enter a Twitter account, or quit: quit
```

由于是第一次执行这个程序，数据库是空的，我们创建了一个数据库文件spider.sqlite3，向数据库添加一张Twitter表。然后获取一些朋友，将他们的信息存储到之前空的数据库中。

此时，我们想要编写一个简单的数据库导出程序，用来查看spider.sqlites3文件：

```
import sqlite3

conn = sqlite3.connect('spider.sqlite3')
cur = conn.cursor()
cur.execute('SELECT * FROM Twitter')
count = 0
for row in cur :
   print row
   count = count + 1
print count, 'rows.'
cur.close()
```

如果再次执行Twitter爬虫程序，程序运行结果如下：

```
(u'opencontent', 0, 1)
(u'lhawthorn', 0, 1)
(u'steve_coppin', 0, 1)
(u'davidkocher', 0, 1)
(u'hrheingold', 0, 1)
...
20 rows.
```

我们看到每个screen_name有一行，没有获取该字段本身的数据，数据库中每人有一个朋友。

现在，在数据库里可以看到第一个Twitter账号（drchuck）的朋友已经获取到。我们再次执行这个程序，只需按下回车键，不用再输入Twitter账号，程序就会检索下一个“未处理账号”的朋友信息。

```
Enter a Twitter account, or quit:
Retrieving http://api.twitter.com/1.1/friends ...
New accounts= 18  revisited= 2
Enter a Twitter account, or quit:
Retrieving http://api.twitter.com/1.1/friends ...
New accounts= 17  revisited= 3
Enter a Twitter account, or quit: quit
```

由于我们按下了回车键（即没有指定Twitter账号），执行下面的代码：

```
if ( len(acct) < 1 ) :
    cur.execute('SELECT name FROM Twitter WHERE retrieved = 0 LIMIT 1')
    try:
        acct = cur.fetchone()[0]
    except:
        print 'No unretrieved twitter accounts found'
        continue
```

我们使用SQL的SELECT语句获取第一个用户的名称（LIMIT 1），但该用户的“是否访问过”字段值还是0。我们还在try/except区块中使用fetchone()[0]模式，从检索到的数据中抽取screen_name，或是得到一个错误消息和循环备份。

如果成功获取到一个未处理的screen_name，检索该账户数据的程序代码如下：

```
url = twurl.augment(TWITTER_URL, {'screen_name': acct, 'count': '20'} )
print 'Retrieving', url
connection = urllib.urlopen(url)
data = connection.read()
js = json.loads(data)

cur.execute('UPDATE Twitter SET retrieved=1 WHERE name = ?', (acct, ) )
```

一旦成功获取数据，我们使用UPDATE语句设置retrieved列值为1，表示已经完成对该账号的朋友检索。这保证了不会重复检索相同的数据，处理继续进行，最终形成Twitter朋友网络。

如果我们执行friend程序，按两次回车键，检索下一个未被访问的朋友的朋友，然后执行dumping程序，程序输出结果如下：

```
(u'opencontent', 1, 1)
(u'lhawthorn', 1, 1)
(u'steve_coppin', 0, 1)
(u'davidkocher', 0, 1)
(u'hrheingold', 0, 1)
...
(u'cnxorg', 0, 2)
(u'knoop', 0, 1)
(u'kthanos', 0, 2)
(u'LectureTools', 0, 1)
...
55 rows.
```

由此可见，我们正确记录了已经访问过的lhawthorn和opencontent两个账号的信息。另外，cnxorg和kthanos已经有了粉丝。由于已经检索了三个用户（drchuck、opencontent与lhawthorn）的朋友，表中已有55行。

每次执行程序与按下回车键时，它会选择下一个未访问的账号（这里的下一个账号是steve_coppin），获取他们的朋友，标记他们，循环steve_coppin的每一位朋友，将他们添加到数据库。如果他们已经存在于数据库，更新他们的朋友数。

由于程序的数据全部存储在数据库的磁盘上，爬虫活动可以被任意多次暂停或继续，数据都不会丢失。

## 14.7 基础数据建模

关系型数据库的真正实力在于，创建多个表以及表间连接。将应用数据分解为多个表并确立两个表间的关系，这一过程称为数据建模。显示表与表间关系的设计文档称为数据模型。

数据建模是相对复杂的技能，本节仅介绍最基础的关系型数据建模。数据建模的更多细节，详见以下维基页面：

<http://en.wikipedia.org/wiki/Relational_model>

让我们来看Twitter爬虫程序，不仅统计一个用户的朋友数，我们希望得到所有的入链关系，即找到特定账号的所有粉丝的列表。

由于每个人都可能会有许多粉丝，所以不能简单添加一列到Twitter表。因此，我们新建一个表来跟踪朋友对。下面是新建表的一种方法：

```
CREATE TABLE Pals (from_friend TEXT, to_friend TEXT)
```

每当遇到drchuck的一个粉丝，我们向表中插入一行：

```
INSERT INTO Pals (from_friend,to_friend) VALUES ('drchuck', 'lhawthorn')
```

当处理了drchuck的20个朋友的Twitter消息源（feed），我们插入“drchuck”作为第一参数的20条记录，这个字符串在数据库重复出现了多次。

重复的字符串数据破坏了数据库规范化的最佳实践。数据库规范化指相同的字符串数据在数据库只能存在一处。如果需要数据出现多次，要为数据创建一个数字键，通过该键引用实际的数据。

在实际应用中，字符串比整数在计算机磁盘与内存上占用更多空间，处理器也需要更多时间进行比较和排序。如果仅有几百条数据，那么存储与处理器耗时并没什么问题。但当数据库中包括百万用户，以及可能的一亿朋友链接，尽可能快速扫描数据就显得非常重要了。

我们把Twitter账号存储在People表，而不是之前示例的Twitter表。People表用额外的一列来存储与该Twitter用户的行数相关的数值键。SQLite的INTEGER PRIMARY KEY这一特殊的数据列类型能为插入的任一行自动增加键值。

新建People表，包括一个额外的id列：

```
CREATE TABLE People
(id INTEGER PRIMARY KEY, name TEXT UNIQUE, retrieved INTEGER)
```

请注意，我们不再维护People表每一行的朋友数。当选择INTEGER PRIMARY KEY作为id列的字段类型，这表明我们希望SQLite来管理该列，在插入一行时自动赋予唯一的数值键。我们还添加了关键词UNIQUE，表示不允许SQLite为两个行插入相同的值。

与之前创建的Pals表不同，我们创建了一个Follows表，包括from_id和to_id两个整数列，以及一个表级约束，表中from_id和to_id必须唯一，即不能在数据库中插入重复的行。

```
CREATE TABLE Follows
    (from_id INTEGER, to_id INTEGER, UNIQUE(from_id, to_id) )
```

向表中添加UNIQUE子句，当试图插入记录时，我们要求数据库强制执行这一套规则。在程序中创建这些规则的方便性稍后会说明。这些规则避免我们犯错误，并简化了一些代码编写。

从本质上说，Follows表的创建实际是构建了一个“关系”，一个用户是其他人的粉丝，将其表示成一个数值对，代表与他联系的用户，以及关系的方向。

![enter image description here](http://www.pythonlearn.com/html-270/book019.png)

## 14.8 多表编程

我们使用之前的两个表、主键和键引用来重做Twitter爬虫程序。新版本的程序代码如下：

```
import urllib
import twurl
import json
import sqlite3

TWITTER_URL = 'https://api.twitter.com/1.1/friends/list.json'

conn = sqlite3.connect('friends.sqlitesqlite3')
cur = conn.cursor()

cur.execute("'CREATE TABLE IF NOT EXISTS People
    (id INTEGER PRIMARY KEY, name TEXT UNIQUE, retrieved INTEGER)"')
cur.execute("'CREATE TABLE IF NOT EXISTS Follows
    (from_id INTEGER, to_id INTEGER, UNIQUE(from_id, to_id))"')

while True:
    acct = raw_input('Enter a Twitter account, or quit: ')
    if ( acct == 'quit' ) : break
    if ( len(acct) < 1 ) :
        cur.execute("'SELECT id, name FROM People
            WHERE retrieved = 0 LIMIT 1"')
        try:
            (id, acct) = cur.fetchone()
        except:
            print 'No unretrieved Twitter accounts found'
            continue
    else:
        cur.execute('SELECT id FROM People WHERE name = ? LIMIT 1',
            (acct, ) )
        try:
            id = cur.fetchone()[0]
        except:
            cur.execute("'INSERT OR IGNORE INTO People (name, retrieved)
                VALUES ( ?, 0)"', ( acct, ) )
            conn.commit()
            if cur.rowcount != 1 :
                print 'Error inserting account:',acct
                continue
            id = cur.lastrowid

    url = twurl.augment(TWITTER_URL,
       {'screen_name': acct, 'count': '20'} )
    print 'Retrieving account', acct
    connection = urllib.urlopen(url)
    data = connection.read()
    headers = connection.info().dict
    print 'Remaining', headers['x-rate-limit-remaining']

    js = json.loads(data)
    # print json.dumps(js, indent=4)

    cur.execute('UPDATE People SET retrieved=1 WHERE name = ?', (acct, ) )

    countnew = 0
    countold = 0
    for u in js['users'] :
        friend = u['screen_name']
        print friend
        cur.execute('SELECT id FROM People WHERE name = ? LIMIT 1',
            (friend, ) )
        try:
            friend_id = cur.fetchone()[0]
            countold = countold + 1
        except:
            cur.execute("'INSERT OR IGNORE INTO People (name, retrieved)
                VALUES ( ?, 0)"', ( friend, ) )
            conn.commit()
            if cur.rowcount != 1 :
                print 'Error inserting account:',friend
                continue
            friend_id = cur.lastrowid
            countnew = countnew + 1
        cur.execute("'INSERT OR IGNORE INTO Follows (from_id, to_id)
            VALUES (?, ?)"', (id, friend_id) )
    print 'New accounts=',countnew,' revisited=',countold
    conn.commit()

cur.close()
```

这个程序变得有些复杂了，介绍了通过整数键连接表格的使用模式。基本模式如下：

1. 创建带有主键与约束的表。
2. 当一个用户（即账号名称）拥有一个逻辑键，我们需要用户的id值。根据People表中是否有该用户，（1）查找People表中的用户，获取该用户的id值，或（2）向People表添加用户，为新增行添加id值。
3. 插入一行，表示“粉丝”关系。

以下依次介绍每一个步骤。

### 14.8.1 数据库表约束

设计表结构时，我们告诉数据库系统强制执行一些规则。这些规则帮助我们避免出错，不要把错误的数据写入表中。创建表的代码如下：

```
cur.execute("'CREATE TABLE IF NOT EXISTS People
    (id INTEGER PRIMARY KEY, name TEXT UNIQUE, retrieved INTEGER)"')
cur.execute("'CREATE TABLE IF NOT EXISTS Follows
    (from_id INTEGER, to_id INTEGER, UNIQUE(from_id, to_id))"')
```

我们定义People表中的name列必须是唯一的（UNIQUE）。同时，定义Follows表每一行两个数字的组合必须唯一。这些约束避免了多次添加同一个关系。

以下代码体现了这些约束的优势：

```
cur.execute("'INSERT OR IGNORE INTO People (name, retrieved)
    VALUES ( ?, 0)"', ( friend, ) )
```

我们在INSERT语句中添加OR IGNORE子句，这表示如果有一个INSERT违反了“name必须唯一”的规则，那么数据库将忽略这个INSERT。数据库约束作为一个安全网络，确保我们不会在无意中犯错。

同样地，以下代码确保不会重复添加同一个Follows关系。

```
cur.execute("'INSERT OR IGNORE INTO Follows
    (from_id, to_id) VALUES (?, ?)"', (id, friend_id) )
```

同样地，如果违反了Follows行的唯一性约束，只需告诉数据库忽略INSERT即可。

### 14.8.2 检索与插入一条记录

当提示用户输入一个Twitter账号，如果账号已存在，我们必须找到它的id值。如果People表中还没有该账号，我们必须插入一条记录，并得到该插入行的id值。

这是一个很常见的模式，在前面的程序中用到过2次。当我们从已获取的Twitter的JSON数据中获取user节点的screen_name，本节代码演示了如何检索一个朋友账号的id。

随着数据的累积，用户账号可能已经存在于数据库中。我们需要先使用SELECT语句，检查People表中该账号是或否存在。

如果try部分一切进展顺利[^2]，我们使用fetchone()获取该记录，然后检索返回的元组的第一个（也是唯一）元素，将其存储为friend_id。

如果SELECT执行失败，fetchone()[0]也会失败，然后控制跳转到except部分。

```
 friend = u['screen_name']
        cur.execute('SELECT id FROM People WHERE name = ? LIMIT 1',
            (friend, ) )
        try:
            friend_id = cur.fetchone()[0]
            countold = countold + 1
        except:
            cur.execute("'INSERT OR IGNORE INTO People (name, retrieved)
                VALUES ( ?, 0)"', ( friend, ) )
            conn.commit()
            if cur.rowcount != 1 :
                print 'Error inserting account:',friend
                continue
            friend_id = cur.lastrowid
            countnew = countnew + 1
```

如果以except代码结束，这意味着，没有发现记录，必须插入新行。我们使用INSERT OR IGNORE仅是避免出错，然后调用commit()来强制数据库对提交进行更新。当写入完成后，我们通过cur.rowcount检查有多少行受到影响。由于我们尝试插入一个单行，如果受影响行的数字不是1，那么将导致错误。

如果INSERT执行成功，我们通过cur.lastrowid找出数据库为新建行赋予的id列值。

### 14.8.3 存储朋友关系

一旦知道了JSON数据中Twitter用户与朋友的键值，在Follows表中插入两个值就是件简单的事情了，程序代码如下：

```
cur.execute('INSERT OR IGNORE INTO Follows (from_id, to_id) VALUES (?, ?)',
    (id, friend_id) )
```

请注意，根据表格创建时的唯一性约束，避免了重复插入同一个关系，然后在INSERT语句中添加OR IGNORE。

程序运行结果如下所示：

```
Enter a Twitter account, or quit:
No unretrieved Twitter accounts found
Enter a Twitter account, or quit: drchuck
Retrieving http://api.twitter.com/1.1/friends ...
New accounts= 20  revisited= 0
Enter a Twitter account, or quit:
Retrieving http://api.twitter.com/1.1/friends ...
New accounts= 17  revisited= 3
Enter a Twitter account, or quit:
Retrieving http://api.twitter.com/1.1/friends ...
New accounts= 17  revisited= 3
Enter a Twitter account, or quit: quit
```

从drchuck账号开始，让程序自动获取下两个账号，并添加到数据库。

当程序执行完成后，以下是People和Follows表的头几行：

```
People:
(1, u'drchuck', 1)
(2, u'opencontent', 1)
(3, u'lhawthorn', 1)
(4, u'steve_coppin', 0)
(5, u'davidkocher', 0)
55 rows.
Follows:
(1, 2)
(1, 3)
(1, 4)
(1, 5)
(1, 6)
60 rows.
```

可以看出，People表中的id、name与visited字段，关系Follows表结尾的数字。在People表中，我们看到前三个用户已经被访问过，他们的数据已被获取。Follows表的数据表明，drchuck（用户1）是所显示前五行的用户的朋友。这是由于我们获取和存储的第一个数据是drchuck的Twitter朋友。如果打印出Follows表的更多行，就会看到用户2和用户3的朋友。

## 14.9 键的三种类型

现在我们已经开始构建数据模型了，将数据放入多个关联表中，使用键来连接这些表中的行。我们需要知道一些有关键的术语。数据库模型中一般存在三种类型的键。

* 逻辑键是“真实世界”中可以检索行的键。在示例数据模型中，name字段是一个逻辑键。它是用户的屏幕名称，在程序中通过name字段多次检索用户的行。你会发现，对一个逻辑键添加UNIQUE约束，这是有道理的。由于逻辑键是我们从“外部世界”如何检索表中的一行，允许表中存在相同值的多行没有多大意义。

* 主键通常是由数据库自动赋予的一个数字。它对外部程序而言没有意义，仅用于把来自不同表的行连接在一起。当我们检索表中的行，通常搜索主键是最快的。由于主键是整数值，占用极少的存储空间，能够快速进行比较与排序。在示例数据模型中，id字段是主键。

* 外键通常是指向不同表中相关行的主键的一个数值。示例数据模型中的外键是from_id。

我们使用一些命名惯例，比如主键名为id，那么将_id后缀添加到对应外键的名称中。

## 14.10 使用JSON获取数据

我们已经了解了数据库规范化原则，将数据分成两个表，通过主键和外键将两个表连接起来，然后通过SELECT将跨表格的数据组装在一起。

SQL使用连接（JOIN）子句把表重新连接。在JOIN子句中可以指定用以连接表之间行的字段。

下面是带有JOIN子句的SELECT语句示例：

```
SELECT * FROM Follows JOIN People
    ON Follows.from_id = People.id WHERE People.id = 1
```

JOIN子句表示，从Follows与People两个表中选择所有字段。ON子句表示，两个表怎样被连接在一起。选取People表的行，然后将Follows表的from_id字段与People表的id字段值相同的行附加在后面。

![enter image description here](http://www.pythonlearn.com/html-270/book020.png)

连接的结果是创建了一个相当长的“元行”（meta-rows），包括People表的字段与Follows表中匹配的字段。由于People表的id字段与Follows表中的from_id字段之间存在多个匹配，JOIN会为每一个匹配到的行创建一个元行，根据需要重复数据。

多表数据库驱动的Twitter爬虫程序多次执行的代码如下：

```
import sqlite3

conn = sqlite3.connect('spider.sqlite3')
cur = conn.cursor()

cur.execute('SELECT * FROM People')
count = 0
print 'People:'
for row in cur :
   if count < 5: print row
   count = count + 1
print count, 'rows.'

cur.execute('SELECT * FROM Follows')
count = 0
print 'Follows:'
for row in cur :
   if count < 5: print row
   count = count + 1
print count, 'rows.'

cur.execute("'SELECT * FROM Follows JOIN People
    ON Follows.from_id = People.id WHERE People.id = 2"')
count = 0
print 'Connections for id=2:'
for row in cur :
   if count < 5: print row
   count = count + 1
print count, 'rows.'

cur.close()
```

在这个程序中，我们首先整体导出People与Follows表，然后导出连接表的数据子集。

程序输出结果如下：

```
python twjoin.py
People:
(1, u'drchuck', 1)
(2, u'opencontent', 1)
(3, u'lhawthorn', 1)
(4, u'steve_coppin', 0)
(5, u'davidkocher', 0)
55 rows.
Follows:
(1, 2)
(1, 3)
(1, 4)
(1, 5)
(1, 6)
60 rows.
Connections for id=2:
(2, 1, 1, u'drchuck', 1)
(2, 28, 28, u'cnxorg', 0)
(2, 30, 30, u'kthanos', 0)
(2, 102, 102, u'SomethingGirl', 0)
(2, 103, 103, u'ja_Pac', 0)
20 rows.
```

可以看出，People与Follows表的列和带JOIN子句的SELECT语句执行后得到的结果行。

最后一次选择中我们找到“opencontent”（即People.id=2）的朋友账号。

在最后一次选择的每个“元行”中，前两列来自Follows表，之后是People表5个列中的3个。连接后的每个元行中第2列（Follows.to_id）匹配第3列（People.id）。

## 14.11 小结

本章全面介绍了Python中数据库的基本使用方法。与Python字典或平面文件相比，编写代码来使用数据库存储数据更加复杂。除非你的应用程序确实需要数据库功能，否则不要轻易使用。数据库的使用优势在于：（1）应用程序需要在大量数据中随机更新一小部分数据；（2）数据量很大，无法用字典来存储，而且需要重复检索信息；（3）在长期运行过程中希望停止或重启，数据可以得以保留，并在下次执行时从中止处继续。

你可以创建单表的简单数据库，以满足多种应用需求。但是，大多数问题都需要多个表与跨表的行之间的链接/关系。为表创建链接时，需要进行周全设计，遵循数据库规范化原则，恰当地运用数据库能力。数据库的使用动机主要需要处理大量数据，有效的数据建模让程序能快速执行，把握住这一点很重要。

## 14.12 调试

一个常见的Python程序开发模式是与SQLite数据建立联系，执行Python程序，使用SQLite数据库浏览器查看结果。这个浏览器可以快速检查程序是否正常执行。

由于SQLite在同一时刻会防止两个程序对同一数据的修改。例如，如果在浏览器中打开数据库，修改数据库，在尚未按下保存按钮时，浏览器会锁定数据库文件，以防止其他程序访问该文件。具体而言，如果数据库文件被锁定，你的Python程序将不能访问这个文件。

一个解决方法是，在Python尝试访问数据库之前，关闭数据库浏览器或使用浏览器的文件菜单来关闭数据库。这样可以避免数据库锁定导致的Python代码运行失败问题。

## 14.13 术语

**属性：**元组的一个值。更常见的提法是列或字段。

**约束：**告知数据库在表中的字段或行上执行规则。一个常见的约束是保证特定字段上无重复值（即所有值必须唯一）。

**游标：**执行数据库的SQL命令，并从数据库中获取数据。游标类似于网络连接中的套接字或文件读取的文件句柄。

**数据库浏览器：**一种无需编写程序，直接与数据库连接并进行操作的软件。

**外键：**指向另一个表中行的主键的数值键。外键建立了不同表中行之间的关系。

**索引：**向表中插入行时，数据库软件由于维护需要而产生的额外数据，目的是提高查询速度。

**逻辑键：**外部世界用来检索特定行的键。例如，在用户信息表中用户的电子邮件地址是不错的用户数据候选逻辑键。

**规范化：**数据模型设计要保证无重复的数据。每个数据项只存储在数据库的一个位置，其他地方用外键来引用。

**主键：**每一行指定的数值键，用于当前表中的行与另一个表中的行之间建立引用关系。数据库的默认配置会自动为插入的行赋予主键。

**关系：**数据库中包含元组与属性的一块区域。更典型的提法是“表”。

**元组：**数据库中表的一个数据条目，包含一组属性。更典型的提法是“行”。

[^1]: 实际上，SQLite在列中存储的数据类型具备一定灵活性，但本章中严格定义数据类型，这样做让这些概念也能适用于其他数据库系统（如MySQL）。

[^2]: 一般来说，以“如果一切顺利”开头的话，你会发现需要使用try/except。
