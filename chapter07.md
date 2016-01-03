# 第7章 文件

## 7.1 持久性

截止目前，我们已经学习了如何编写程序，通过条件执行、函数和迭代等手段，与中央处理器CPU沟通我们的意愿。我们还学习了如何在主存储器中创建与使用数据结构。CPU和内存是软件工作与运行的地方。它也是所有计算机进行“思考”的大脑。

回顾一下之前讨论过的硬件架构问题，电源关闭后CPU与主存储器（也就是内存）中的数据就会丢失。之前我们学习编写的Python程序仅作为临时的练习。

![enter image description here](http://www.pythonlearn.com/html-270/book010.png)

本章介绍辅助存储器（也就是文件）。电源关闭后辅助存储器里的数据不会丢。借助U盘（闪存），我们可以把程序从一个系统转移到另一个系统。

首先，我们学习文本文件的读写。这里的文本文件是指我们在文本编辑器中创建的。然后，我们会了解如何与数据库文件进行交互，例如，二进制文件，通过数据库软件进行读写。

## 7.2 打开文件

从硬盘上读写文件之前，必须首先打开这个文件。打开文件需要与操作系统进行对话，它知道文件数据的存储位置。打开一个文件时，让操作系统通过文件名找到它，并确定这个文件是否存在。在下面的例子中，我们打开mbox.txt这个文件，它应该存储在你运行的python程序同一个文件夹下。这个示例用来打开mbox.txt文件。这个文件存储在你启动Python时所在的那个文件夹。从<http://www.py4inf.com/code/mbox.txt>下载这个文件。

```
>>> fhand = open('mbox.txt')
>>> print fhand
<open file 'mbox.txt', mode 'r' at 0x1005088b0>
```

如果文件成功被打开，操作系统会返回一个文件句柄。文件句柄并不存储文件的实际数据，只是一个“操作”，用于读取数据。如果请求的文件存在，你会得到一个句柄并获得读取该文件的相应权限。

![enter image description here](http://www.pythonlearn.com/html-270/book010.png)

如果文件不存在，打开失败，输出追踪错误信息，无法得到访问文件内容的句柄。

```
>>> fhand = open('stuff.txt')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IOError: [Errno 2] No such file or directory: 'stuff.txt'
```

稍后，我们会使用try和except更好地处理文件打开时文件不存在的情况。

## 7.3 文本文件与文本行

文本文件可视为若干文本行的序列，这与Python字符串是字符的序列道理相同。举例来说，以下是一个文本文件示例，记录了在一个开源项目中开发团队成员的邮件活动。

邮件交流的整体文件可以从 <http://www.py4inf.com/code/mbox.txt>下载。文件的缩减版可以从<http://www.py4inf.com/code/mbox-short.txt>下载。文件中包含多条邮件，遵守一种标准格式。以“From ”开头的行是每一条邮件第一行，以“From:” 开头的行是邮件的一部分，注意区分。更多信息请访问<http://en.wikipedia.org/wiki/Mbox>。

将文本文件分解成文本行，“一行的结束”用专门的字符来表示，称为换行符。

Python字符串常量的换行符用\n表示。这看起来像两个字符，事实上它是一个字符。在Python解释器中输入变量stuff，\n出现在字符串中，使用print语句可以看到字符串被换行符分成了两行。

```
>>> stuff = 'Hello\nWorld!'
>>> stuff
'Hello\nWorld!'
>>> print stuff
Hello
World!
>>> stuff = 'X\nY'
>>> print stuff
X
Y
>>> len(stuff)
3
```

因为换行符是一个字符，所以字符串“X\nY”的长度是3。

基于以上考虑，逐行读取文件时，我们需要知道每一行的结尾都有一个特殊的隐藏字符，即换行符。

总之，换行符将文本文件分解为若干文本行。

## 7.4 读取文本行

虽然文件句柄并不包含文件的数据，但它可以方便地构建一个for循环，按行依次读取文件。

```
fhand = open('mbox.txt')
count = 0
for line in fhand:
    count = count + 1
print 'Line Count:', count

python open.py
Line Count: 132045
```

for循环中文件句柄被当做序列来使用。在这个示例中，for循环只是简单地计算并输出文件的行数。这个循环大致用英语可以翻译成：“每遇到文件中的一行（表示为文件句柄），将count变量值加一”。

open函数如果没能读取整个文件，可能是文件过大，其中包含许多个G的数据。不论文件大小，open语句的打开时间是不变的。最终，for循环读取了文件中的数据。

以for循环这种方式读取文件时，Python根据换行符将文件数据分成若干文本行。Python根据换行符获得一行，每次迭代中将换行符作为一行的最后一个字符。

由于for循环每次只读取一行数据，因此它能高效地读取与统计出大型文件的文本行，无需耗尽内存来存储数据。上面的程序只使用很少的内存就能统计处任意大小文件的文本行，它先读取，然后统计，最后舍弃。

如果文件大小相对于内存容量来说很小，那么就可以把它当做一个字符串，在文件句柄上使用read方法一次性读取进来。

```
>>> fhand = open('mbox-short.txt')
>>> inp = fhand.read()
>>> print len(inp)
94626
>>> print inp[:20]
From stephen.marquar
```

在这个示例中， mbox-short.txt整个文件（总计94626个字符）直接被读入到变量inp中。我们使用字符串分割，打印出变量inp中存储的字符串前20个字符。

以这种方式读取文件时，所有的文本行和换行符被当做一个整体，作为一个大字符串存储在inp变量中。请记住，只有当计算机内存能够承载文件数据大小的情况下，才能用这种方式打开文件。

如果文件太大，内容无法承载，那就需要写一个for或while循环来分块读取。

## 7.5 搜索文件

搜索文件中数据的一种最常见方式是通读整个文件，只处理符合特定条件的文本行，其他一概忽略。我们将文件读取与字符串方法结合起来，构建简单的搜索机制。

举例来说，读取一个文件并把以“From:”开头的行打印出来。我们可以使用字符串startwith方法来选择符合前缀要求的行。

```
fhand = open('mbox-short.txt')
for line in fhand:
    if line.startswith('From:') :
        print line
```

程序运行结果如下：

```
From: stephen.marquard@uct.ac.za

From: louis@media.berkeley.edu

From: zqian@umich.edu

From: rjlowe@iupui.edu
...
```

输出结果正是我们想要的以“Form:”开头的行，但是为什么会有多余的空行出现呢？这是由于不可见的换行符所致。每一行都以换行符结束，因此print语句输出的变量line中的字符串带有一个换行符，print输出时本身还会增加一个换行符，所以最终就变成了空两行。

我们可以使用字符串分割来打印出不含最后一个字符的文本行，不过还有一个更简单的办法，使用rstrip方法截掉字符串后面的空白符，程序代码如下所示：

```
fhand = open('mbox-short.txt')
for line in fhand:
    line = line.rstrip()
    if line.startswith('From:') :
        print line
```

程序运行结果如下：

```
From: stephen.marquard@uct.ac.za
From: louis@media.berkeley.edu
From: zqian@umich.edu
From: rjlowe@iupui.edu
From: zqian@umich.edu
From: rjlowe@iupui.edu
From: cwen@iupui.edu
...
```

随着文件处理程序变得越来越复杂，你可能会用到continue语句来编写搜索循环。搜索循环的基本思路是寻找“感兴趣的”行，跳过“不感兴趣的”行。当找到感兴趣的的文本行，执行相应的操作。

跳过不感兴趣的文本行的循环结构，代码如下所示：

```
fhand = open('mbox-short.txt')
for line in fhand:
    line = line.rstrip()
    # Skip 'uninteresting lines'
    if not line.startswith('From:') :
        continue
    # Process our 'interesting' line
    print line
```

程序运行结果是一样的。可以这样理解，不感兴趣的文本行就是那些不以“From:”开头的行，我们使用continue跳过这些行。对于感兴趣的文本行，也就是那些以“From：”开头的行，我们进行相应处理。

我们可以使用字符串方法find来模拟文本编辑器的搜索功能，找到任何一行中出现待查的字符串。由于find方法可以寻找一个字符串在另一个字符串中出现的次数，也可以返回字符串的位置或-1（表示字符串没有找到）。我们编写一个循环，找到包含“@uct.ac.za”字符串的文本行，也就是找到来自南非开普敦大学的邮件。

```
fhand = open('mbox-short.txt')
for line in fhand:
    line = line.rstrip()
    if line.find('@uct.ac.za') == -1 :
        continue
    print line
```

程序运行结果如下：

```
From stephen.marquard@uct.ac.za Sat Jan  5 09:14:16 2008
X-Authentication-Warning: set sender to stephen.marquard@uct.ac.za using -f
From: stephen.marquard@uct.ac.za
Author: stephen.marquard@uct.ac.za
From david.horwitz@uct.ac.za Fri Jan  4 07:02:32 2008
X-Authentication-Warning: set sender to david.horwitz@uct.ac.za using -f
From: david.horwitz@uct.ac.za
Author: david.horwitz@uct.ac.za
...
```

## 7.6 让用户选择文件名

我们不希望在处理不同文件时每次都要修改python代码。一个更号的方法是每次运行程序时，让用户自己输入文件名。这样一来，用户就可以将这个程序用于不同文件，无需改动Python代码。

一个非常简单的方法可以满足以上需求，使用raw_input方法让用户输入文件名，代码如下所示：

```
fname = raw_input('Enter the file name: ')
fhand = open(fname)
count = 0
for line in fhand:
    if line.startswith('Subject:') :
        count = count + 1
print 'There were', count, 'subject lines in', fname
```

从用户那里获得文件名，把它赋予fname变量，然后打开对应的文件。现在，我们可以对不同的文件重复运行这个程序了。

```
python search6.py
Enter the file name: mbox.txt
There were 1797 subject lines in mbox.txt

python search6.py
Enter the file name: mbox-short.txt
There were 27 subject lines in mbox-short.txt
```

进入下一小节之前，仔细查看这段程序，问问自己：“可能会出现的错误是什么？”或者“友好的用户可能会做些什么，导致这个精巧的程序会报错，这样的话我们在用户眼中就不那么酷了”。

## 7.7 使用try、except与open

告诉过你不要偷看。这是最后的机会。

如果用户输入的不是一个文件名呢？

```
python search6.py
Enter the file name: missing.txt
Traceback (most recent call last):
  File "search6.py", line 2, in <module>
    fhand = open(fname)
IOError: [Errno 2] No such file or directory: 'missing.txt'

python search6.py
Enter the file name: na na boo boo
Traceback (most recent call last):
  File "search6.py", line 2, in <module>
    fhand = open(fname)
IOError: [Errno 2] No such file or directory: 'na na boo boo'
```

不要笑，不管是有意还是无意，用户做的任何操作都有可能破坏你的程序。事实上，软件开发团队中有一个重要的部分是质量保障（简称QA），可能由一个人或一个小组负责。他们的工作就是尽可能尝试破坏程序员开发的软件。

在用户购买软件或为程序员付薪水之前，QA团队的责任就是寻找程序的缺陷。可以这样说，QA团队是程序员的最佳搭档。

程序出错可以用try/except结构快速修复。假设open方法打开文件时会出错，那么在open方法打开失败时，增加一个恢复模式，代码如下：

```
fname = raw_input('Enter the file name: ')
try:
    fhand = open(fname)
except:
    print 'File cannot be opened:', fname
    exit()

count = 0
for line in fhand:
    if line.startswith('Subject:') :
        count = count + 1
print 'There were', count, 'subject lines in', fname
```

exit函数会终止程序，这个程序永不返回值。当用户（或QA团队）输入不正确的文件名时，我们可以“捕获”它们并快速进行修复：

```
python search7.py
Enter the file name: mbox.txt
There were 1797 subject lines in mbox.txt

python search7.py
Enter the file name: na na boo boo
File cannot be opened: na na boo boo
```
对open方法调用的保护是Python程序中try/except的典型用法。以Python方式做事，我们使用“Pythonic”这个术语（Python风格）。上面的例子可称为文件打开的Python风格。当更加熟悉Python后，你会经常和其他程序员讨论，两个效果相同的方案哪一个更具有Python风格。愈发Python风格化的目的是将编程变得工程性与艺术性兼备。我们不仅要让程序能够正常工作，还要让解决方案更加优雅。

## 7.8 写入文件

为了能够写入文件，需要在打开文件时使用“w”作为第二个参数。

```
>>> fout = open('output.txt', 'w')
>>> print fout
<open file 'output.txt', mode 'w' at 0xb7eb2410>
```

如果文件已经存在，以写入模式打开文件，这样会删除旧数据，因此请谨慎使用。如果文件不存在，那么会创建一个新的文件。

文件句柄对象的write方法把数据写入文件。

```
>>> line1 = 'This here's the wattle,\n'
>>> fout.write(line1)
```

在结束一行时，确保已明确插入了换行符。print语句会自动加上一个换行符，而write方法不会这样做。

```
>>> line2 = 'the emblem of our land.\n'
>>> fout.write(line2)
```

当文件写入完成，记得关闭文件，确保写入物理磁盘，这样断电后数据才不会丢失。

```
>>> fout.close()
```

以读方式打开文件也要记得关闭文件。只顾打开新文件就显得有点粗心大意了。Python会在程序结束时，确认所有打开的文件被关闭了。当写入文件时，我们要对文件关闭进行明确声明，确保万无一失。

## 7.9 调试

读写文件时，你可能会遇到空格带来的问题。因为空格符、制表符和换行符是隐藏的，所以这些错误很难调试。

```
>>> s = '1 2\t 3\n 4'
>>> print s
1 2  3
 4
```

内置函数repr可以解决这类问题。它将任一对象作为参数，返回该对象的一个字符串表示。字符串中用反斜杠序列代表空白字符。

```
>>> print repr(s)
'1 2\t 3\n 4'
```

这对调试很有帮助。

另一个可能遇到的问题是，不同的操作系统使用不同的字符来表示一行的结束。一些操作系统使用换行符\n，一些操作系统使用返回字符\r，还有一些操作系统两者都使用。如果在不同的操作系统之间转移文件，这些差异可能会导致错误发生。

绝大多数操作系统都提供格式转换的应用。详细信息和更多疑问请访问<http://wikipedia.org/wiki/Newline>。当然，你也可以自己写一个。

## 7.10 术语

**捕获：**使用try/except语句防止程序意外中止。

**换行符：**在文件和字符串中表示一行结尾的特殊字符。

**Python风格：**让Python更简洁、明确、高效工作的编程技术。使用try和except来恢复丢失的文件，这是Python风格的一个典型举例。

**质量保证：**负责软件产品整体质量保证的一个人或一个团队。QA任务包括产品测试与识别错误，一般在软件发布之前进行。

**文本文件：**保存在永久存储介质（如硬盘）的字符序列。

## 7.11 练习

**习题7.1** 编写一个程序，读取一个文件，以大写方式逐行打印出文件内容。程序运行结果如下所示：

```
python shout.py
Enter a file name: mbox-short.txt
FROM STEPHEN.MARQUARD@UCT.AC.ZA SAT JAN  5 09:14:16 2008
RETURN-PATH: <POSTMASTER@COLLAB.SAKAIPROJECT.ORG>
RECEIVED: FROM MURDER (MAIL.UMICH.EDU [141.211.14.90])
  BY FRANKENSTEIN.MAIL.UMICH.EDU (CYRUS V2.3.8) WITH LMTPA;
  SAT, 05 JAN 2008 09:14:16 -0500
```

你可以从<http://www.py4inf.com/code/mbox-short.txt>下载文本文件。

**习题7.2** 编写一个程序，让用户输入文件名，然后读取文件，按行的形式j进行查看。

X-DSPAM-Confidence:  0.8475

遇到以“X-DSPAM-Confidence：”开头的行，提取该行中的浮点数。统计行数，计算这些行的垃圾邮件信度值。文件读取结束后，打印垃圾邮件平均信度。

```
Enter the file name: mbox.txt
Average spam confidence: 0.894128046745

Enter the file name: mbox-short.txt
Average spam confidence: 0.750718518519
```

用mbox.txt和mbox-short.txt这两个文件测试你的代码。

**习题7.3** 有时候，程序员感到无聊或是想找点乐子，他们会在程序里加入彩蛋（无害的代码）（<http://en.wikipedia.org/wiki/Easter_egg_(media)>）。修改上面的程序，当用户输入 “na na boo boo”时，打印一些有趣的消息。不管文件是否存在，程序都能正常运行。下面是程序运行样本：

```
python egg.py
Enter the file name: mbox.txt
There were 1797 subject lines in mbox.txt

python egg.py
Enter the file name: missing.tyxt
File cannot be opened: missing.tyxt

python egg.py
Enter the file name: na na boo boo
NA NA BOO BOO TO YOU - You have been punk'd!
```

不鼓励你在程序里加入彩蛋，这里只用作练习。
