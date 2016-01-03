# 第16章 常见任务自动化处理

我们已经学习了从文件、网络、Web Services和数据库中读取数据。Python还可以遍历计算机的所有目录和文件夹，并读取这些文件。

本章中编写的程序会扫描计算机，对每个文件执行某些操作。文件被组织到目录中，也称为“文件夹”。简单的Python脚本既能快速解决简单任务，也能应对目录树或整个计算机上成百上千的文件。

使用os.walk和for循环遍历目录树中的所有目录与文件。这与open方法循环读取文件内容、套接字通过编写循环读取网络连接中的内容以及urllib打开网页读取所有内容等的原理类似。

## 16.1 文件名与路径

每个运行中的程序有一个“当前目录”，作为大多数操作的默认目录。例如，当打开一个文件进行读取时，Python会在当前目录下寻找这个文件。

os（代表operating system，操作系统）模块提供文件与目录的操作功能。os.getcwd返回当前目录的名称：

```
>>> import os
>>> cwd = os.getcwd()
>>> print cwd
/Users/csev
```

cwd代表当前工作目录。这个示例程序的运行结果是/Users/csev，这是用户csev的当前目录。

类似cwd这样的字符串表示的是路径。相对路径从当前目录开始，绝对路径从文件系统的顶层目录开始。

我们看到的路径都是简单文件名，因此它们是相对于当前目录的。找到一个文件的绝对路径，使用os.path.abspath：

```
>>> os.path.abspath('memo.txt')
'/Users/csev/memo.txt'
```

os.path.exists检查文件或目录是否存在：

```
>>> os.path.exists('memo.txt')
True
```

如果存在，os.path.isdir检查它是否是一个目录：

```
>>> os.path.isdir('memo.txt')
False
>>> os.path.isdir('music')
True
```

同样地，os.path.isfile检查它是否是一个文件。

os.listdir根据指定目录，返回其下的文件与子目录的列表。

```
>>> os.listdir(cwd)
['music', 'photos', 'memo.txt']
```

## 16.2 示例：清理照片目录

以前我编写过一个类似Flickr的软件，可以从手机上接收照片，并把它们存储在服务器上。我编写这个软件时Flickr还未出现，当Flickr出现后，我仍然在使用这个程序，用来保留原始照片。

我还会在彩信和电子邮件标题行发送一条简单的文本描述。我将这些消息存在一个文本文件中，放置在照片文件所在的目录下。根据照片拍摄的月、年、日和时间来组织目录结构。以下是照片及其描述的命名示例：

```
./2006/03/24-03-06_2018002.jpg
./2006/03/24-03-06_2018002.txt
```

七年之后，我有了许多照片和标题。这些年我更换过手机，从消息中抽取标题的代码有时会失效，服务器上出现了一些无用的数据。

我想要遍历这些文件，找出哪些文本文件是真正的标题，哪些是垃圾信息，然后删除这些垃圾信息。首先，盘点出子文件夹下有多少文本文件，运行以下程序：

```
import os
count = 0
for (dirname, dirs, files) in os.walk('.'):
   for filename in files:
       if filename.endswith('.txt') :
           count = count + 1
print 'Files:', count

python txtcount.py
Files: 1917
```

这段代码的关键是Python的os.walk库。当调用os.walk时，指定一个起始目录，它会递归式遍历所有的子目录。“.”表示当前目录，从此处往下查找。当它每遇到一个目录，我们将得到for循环内元组的三个值。第一个值是当前目录名，第二个值是当前目录的子目录列表，第三个值是当前目录的文件列表。

没必要依次查看每个子目录。事实上，通过os.walk来访问每个文件夹。如果想要查看每个文件，编写一个简单的for循环来查看当前目录下的每个文件。如果文件以“.txt”结尾，我们就查看这个文件，并统计整个目录树中以“.txt”为后缀的文件数目。

一旦知道了有多少文件以“.txt”结尾，接下来要自动判断文件的好坏。因此，我们编一个简单的程序，打印出文件及其大小：

```
import os
from os.path import join
for (dirname, dirs, files) in os.walk('.'):
   for filename in files:
       if filename.endswith('.txt') :
           thefile = os.path.join(dirname,filename)
           print os.path.getsize(thefile), thefile
```

现在，不仅仅要统计文件数，我们使用os.path.join创建一个文件名，将目录中的文件名与目录名连接在一起。这里使用os.path.join，而不是用字符串连接。这样做的原因在于：Windows上使用反斜杠（\）来构造文件路径，Linux和Mac上使用正斜杠（/）来构造文件路径。os.path.join知道如何处理这一差异，能够识别当前运行的操作系统，据此选择适合的连接。因此，相同的Python代码在Windows和类Unix系统上都能执行。

一旦得到了带有目录路径的完整文件名，使用os.path.getsize获取文件大小，并打印输出，程序运行结果如下：

```
python txtsize.py
...
18 ./2006/03/24-03-06_2303002.txt
22 ./2006/03/25-03-06_1340001.txt
22 ./2006/03/25-03-06_2034001.txt
...
2565 ./2005/09/28-09-05_1043004.txt
2565 ./2005/09/28-09-05_1141002.txt
...
2578 ./2006/03/27-03-06_1618001.txt
2578 ./2006/03/28-03-06_2109001.txt
2578 ./2006/03/29-03-06_1355001.txt
...
```

扫视一下程序输出，我们注意到有一些文件很短，有些文件非常大，还有一些相同大小的文件（2578和2565）。当打开一些大文件，我们发现它们除了一些通用的HTML标签之外，其他什么都没有。那些HTML从我的T-Moblie手机发送的消息。

```
<html>
        <head>
                <title>T-Mobile</title>
...
```

跳过这个文件，它看起来没有包含有用的信息，随后我们可能做删除处理。

在删除这些文件之前，我们编写一个程序，查找多余一行的文件，并显示文件的内容。不要被2578或2565字符长度的文件所干扰，因为我们已经知道这些文件没有包含有用信息。

程序代码如下：

```
import os
from os.path import join
for (dirname, dirs, files) in os.walk('.'):
   for filename in files:
       if filename.endswith('.txt') :
           thefile = os.path.join(dirname,filename)
           size = os.path.getsize(thefile)
           if size == 2578 or size == 2565:
               continue
           fhand = open(thefile,'r')
           lines = list()
           for line in fhand:
               lines.append(line)
           fhand.close()
           if len(lines) > 1:
                print len(lines), thefile
                print lines[:4]
```

我们使用continue跳过两个“大小不正确”的文件，然后打开其他文件，将读取到文件的内容放到一个Python列表中。如果文件多余一行，打印出文件的行数和前三行内容。

这样一来，程序过滤掉两个大小不正确的文件。假设所有单行的文件是正确的，那么我们得到一些符合要求的数据：

```
python txtcheck.py
3 ./2004/03/22-03-04_2015.txt
['Little horse rider\r\n', '\r\n', '\r']
2 ./2004/11/30-11-04_1834001.txt
['Testing 123.\n', '\n']
3 ./2007/09/15-09-07_074202_03.txt
['\r\n', '\r\n', 'Sent from my iPhone\r\n']
3 ./2007/09/19-09-07_124857_01.txt
['\r\n', '\r\n', 'Sent from my iPhone\r\n']
3 ./2007/09/20-09-07_115617_01.txt
```

但是，文件中还是存在一个或多个令人头疼的模式：有一些三行文件，包含两个空行，之后跟一行文字“发自我的iPhone”，这样的数据仍然存在。因此，针对这个情况修改程序如下：

```
lines = list()
           for line in fhand:
               lines.append(line)
           if len(lines) == 3 and lines[2].startswith('Sent from my iPhone'):
               continue
           if len(lines) > 1:
                print len(lines), thefile
                print lines[:4]
```

如果是三行文件，程序对其进行检查；如果第三行以特定内容开始，则跳过它。

现在运行这个程序，我们看到还有4个多行文件，但这些文件看起来是合理的：

```
python txtcheck2.py
3 ./2004/03/22-03-04_2015.txt
['Little horse rider\r\n', '\r\n', '\r']
2 ./2004/11/30-11-04_1834001.txt
['Testing 123.\n', '\n']
2 ./2006/03/17-03-06_1806001.txt
['On the road again...\r\n', '\r\n']
2 ./2006/03/24-03-06_1740001.txt
['On the road again...\r\n', '\r\n']
```

纵观程序的整体模式，通过接受或拒绝文件，对结果进行清理。一旦找到“坏”模式，使用continue跳过不符合要求的文件。这样对代码进行修正，找到更多不符合要求的文件模式。

现在，我们准备删除这些文件。这里反转下逻辑，不打印输出剩下的好文件，而是打印出那些不符合要求、准备删除的文件。

```
import os
from os.path import join
for (dirname, dirs, files) in os.walk('.'):
   for filename in files:
       if filename.endswith('.txt') :
           thefile = os.path.join(dirname,filename)
           size = os.path.getsize(thefile)
           if size == 2578 or size == 2565:
               print 'T-Mobile:',thefile
               continue
           fhand = open(thefile,'r')
           lines = list()
           for line in fhand:
               lines.append(line)
           fhand.close()
           if len(lines) == 3 and lines[2].startswith('Sent from my iPhone'):
               print 'iPhone:', thefile
```

我们得到了一个待删除的候选文件列表，明白了为什么这些文件会被删除。程序运行结果如下：

```
python txtcheck3.py
...
T-Mobile: ./2006/05/31-05-06_1540001.txt
T-Mobile: ./2006/05/31-05-06_1648001.txt
iPhone: ./2007/09/15-09-07_074202_03.txt
iPhone: ./2007/09/15-09-07_144641_01.txt
iPhone: ./2007/09/19-09-07_124857_01.txt
...
```

我们可以检查这些文件，确保没有在不经意间引入错误导致程序结束，或是由于逻辑问题导致一些文件被 “错抓”。

当我们对待删除的列表感到满意，对程序做出如下修改：

```
if size == 2578 or size == 2565:
    print 'T-Mobile:',thefile
    os.remove(thefile)
    continue
...
if len(lines) == 3 and lines[2].startswith('Sent from my iPhone'):
    print 'iPhone:', thefile
    os.remove(thefile)
    continue
```

在这个版本的程序中，我们不仅打印出文件，还使用os.remove移除不符合要求的文件。

```
python txtdelete.py
T-Mobile: ./2005/01/02-01-05_1356001.txt
T-Mobile: ./2005/01/02-01-05_1858001.txt
...
```

出于试验目的，再运行一遍程序，这次不会输出任何结果，这是因为不符合要求的文件已经被移除过了。

如果再次运行txtcount.py，899个不符合要求的文件会被移除。

```
python txtcount.py
Files: 1018
```

在本节中，我们遵循一定处理步骤。首先，使用Python遍历目录和文件来寻找模式；然后，我们在Python的帮助下，确定目录中哪些内容需要进行清理，一旦找到哪些文件符合要求，识别出哪些文件没有用；最后，使用Python进行清理，删除那些没有用的文件。

需要解决的问题可能非常简单，可能仅需要查看文件名，或逐个读入文件，查找文件中存在的模式。有时，你需要读取所有文件，修改其中一些文件。当掌握了os.walk与其他os实用工具之后，这些操作会变得非常简单。

## 16.3 命令行参数

前面章节中的很多程序都使用raw_input为文件名的输入，从文件读取数据。整个数据处理过程如下：

```
name = raw_input('Enter file:')
handle = open(name, 'r')
text = handle.read()
...
```

对这个程序做一些简化，在Python启动时，通过命令行取得文件名。运行Python程序，提示如下：

```
python words.py
Enter file: mbox-short.txt
...
```

我们可以在Python文件后面附加其他字符串，在Python程序中访问这些命令行参数。下面的程序演示了从命令行读取参数：

```
import sys
print 'Count:', len(sys.argv)
print 'Type:', type(sys.argv)
for arg in sys.argv:
   print 'Argument:', arg
```

sys.argv的内容是一个字符串列表，其中第一个字符串是Python程序的名称，Python文件之后其他字符串是命令行参数。

下面的程序从命令行读取了几个参数：

```
python argtest.py hello there
Count: 3
Type: <type 'list'>
Argument: argtest.py
Argument: hello
Argument: there
```

这三个参数作为三元列表传递到程序中。列表的第一个元素是文件名（argtest.py），文件名之后的其他两个是命令行参数。

我们重写这个程序来读取文件，从命令行参数获得文件名，程序代码如下：

```
import sys

name = sys.argv[1]
handle = open(name, 'r')
text = handle.read()
print name, 'is', len(text), 'bytes'
```

我们把第二个命令行参数作为文件名，在[0]处跳过之前的程序名。打开文件并读取文件内容的代码如下所示：

```
python argfile.py mbox-short.txt
mbox-short.txt is 94626 bytes
```

使用命令行参数作为输入，使得Python程序更易于重用。特别是对仅有一个或两个字符串输入的情况有用。

## 16.4  管道

大多数操作系统提供命令行界面，也被称为Shell。Shell通常提供文件系统导航与应用启动的命令。例如，在Unix中，cd命令更改目录，ls显示目录的内容，键入诸如Firefox来启动网络浏览器。

从Shell可以启动任何程序，也可以通过Python的管道（pipe）来启动程序。管道是用来表示正在运行的进程的一个对象。

例如，Unix的命令[^1]ls –l 通常以长格式显示当前目录的内容。你可以用os.open来启动ls命令：

```
>>> cmd = 'ls -l'
>>> fp = os.popen(cmd)
```

参数是包含Shell命令的字符串。返回值是一个文件指针，这个过程就像是打开一个文件。通过ls进程，readline每次读取一行，或使用read方法一次性得到全部内容：

```
>>> res = fp.read()
```

完成之后，像关闭文件一样关闭管道：

```
>>> stat = fp.close()
>>> print stat
None
```

返回值是ls进程的最终状态。None表示正常结束，没有错误出现。

## 16.5 术语

**绝对路径：**从目录树顶层开始，文件或目录所在的位置。无论是否在当前工作目录，都可以访问到文件或目录。

**校验：**参见哈希算法（hashing）。“校验”这个术语来自于数据的验证需求，当数据在网络上传送或写入到备份介质后再进行读取的过程中，检查数据是否存在被篡改的可能。当数据写入或发送时，发送系统会计算出校验值，一并发送出去。当数据读入和收到时，接收系统会根据接收到的数据，重新计算校验值，与发送来的校验值进行比较。如果校验值不匹配，那么就会认为数据在传输过程中被篡改了。

**命令行参数：**Python文件名之后命令行中的参数。

**当前工作目录：**当前你“所在”的目录。在大多数系统的命令行界面，使用cd命令更改工作目录。在Python中，仅使用文件名打开文件，并没有指定路径信息，这时文件必须在当前运行程序的工作目录下。

**哈希算法：**读取潜在的大量数据，为数据生成一个唯一的校验值。最佳的哈希函数只产生很少的“冲突”。这里的冲突是指哈希函数对两个不同的数据流，产生相同的哈希值。MD5、SHA1和SHA256是常用的哈希算法。

**管道：**与正在运行的程序进行连接的通道。通过管道，你可以编写程序来发送数据给其他程序，或从其他程序接收数据。管道与套接字类似，但是管道只能用于同一台计算机上程序之间的连接，也就是说不能通过网络进行连接。

**相对路径：**相对于当前的工作目录，文件或目录所在的位置。

**shell：** 操作系统的命令行界面。在一些操作系统中称为“终端程序”。在命令行界面中，输入一条命令和参数，然后按下回车键来执行这条命令。

**遍历：**访问整个目录树、子目录以及子目录的子目录，直到访问到所有的目录。这称之为“遍历目录树”。

## 16.6 练习

**习题 16.1**：大量MP3文件集合中可能存在相同歌曲的多个副本，存储于不同的目录或者以不同的文件命名。这个练习的目标是找到重复的MP3文件。

1. 编写一个程序，遍历一个文件夹及其子文件夹中的所有以.mp3后缀结尾的文件，并列出相同大小的一对对文件。提示：使用字典，字典的键是从os.path.getsize得到的文件大小，字典的值是文件名与路径名的结合。每遇到一个文件，检查其是否与已知文件的大小相同。如果大小相同，得到一个重复大小的文件，打印该文件大小与两个文件的名称（一个来自哈希，另一个是你正在查看的文件）。
2. 修改之前的程序，用哈希或校验算法查看重复内容的文件。例如，MD5（Message-Digest algorithm 5, 消息摘要算法第五版）接受任意长度的消息，返回一个128位的校验值。不同内容的两个文件返回相同校验值的可能性非常小。MD5的具体内容详见<http://wikipedia.org/wiki/Md5>。以下代码片段打开一个文件，读入内容，计算校验值。

```
import hashlib
...
           fhand = open(thefile,'r')
           data = fhand.read()
           fhand.close()
           checksum = hashlib.md5(data).hexdigest()
```

新建一个字典，包含校验值作为键，文件名作为值。当计算了校验值，它就作为字典的键存在，有两个文件内容重复，因此打印出字典中的文件和刚才阅读的文件。在图像文件的文件夹下运行程序，结果如下所示：

```
./2004/11/15-11-04_0923001.jpg ./2004/11/15-11-04_1016001.jpg
./2005/06/28-06-05_1500001.jpg ./2005/06/28-06-05_1502001.jpg
./2006/08/11-08-06_205948_01.jpg ./2006/08/12-08-06_155318_02.jpg
```

很显然，我重复提交了相同的照片，没有及时删除之前的拷贝。

[^1]: 当使用管道与操作系统命令（如ls）对话时，有一点很重要，了解正在使用的操作系统类型，使用管道打开操作系统支持的命令。
