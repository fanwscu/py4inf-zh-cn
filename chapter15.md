# 第15章 数据可视化

至此，我们认识了Python语言，学习了Python的基本用法、网络连接，以及使用数据库操作数据等知识。

本章介绍三个完整的应用程序，将之前学到的知识整合起来，对数据进行管理与可视化。你可以使用这些程序代码来解决现实问题。

每个应用程序是一个ZIP文件，可以下载和解压到本地计算机上运行。

## 15.1 根据地理编码数据创建Google地图应用

这个项目使用Google的地理编码API来清洗用户输入的大学地名，然后将这些数据显示在Google地图上。

![enter image description here](http://www.pythonlearn.com/html-270/book021.png)

从以下地址下载应用程序：

http://www.py4inf.com/code/geodata.zip

首先要解决的问题是，免费的Google地理编码API对每天的请求数量有一定限制。如果数据过多，可能需要在查询过程中多次停止与重启。这里，我们把问题分解成两个阶段。

第一阶段，按行读入where.data文件中的调查数据，通过Google获取地理编码信息，将其存储在geodata.sqlite数据库。在对每个用户输入的地名使用地理编码API之前，我们需要简单检查下输入的行是否有数据存在。数据库具有本地缓存功能，可以对地理编码数据进行缓存，这样就无需向Google重复请求同一数据。

移除geodata.sqlite文件之后，你可以选择任何时候重启这个过程。执行geoload.py程序，依次读入where.data文件的每一行，检查该数据在数据库是否存在。若不存在，调用地理编码API获取该数据库，将其存储在数据库中。

下面是数据库已有一些数据的情况，程序运行结果如下：

```
Found in database  Northeastern University
Found in database  University of Hong Kong, ...
Found in database  Technion
Found in database  Viswakarma Institute, Pune, India
Found in database  UMD
Found in database  Tufts University

Resolving Monash University
Retrieving http://maps.googleapis.com/maps/api/
    geocode/json?sensor=false&address=Monash+University
Retrieved 2063 characters {    "results" : [
{u'status': u'OK', u'results': ... }

Resolving Kokshetau Institute of Economics and Management
Retrieving http://maps.googleapis.com/maps/api/
    geocode/json?sensor=false&address=Kokshetau+Inst ...
Retrieved 1749 characters {    "results" : [
{u'status': u'OK', u'results': ... }
...
```

前5个地名已经存在于数据库中，所以它们被跳过。程序会扫描到未检索过的地名，然后开始获取数据。

geoload.py可以随时停止，还有一个计数器用来控制每次运行中地理编码API的调用上限。由于where.data只包括几百个数据，所以无需设置日访问限制。如果数据量很大，需要几天时间的多次运行才能获取所有的地理编码数据，这种情况下就需要设置访问限制了。

载入一些数据到geodata.sqlite之后，你可以使用geodump.py程序对数据进行可视化。此程序会读取数据库，将地名、经度、纬度转换成可执行的JavaScript代码形式，写入where.js文件。

geodump.py运行结果如下：

```
Northeastern University, ... Boston, MA 02115, USA 42.3396998 -71.08975
Bradley University, 1501 ... Peoria, IL 61625, USA 40.6963857 -89.6160811
...
Technion, Viazman 87, Kesalsaba, 32000, Israel 32.7775 35.0216667
Monash University Clayton ... VIC 3800, Australia -37.9152113 145.134682
Kokshetau, Kazakhstan 53.2833333 69.3833333
...
12 records written to where.js
Open where.html to view the data in a browser
```

where.html文件包含了Google地图可视化所需的HTML与JavaScript代码。它读入where.js文件中的最新数据，将其可视化。where.js文件格式如下：

```
myData = [
[42.3396998,-71.08975, 'Northeastern Uni ... Boston, MA 02115'],
[40.6963857,-89.6160811, 'Bradley University, ... Peoria, IL 61625, USA'],
[32.7775,35.0216667, 'Technion, Viazman 87, Kesalsaba, 32000, Israel'],
   ...
];
```

这个JavaScript变量是一个包含列表的列表。JavaScript列表常量的语法与Python非常相似，你应该不会感到陌生。

在浏览器中打开where.html来查看地图。鼠标悬浮在地图标记点上可以看到地理编码API对应的用户输入的地名。如果打开where.html文件看不到数据，你可能需要检查浏览器的JavaScript或在开发者控制台进行排查。

## 15.2 网络与互联可视化

这个应用程序实现了搜索引擎的一些功能。首先，爬取一部分网页集合，然后实现了一个Google的PageRank算法简化版，确定哪些页面具有高连接度，最后，在这个小网络中对页面等级与连接度进行可视化。

下载和解压这个应用程序：

<http://www.py4inf.com/code/pagerank.zip>

![enter image description here](http://www.pythonlearn.com/html-270/book022.png)

首先，spider.py程序爬取一个网站，将网站的页面存储到spider.sqlite数据库，记录页面之间的链接。移除spider.sqlite文件之后，你可以随时再次执行spider.py。

```
Enter web url or enter: http://www.dr-chuck.com/
['http://www.dr-chuck.com']
How many pages:2
1 http://www.dr-chuck.com/ 12
2 http://www.dr-chuck.com/csev-blog/ 57
How many pages:
```

在程序运行中，我们告知它爬取一个网站，检索两个页面。如果你重启程序，可以让它爬取更多页面，它不会重复爬取数据库已有的页面。程序重启会随机检索未爬取的页面，然后从那里开始。因此，spider.py程序的每一次运行都是累积式的。

```
Enter web url or enter: http://www.dr-chuck.com/
['http://www.dr-chuck.com']
How many pages:3
3 http://www.dr-chuck.com/csev-blog 57
4 http://www.dr-chuck.com/dr-chuck/resume/speaking.htm 1
5 http://www.dr-chuck.com/dr-chuck/resume/index.htm 13
How many pages:
```

同一个数据库中可以有多个起点，在程序中称为”网络“。该爬虫程序随机选择网络中未访问的链接，作为下一个网页进行爬取。

如果要导出spider.sqlite文件内容，spdump.py程序运行结果如下：

```
(5, None, 1.0, 3, u'http://www.dr-chuck.com/csev-blog')
(3, None, 1.0, 4, u'http://www.dr-chuck.com/dr-chuck/resume/speaking.htm')
(1, None, 1.0, 2, u'http://www.dr-chuck.com/csev-blog/')
(1, None, 1.0, 5, u'http://www.dr-chuck.com/dr-chuck/resume/index.htm')
4 rows.
```

以上显示了入链的数目、旧的页面排名、新的页面排名、页面id和页面的url。spdumpy程序只显示至少有一个入链的页面。

当数据库里已经保留一些页面数据之后，执行sprank.py程序来实现页面排名。你只需指定页面排名的迭代次数即可。

```
How many iterations:2
1 0.546848992536
2 0.226714939664
[(1, 0.559), (2, 0.659), (3, 0.985), (4, 2.135), (5, 0.659)]
```

再次导出数据库，查看页面排名的更新情况：

```
(5, 1.0, 0.985, 3, u'http://www.dr-chuck.com/csev-blog')
(3, 1.0, 2.135, 4, u'http://www.dr-chuck.com/dr-chuck/resume/speaking.htm')
(1, 1.0, 0.659, 2, u'http://www.dr-chuck.com/csev-blog/')
(1, 1.0, 0.659, 5, u'http://www.dr-chuck.com/dr-chuck/resume/index.htm')
4 rows.
```

sprank.py可以执行多次，它会在每次执行时优化页面排名。你可以执行几次sprank.py，然后用spider.py爬取一些页面，再执行sprank.py来收敛网页排名值。搜索引擎一般会同时运行爬取程序与排名程序。

如果在没有重新爬取网页的情况下再次计算网页排名，你可以用sprest.py程序重置，然后重启sprank.py。

```
How many iterations:50
1 0.546848992536
2 0.226714939664
3 0.0659516187242
4 0.0244199333
5 0.0102096489546
6 0.00610244329379
...
42 0.000109076928206
43 9.91987599002e-05
44 9.02151706798e-05
45 8.20451504471e-05
46 7.46150183837e-05
47 6.7857770908e-05
48 6.17124694224e-05
49 5.61236959327e-05
50 5.10410499467e-05
[(512, 0.0296), (1, 12.79), (2, 28.93), (3, 6.808), (4, 13.46)]
```

对于PageRank算法的每次迭代，它会打印出每个页面排名的平均变化。该网络在初始状态非常不均衡，这是由于单个页面排名值在迭代过程中变化很大。经过一些迭代之后，页面排名开始收敛了。执行prank.py足够长时间之后，网页排名值就会相对稳定。

如果想要对网页排名中当前靠前的页面进行可视化，执行spjson.py程序，读取数据库，将最高连接页面的数据转换为JSON格式，可以在网络浏览器中查看效果。

```
Creating JSON output on spider.json...
How many nodes? 30
Open force.html in a browser to view the visualization
```

在网络浏览器中打开force.html来查看此数据。这是一个自动布局的包含节点与链接的网络。你可以点击和拖拽任一节点，也可以双击节点，查看该节点的URL。

如果重新运行其他工具，重新执行spjson.py，在浏览器中点击刷新，显示spider.json得到的新数据。

## 15.3 邮件数据可视化

读到这里，你应该还记得mbox-short.txt与mbox.txt这两个数据文件。下面我们将深入分析这些电子邮件数据。

在现实世界中，有时你需要从服务器下载邮件数据，这可能要花费相当长时间，数据可能会存在不一致，充满错误和需要做大量清洗与调整工作。本节介绍的程序是截至目前最复杂的，从服务器下载近1个G大小的数据，然后对其可视化。

![enter image description here](http://www.pythonlearn.com/html-270/book023.png)

下载应用程序代码：

<http://www.py4inf.com/code/gmane.zip>

这里使用<http://www.gmane.org>的免费电子邮件列表归档服务。由于该服务提供了电子邮件活动的归档，数据质量高且可搜索，所以在开源项目中非常流行。它的数据API访问政策也比较宽松，没有访问限制，但请不要过度使用，仅获取你需要的数据即可。

你可以在下面的网页中阅读gmane的条款与条件：

<http://gmane.org/export.php>

*使用gmane.org数据时需要考虑，在访问服务和长时间运行任务时，有义务添加延时，这一点非常重要。不要滥用这项免费服务，避免累及他人。*

使用该软件爬取Sakai电子邮件数据时，可能会产生约1个G的数据，运行需要花费几天时间。下载的压缩包里有一个README.txt文件，提供如何下载一个已爬取的content.sqlite副本的操作指南，无需花费5天时间执行程序来爬取数据。contet.sqlite包含了主要的Sakai电子邮件语料库。如果下载了预先爬行好的内容，你仍然可以执行这个爬取进程，以获取最新的消息。

第一步是爬取gmane归档库。在gmane.py中基础URL是硬编码的，被硬编码为Sakai开发者列表。通过修改基础URL可以爬取其他归档库。如果修改了基础URL，请确保删除content.sqlite文件。

gmane.py文件作为一个“负责任”的缓存型爬虫，有条不紊运行，每秒检索一条邮件信息，这样避免被gmane封掉。它把所有数据存储在数据库，根据需要可以多次中断和重启。数据下载可能需要花费几个小时。因此，程序运行中可能需要重启几次。

gmane.py程序获取到Sakai开发者列表最后5条消息如下所示：

```
How many messages:10
http://download.gmane.org/gmane.comp.cms.sakai.devel/51410/51411 9460
    nealcaidin@sakaifoundation.org 2013-04-05 re: [building ...
http://download.gmane.org/gmane.comp.cms.sakai.devel/51411/51412 3379
    samuelgutierrezjimenez@gmail.com 2013-04-06 re: [building ...
http://download.gmane.org/gmane.comp.cms.sakai.devel/51412/51413 9903
    da1@vt.edu 2013-04-05 [building sakai] melete 2.9 oracle ...
http://download.gmane.org/gmane.comp.cms.sakai.devel/51413/51414 349265
    m.shedid@elraed-it.com 2013-04-07 [building sakai] ...
http://download.gmane.org/gmane.comp.cms.sakai.devel/51414/51415 3481
    samuelgutierrezjimenez@gmail.com 2013-04-07 re: ...
http://download.gmane.org/gmane.comp.cms.sakai.devel/51415/51416 0

Does not start with From
```

该程序扫描content.sqlite，从1开始，直到找到未被爬取的消息的序号，然后开始爬取那条消息。直到爬取到需要的消息序号或者访问到一个不符合消息格式的页面，程序终止。

有时候，gmane.org的消息可能不全，可能是管理员被删除了或消息被弄丢了。如果爬虫停止，可能是它碰到一条丢失的消息。打开SQLite管理器，添加一个丢失的id，其他字段留空，然后重启gmane.py。这样爬取进程就可以继续了。这些空消息在下阶段处理时会被忽略。

一旦爬取了所有消息并将它们存储在content.sqlite，你可以再次执行gmane.py来获取邮件列表上新发布的消息。这听来不错。

content.sqlite的数据缺乏有效的数据模型和未被压缩，显得相当原始。这样做是有意的，它可以让你在SQLite管理器中查看content.sqlite，在爬取过程中调试问题。如果想要对这个数据库进行查询，这可不是个好主意，效率会非常低。

第二阶段是执行gmodel.py程序。该程序从content.sqlite读入原始数据，进行数据清理与建模，生成index.sqlite文件。由于压缩了标题和正文，index.sqlite比content.sqlite文件体积一般要小十倍。

每次执行gmodel.py，它都会删除和重建index.sqlite，允许调整参数和编辑content.sqlite里的映射表，从而控制数据清洗过程。以下是gmodel.py的执行情况示例。每处理250条邮件消息之后打印一行，这样可以观察到一些程序执行情况。该程序会运行一段时间，期间处理近1个G的电子邮件数据。

```
Loaded allsenders 1588 and mapping 28 dns mapping 1
1 2005-12-08T23:34:30-06:00 ggolden22@mac.com
251 2005-12-22T10:03:20-08:00 tpamsler@ucdavis.edu
501 2006-01-12T11:17:34-05:00 lance@indiana.edu
751 2006-01-24T11:13:28-08:00 vrajgopalan@ucmerced.edu
...
```
gmodel.py程序主要是执行一些数据清理任务。

域名.com、.org、.edu和.net被截断成2节，其他域名被分为3节。因此，si.umich.edu处理为umich.edu，caret.cam.ac.uk处理为cam.ac.uk。另外，电子邮件地址全部转为小写。一些@gmane.org地址，如下所示：

```
 arwhyte-63aXycvo3TyHXe+LvDLADg@public.gmane.org
```

这样的邮件地址如果与语料库中的真实电子邮件地址匹配，就会被转换为真实地址。

content.sqlite数据库包括两个表，允许域名与个人电子邮件（可能会发生变化）之间进行映射。例如，在Sakai开发者列表中，Steve Githens由于更换了工作，使用以下电子邮件地址：

```
s-githens@northwestern.edu
sgithens@cam.ac.uk
swgithen@mtu.edu
```

我们在contente.sqlite的Mapping表中添加两条数据，这样gmodel.py就可以将3个电子邮件地址映射为一个地址：

```
s-githens@northwestern.edu ->  swgithen@mtu.edu
sgithens@cam.ac.uk -> swgithen@mtu.edu
```

如果多个DNS名需要映射到一个DNS上，你也可以在DNSMapping表中做类似添加。如下映射添加到Sakai数据中：

```
iupui.edu -> indiana.edu
```

这样，所有印第安纳大学的校园账号就可以集中跟踪了。

反复执行gmodel.py来查看数据，通过添加映射让数据更加干净。程序一旦完成，你会得到一个电子邮件的索引版本，即index.sqlite。这个数据库文件用于数据分析非常快。

首先，做两个简单的数据分析：“谁发送邮件最多？”和“哪个组织发送邮件最多？”。使用gbasic.py实现：

```
How many to dump? 5
Loaded messages= 51330 subjects= 25033 senders= 1584

Top 5 Email list participants
steve.swinsburg@gmail.com 2657
azeckoski@unicon.net 1742
ieb@tfd.co.uk 1591
csev@umich.edu 1304
david.horwitz@uct.ac.za 1184

Top 5 Email list organizations
gmail.com 7339
umich.edu 6243
uct.ac.za 2451
indiana.edu 2258
unicon.net 2055
```

请注意，与gmane.py和gmodel.py相比，gbasic.py处理数据非常快。虽然它们都在相同的数据上工作，但gbasic.py使用index.sqlite中压缩过和规范化的数据。如果有大量数据需要管理，本节示例应用程序采用的多步处理可能多用了一些开发时间，但在数据探索与可视化时节省了大量时间。

gword.py实现了主题行词频的简单可视化：

```
Range of counts: 33229 129
Output written to gword.js
```

gword.py执行后生成gword.js文件，通过gword.htm进行可视化，生成一个类似本节开头的词云。

第二个可视化用gline.py生成。它计算了一段时间内某组织的电子邮件参与情况。

```
Loaded messages= 51330 subjects= 25033 senders= 1584
Top 10 Oranizations
['gmail.com', 'umich.edu', 'uct.ac.za', 'indiana.edu',
'unicon.net', 'tfd.co.uk', 'berkeley.edu', 'longsight.com',
'stanford.edu', 'ox.ac.uk']
Output written to gline.js
```

gline.py执行后生成gline.js文件，通过gline.htm进行可视化。

![enter image description here](http://www.pythonlearn.com/html-270/book024.png)

以上是一个相对复杂的高级应用程序，具备一些数据检索、清洗与可视化功能。
