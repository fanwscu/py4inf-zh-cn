# 第12章 网络编程

虽然书中许多示例侧重于读取文件和检索文件中的数据，而如今的互联网上有丰富的信息来源，值得考虑在内。

在本章中，我们伪装成一个网络浏览器，使用超文本传输协议（HyperText Transport Protocol, HTTP）检索网页，读取页面数据并进行解析。

## 12.1 超文本传输协议 — HTTP

网络协议驱动了整个网络，其本身非常简单。由于Python内置了sockets库，在Python程序中建立网络连接，通过这些套接字检索数据，变得非常容易。

套接字很像文件。不同的是它提供了两个程序之间的双向连接，在一个套接字上可以同时读取和写入。如果你在套接字的一端编写内容，套接字会把数据发送给另一端的应用程序。如果从套接字读取，将得到另一个程序发送的数据。

然而，当套接字另一端没有发送任何数据时，如果你尝试读取套接字，结果就只能等待。如果套接字两端的程序都在等待数据，而不发送任何数据，它们就会这样僵持下去。

程序的一个重要组成部分是与互联网的通讯，即具备某种协议。协议是一组精确规则的集合，决定了谁先谁后，做些什么，对消息如何响应，以及下一步谁来发送等。从某种意义上说，套接字两端的两个程序像是在跳舞，确保不会踩到对方的脚趾。

已有大量文档介绍网络协议。超文本传输协议原文如下：

<http://www.w3.org/Protocols/rfc2616/rfc2616.txt>

这个文档包含179页，内容复杂详尽。如果你感兴趣，请读完它。如果仅是翻看RFC2616的第36页左右，你会找到GET请求的语法。如果仔细阅读，你会发现是从网络服务器请求文档，与<http://www.py4inf.com>服务器的80端口建立连接，然后发送表单的一行。

```
GET http://www.py4inf.com/code/romeo.txt HTTP/1.0
```

第二个参数是我们请求的网页，随后我们发送一个空白行。网络服务器将响应文档的一些头部信息和文档内容之后的空白行。

## 12.2 世界上最简单的网络浏览器

解释HTTP工作原理的最简单方法，也许就是编写一段非常简单的Python程序。与网络服务器建立连接，遵循HTTP协议规则，向服务器请求文档并显示出来。

```
import socket

mysock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
mysock.connect(('www.py4inf.com', 80))
mysock.send('GET http://www.py4inf.com/code/romeo.txt HTTP/1.0\n\n')

while True:
    data = mysock.recv(512)
    if ( len(data) < 1 ) :
        break
    print data

mysock.close()
```

首先，程序与服务器<http://www.py4inf.com>在80端口建立一个连接，这个程序扮演了网络浏览器的角色。HTTP协议要求，必须发送GET命令，并在后面跟一个空白行。

![enter image description here](http://www.pythonlearn.com/html-270/book013.png)

发送空白行之后，我们编写一个循环，从套接字中接收512个字符的数据片段，并打印出这些数据，直到没有数据可以读入，即recv()返回一个空字符串。

程序运行结果如下：

```
HTTP/1.1 200 OK
Date: Sun, 14 Mar 2010 23:52:41 GMT
Server: Apache
Last-Modified: Tue, 29 Dec 2009 01:31:22 GMT
ETag: "143c1b33-a7-4b395bea"
Accept-Ranges: bytes
Content-Length: 167
Connection: close
Content-Type: text/plain

But soft what light through yonder window breaks
It is the east and Juliet is the sun
Arise fair sun and kill the envious moon
Who is already sick and pale with grief
```

程序一开始输出的是网络服务器发送的文档描述的头部信息。例如，Content-Type头部指明文档是一个普通文本文档（text/plain）。

服务器发送了头部信息之后，添加一个空白行表示头部信息发送完毕，然后发送实际的romeo.txt文件数据。

示例展示了如何通过套接字建立低级别的网络连接。套接字用于网络服务器、邮件服务器以及其他许多类型服务器的通讯。找到描述协议的文档，编写代码，根据协议发送和获取数据，要做的就这么多了。

由于最常用的协议是HTTP（即Web）协议，Python针对HTTP协议设计了专门的库来支持网络文档数据的获取。

## 12.3 通过HTTP检索图像

在以上示例中，我们获取了一个包含换行符的普通文本文件，程序运行时简单地拷贝数据并显示出来。接下来，我们使用HTTP编写一个类似的程序，用来检索图片。在一个字符串中累积数据，截取头部信息，然后将图片数据保存到一个文件中，程序代码如下：

```
import socket
import time

mysock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
mysock.connect(('www.py4inf.com', 80))
mysock.send('GET http://www.py4inf.com/cover.jpg HTTP/1.0\n\n')


count = 0
picture = "";
while True:
    data = mysock.recv(5120)
    if ( len(data) < 1 ) : break
    # time.sleep(0.25)
    count = count + len(data)
    print len(data),count
    picture = picture + data

mysock.close()

# Look for the end of the header (2 CRLF)
pos = picture.find("\r\n\r\n");
print 'Header length',pos
print picture[:pos]

# Skip past the header and save the picture data
picture = picture[pos+4:]
fhand = open("stuff.jpg","wb")
fhand.write(picture);
fhand.close()
```

程序运行结果如下：

```
$ python urljpeg.py
2920 2920
1460 4380
1460 5840
1460 7300
...
1460 62780
1460 64240
2920 67160
1460 68620
1681 70301
Header length 240
HTTP/1.1 200 OK
Date: Sat, 02 Nov 2013 02:15:07 GMT
Server: Apache
Last-Modified: Sat, 02 Nov 2013 02:01:26 GMT
ETag: "19c141-111a9-4ea280f8354b8"
Accept-Ranges: bytes
Content-Length: 70057
Connection: close
Content-Type: image/jpeg
```

针对这个url，Content-Type头部指明文档本身是一个图像（img/jpeg）。程序运行完毕之后，使用图像浏览器打开stuff.jpg文件查看图像数据。

程序运行中调用了recv()函数，每次不会得到5120个字符。调用recv()时，通过网络，我们从网络服务器获得更多字符串。在这个示例中，每一次获得1460或2920个字符，请求上限是5120个字符。

网速不同会导致不同的结果。还要注意的是，最后一次调用recv()，在数据流结束时得到1681个字节，再下一个recv()调用获得零长度的字符串。这就是告诉我们，服务器已经在套接字末尾调用了close()，没有更多数据可发送了。

把time.sleep()前面的注释去掉，这样可以减缓随后的调用。这样一来，每次相隔1/4秒，服务器让我们“靠前”，发送更多的数据。程序的延时间隔执行如下所示：

```
$ python urljpeg.py
1460 1460
5120 6580
5120 11700
...
5120 62900
5120 68020
2281 70301
Header length 240
HTTP/1.1 200 OK
Date: Sat, 02 Nov 2013 02:22:04 GMT
Server: Apache
Last-Modified: Sat, 02 Nov 2013 02:01:26 GMT
ETag: "19c141-111a9-4ea280f8354b8"
Accept-Ranges: bytes
Content-Length: 70057
Connection: close
Content-Type: image/jpeg
```

这次运行中除了第一次和最后一次recv()调用，每次请求新数据都会得到5120个字符。

服务器生成的send()请求与程序生成的recv()请求之间存在一个缓冲区。当程序执行延迟请求时，在某些点上，服务器可能会在套接字中填满缓冲区，并强制暂停，直到程序开始清空缓存区。发送应用或接收应用的暂停行为被称为“流量控制”。

## 12.4 使用urllib检索网页

与通过HTTP 套接字库手动发送与获取数据相比，Python中有一种更简单的解决方法，使用urllib库。

使用urllib，你可以将网页看成一个文件。只需简单指明需要检索的网页，urllib会处理所有HTPP协议和头部细节。

使用urllib读取romeo.txt文件的代码如下：

```
import urllib

fhand = urllib.urlopen('http://www.py4inf.com/code/romeo.txt')
for line in fhand:
   print line.strip()
```

网页通过urllib.urlopen打开后，我们就可以把它当成一个文件，使用for循环来读取。

程序运行时，我们仅看到文件内容的输出。虽然头部信息仍然会发送，但是urllib代码会处理头部，发送给我们的仅是文件内容。

```
But soft what light through yonder window breaks
It is the east and Juliet is the sun
Arise fair sun and kill the envious moon
Who is already sick and pale with grief
```

为了演示说明，我们编写一个程序来获取romeo.txt的数据，计算文件中每个单词的频率，代码如下所示：

```
import urllib

counts = dict()
fhand = urllib.urlopen('http://www.py4inf.com/code/romeo.txt')
for line in fhand:
    words = line.split()
    for word in words:
        counts[word] = counts.get(word,0) + 1
print counts
```

同样地，一旦打开了网页，我们就可以把它当做一个本地文件进行读取。

## 12.5 解析HTML和Web抓取

Python的urllib常见用法之一是网页抓取。网页抓取是编写一个程序，伪装成网络浏览器，检索网页，然后在这些页面中根据模式检视数据。

举例来说，搜索引擎（如Google）会查看网页的源代码，抽取链接到其他页面的超链接，然后检索这些页面，抽取超链接，如此往复下去。使用这种技术，Google爬虫几乎遍历了网络上的所有页面。

Google还使用页面链接的频次来说明一个具体页面的重要性，在搜索结果中表明页面排位的高低。

## 12.6 使用正则表达式解析HTML

HTML解析的一个简单方法是使用正则表达式进行重复搜索，根据特定模式，抽取出与之匹配的子字符串。

以下是一个简单的网页：

```
<h1>The First Page</h1>
<p>
If you like, you can switch to the
<a href="http://www.dr-chuck.com/page2.htm">
Second Page</a>.
</p>
```

我们构造一个符合语法规则的正则表达式，匹配和抽取以上网页文本中的超链接，正则表达式如下所示：

```
href="http://.+?"
```

这个正则表达式查找以ref="http:// 开头的字符串，之后是一个或多个字符“.+?”，最后是另一个双引号。“.+?”表示以非贪婪方式进行匹配，而不是贪婪方式。非贪婪匹配试图找到最小可能匹配的字符串，贪婪匹配试图找到最大可能匹配的字符串。

正则表达式中的括号表示，我们需要精确匹配的字符串，程序代码如下：

```
import urllib
import re

url = raw_input('Enter - ')
html = urllib.urlopen(url).read()
links = re.findall('href="(http://.*?)"', html)
for link in links:
    print link
```
正则表达式的findall方法返回正则表达式匹配到的字符串列表，仅返回双引号之间的超链接文本。程序运行结果如下：

```
python urlregex.py
Enter - http://www.dr-chuck.com/page1.htm
http://www.dr-chuck.com/page2.htm

python urlregex.py
Enter - http://www.py4inf.com/book.htm
http://www.greenteapress.com/thinkpython/thinkpython.html
http://allendowney.com/
http://www.py4inf.com/code
http://www.lib.umich.edu/espresso-book-machine
http://www.py4inf.com/py4inf-slides.zip
```

如果HTML网页是良构的和可预测的，正则表达式会处理地很漂亮。但是，由于存在大量“破坏性”的HTML网页，你可能会发现，仅使用正则表达式的解决方案可能会错过一些有效链接或中止于“坏数据”。

强大的HTML解析库可以解决这个问题。

## 12.7 使用BeautifulSoup解析HTML

已有许多Python库可以帮助你解析HTML与抽取页面中的数据。每个Python库都有优缺点，根据需要进行选择。

举例来看，我们使用BeautifulSoup来简单解析一些HTML输入和抽取链接。从<http://www.crummy.com>网站下载和安装BeautifulSoup代码。

你可以下载和安装BeautifulSoup，或简单地将BeautifulSoup.py放在你的程序文件夹下。

HTML与XML看起来很像，有一些网页被精心构造为XML。一般而言，大多数HTML会被XML解析器认为是格式不正确而整体拒绝，导致解析失败。BeautifulSoup极大容忍了HTML的缺陷，依然让你能轻易抽取所需的数据。

我们使用urllib读取页面，然后根据锚标签抽取href属性的内容。

```
import urllib
from BeautifulSoup import *

url = raw_input('Enter - ')
html = urllib.urlopen(url).read()
soup = BeautifulSoup(html)

# Retrieve all of the anchor tags
tags = soup('a')
for tag in tags:
   print tag.get('href', None)
```

该程序提示输入一个网址，然后打开网页，读取与传递数据到BeautifulSoup解析器，接下来，检索所有的锚标签，打印出每个标签的href属性内容。

```
python urllinks.py
Enter - http://www.dr-chuck.com/page1.htm
http://www.dr-chuck.com/page2.htm

python urllinks.py
Enter - http://www.py4inf.com/book.htm
http://www.greenteapress.com/thinkpython/thinkpython.html
http://allendowney.com/
http://www.si502.com/
http://www.lib.umich.edu/espresso-book-machine
http://www.py4inf.com/code
http://www.pythonlearn.com/
```

使用BeautifulSoup取出每个标签的不同部分，代码如下：

```
import urllib
from BeautifulSoup import *

url = raw_input('Enter - ')
html = urllib.urlopen(url).read()
soup = BeautifulSoup(html)

# Retrieve all of the anchor tags
tags = soup('a')
for tag in tags:
   # Look at the parts of a tag
   print 'TAG:',tag
   print 'URL:',tag.get('href', None)
   print 'Content:',tag.contents[0]
   print 'Attrs:',tag.attrs
```

程序运行结果如下：

```
python urllink2.py
Enter - http://www.dr-chuck.com/page1.htm
TAG: <a href="http://www.dr-chuck.com/page2.htm">
Second Page</a>
URL: http://www.dr-chuck.com/page2.htm
Content: [u'\nSecond Page']
Attrs: [(u'href', u'http://www.dr-chuck.com/page2.htm')]
```

这些例子仅揭开了BeautifulSoup解析HTML功能的冰山一角。更多内容详见<http://www.crummy.com>文档与示例。

## 12.8 使用urllib读取二进制文件

有时，你需要检索非文本（二进制）文件，如图像或视频文件。这些文件的数据直接打印输出是没有用的，但可以通过urllib将URL指向的文件保存在本地硬盘。

该模式打开URL，使用read方法下载整个文档内容，将其存入一个字符串变量（如img），然后将变量内容写入本地文件。程序代码如下：

```
img = urllib.urlopen('http://www.py4inf.com/cover.jpg').read()
fhand = open('cover.jpg', 'w')
fhand.write(img)
fhand.close()
```

该程序通过网络读取所有数据，将它存在计算机内存的img变量中，然后打开文件cover.jpg，将数据写入到硬盘中。如果文件大小小于计算机内存容量，这个程序将会成功运行。

然而，如果是一个大型音频或视频文件，当计算机耗尽内存时，这个程序可能会崩溃或运行极为缓慢。为了避免耗尽内存，我们以区块（或缓冲区）检索数据，在检索下一个区块前将当前区块写入磁盘。这样一来，程序就可以读取任意大小的文件，无需担心耗尽计算机的全部内存。

```
import urllib

img = urllib.urlopen('http://www.py4inf.com/cover.jpg')
fhand = open('cover.jpg', 'w')
size = 0
while True:
    info = img.read(100000)
    if len(info) < 1 : break
    size = size + len(info)
    fhand.write(info)

print size,'characters copied.'
fhand.close()
```
在这个示例中，每次读取100,000个字符，从网络检索下一批100,000个字符数据之前，先将这些字符写入cover.jpg文件。

程序运行结果如下：

```
python curl2.py
568248 characters copied.
```

如果是Unix或Mac计算机，你可以使用操作系统内置命令来执行这个操作：

```
curl -O http://www.py4inf.com/cover.jpg
```

curl命令是“copy URL”的缩写，这两个例子文件命名为curl1.py和curl2.py，可以从<http://www.py4inf.com/code>下载。它们实现了culr命令相似的功能。curl3.py示例程序以更高效的方式完成二进制文件的读写，这种模式可能对你自己编写程序时有所帮助。

## 12.9  术语

**BeautifulSoup：** 一个用于HTML文档解析与数据抽取的Python库。它能够处理大多数在浏览器中通常被忽略的，存在缺陷的HTML。BeautifulSoup代码<http://从www.crummy.com>网站下载。

**端口：**当与服务器建立套接字连接时，服务器告诉应用程序进行通讯所采用的数字。例如，网络流量通常使用80端口，电子邮件流量使用25端口。

**抓取：**把程序伪装成网络浏览器，检索网页，查找网页中的内容。通常，程序会根据一个网页中的链接找到下一个网页，实现对网页网络或社交网络的遍历。

**套接字：**两个应用程序之间的网络连接，彼此可以发送与接收数据。

**爬虫：**网络搜索引擎的检索页面，然后从该页面所有链接再次发起检索，如此往复下去，直到它们检索到网络上的几乎所有页面。这些页面将用于构建索引，供搜索之用。

## 12.10 练习

**习题12.1** 修改套接字程序socket1.py，提示用户输入URL，让它可以读取任何网页。你可以使用split(’/’)拆分URL，抽取出套接字connect调用的主机名。使用try和except增加错误检查，处理用户输入不恰当的网址或不存在的URL这两种情况。

**习题12.2** 修改套接字程序，统计接收到的字符数与3000个字符之后未显示的文本。该程序应检索整个文档，统计字符总数并显示在文档结尾。

**习题12.3** 使用urllib重复之前的练习：（1）从URL中检索文档；（2）显示3000个字符；（3）统计文档的字符总数。这里不必担心头部信息，只显示文档内容中前3000个字符即可。

**习题12.4** 修改ulrlinks.py程序，对检索到的HTML文档抽取和统计段落标签(p)，在程序输出中显示段落数量。不要显示段落文本，仅统计段落总数。在简单网页和复杂网页上测试该程序。

**习题12.5** （进阶）修改socket程序，使其只显示头部和空行之后的检索数据。请记住，recv是按照字符（包括换行及所有）而非行来接收的。
