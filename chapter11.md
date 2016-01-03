# 第11章 正则表达式

至此，我们已经学会如何读取文件，寻找模式与提取感兴趣的文本行片段。文本行提取的方法，包括字符串方法（如split与find）、列表与字符串切片等。

文本搜索与抽取是常见任务。Python拥有非常强大的快速有效处理此类任务的库——正则表达式。正则表达式功能强大，比较复杂，其语法需要一些时间来熟悉，所以本书并没有过早提及。

正则表达式几乎就是一门关于字符串搜索与解析的小型编程语言。事实上，整本书都是围绕正则表达式主题展开。本章仅介绍正则表达式基础，有关正则表达式更多详细信息，请参阅以下网址：

<http://en.wikipedia.org/wiki/Regular_expression>

<http://docs.python.org/library/re.html>

正则表达式库在使用之前必须先导入到程序中。正则表达式库最简单的用法是search()函数。搜索函数的简单用法如下程序所示：

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    if re.search('From:', line) :
        print line
```

打开文件，循环每一行，使用正则表达式的search()函数，打印出包含字符串"From:"的文本行。这个程序其实并没有发挥正则表达式的真正实力，line.find()函数可以更容易地实现相同的结果。

正则表达式的强大之处体现于，可以在搜索字符串中添加特定字符，以实现更精确的字符串文本行的匹配控制。通过在正则表达式中添加特定字符，编写很少代码就可以实现复杂的匹配与抽取。

例如，正则表达式的^符号匹配一行的开始。我们修改一下上面的程序，仅匹配“From:”开头的文本行。

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    if re.search('^From:', line) :
        print line
```

好了，这就做到仅匹配“From:”开头的文本行。这仍然是一个非常简单的例子，字符串库的startswith()函数同样可以实现。之所以这样讲解，目的是介绍正则表达式的理念，包含特定行动字符，给予文本匹配更多的控制。

## 11.1 正则表达式的字符匹配

许多特定字符可以帮助我们编写非常强大的正则表达式。最常用的特定字符是句点，它可以匹配所有字符。

在下面的例子中，正则表达式"F..m:"会匹配配“From:”、"Fxxm"、“F12m”或"Fl@m"。正则表达式的句点可以匹配任意字符。

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    if re.search('^F..m:', line) :
        print line
```

“*”和“+”表示一个字符可以重复任意次数，在构造正则表达式时结合这种能力特别有用。这些特定字符用来代替单个字符，星号匹配零或多个字符，加号匹配一个或多个字符。

进一步减少代码，以下示例使用重复的**通配符**：

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    if re.search('^From:.+@', line) :
        print line
```

搜索字符串“^From:.+@”会成功匹配以“From:”开头，之后一个或多个字符，以@结尾的文本行。结果匹配如下所示：

```
From: stephen.marquard @uct.ac.za
```

可以这样理解，“.+”通配符匹配了冒号与@之间的所有字符。

```
 From:.+ @
```

有时候，加号与星号可能会“用力过猛”。例如下面的字符串匹配，".+"将其外推，直到最后一个@。

```
From: stephen.marquard@uct.ac.za, csev@umich.edu, and cwen @iupui.edu
```

通过添加其他字符，让星号和加号不要如此“贪婪”地匹配，这是可以做到的。详情请参阅“关闭贪婪行为”的文档。

## 11.2 使用正则表达式抽取数据

在Python中抽取字符串的数据，用到的是findall()函数。通过正则表达式的匹配，抽取所有符合的子字符串。以下示例从格式无关的任何文本行中抽取类似电子邮件地址的文本。

```
From stephen.marquard@uct.ac.za Sat Jan  5 09:14:16 2008
Return-Path: <postmaster@collab.sakaiproject.org>
          for <source@collab.sakaiproject.org>;
Received: (from apache@localhost)
Author: stephen.marquard@uct.ac.za
```

我们不想为每个文本行类型编写代码，每个文本行都分割和切片一次。以下程序使用findall()找到文本中的电子邮件地址，从每一行抽取一个或多个电子邮件地址。

```
import re
s = 'Hello from csev@umich.edu to cwen@iupui.edu about the meeting @2PM'
lst = re.findall('\S+@\S+', s)
print lst
```

findall()函数搜索第二个参数的字符串，返回一个包含形如电子邮件地址字符串的列表。我们使用两字符序列来匹配非空字符(\S)。

程序运行结果如下：

```
['csev@umich.edu', 'cwen@iupui.edu']
```

解释一下这个正则表达式，我们寻找至少含有一个非空字符的子字符串，之后是@，然后再是至少一个或多个非空字符。“\S+”匹配尽可能多个非空字符。这就是正则表达式中的贪婪匹配。

正则表达式会匹配两个电子邮件地址，但不会匹配“@2PM”，原因是@之前没有非空字符。在程序中使用这个正则表达式，读取文件的所有行，然后打印出所有类似电子邮件地址的结果，如下所示：

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    x = re.findall('\S+@\S+', line)
    if len(x) > 0 :
        print x
```

读取每一行，抽取与正则表达式匹配的所有字符串。由于findall()返回的是列表，我们简单查看下返回的列表不为零，打印出来的每行至少包含一个电子邮件地址。

对mbox.txt运行程序，得到如下结果：

```
['wagnermr@iupui.edu']
['cwen@iupui.edu']
['<postmaster@collab.sakaiproject.org>']
['<200801032122.m03LMFo4005148@nakamura.uits.iupui.edu>']
['<source@collab.sakaiproject.org>;']
['<source@collab.sakaiproject.org>;']
['<source@collab.sakaiproject.org>;']
['apache@localhost]']
['source@collab.sakaiproject.org;']
```

一些电子地址的开头或结尾包含了不正确的字符，如“<”或“;”。这里声明一下，仅需要以字母或数字开头和结尾的字符串部分。

要做到这一点，我们使用正则表达式的另一个功能，使用方括号罗列多个可接受的匹配字符。在某种意义上，“\S”匹配的是非空字符的集合。现在，我们更清楚一些字符匹配的本质了。

下面是新的正则表达式：

```
[a-zA-Z0-9]\S*@\S*[a-zA-Z]
```

这看起来有点复杂，你现在应该明白正则表达式为什么被称为一门专门的语言了。解释一下这个正则表达式，寻找以一种子字符串，以小写字母、大写字母或数字开头[a-zA-Z0-9]，之后是零个或多个非空字符“\S”，然后是@，再是零个或多个非空字符“\S”，最后是一个大写或小写字母。请注意，我们从加号到星号，再到零个或多个非空字符。[a-zA-Z0-9]本身就是一个非空字符。请记住，星号和加号直接作用于它左侧的单个字符。

如果在程序中使用这个正则表达式，数据会变得干净一些：

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    x = re.findall('[a-zA-Z0-9]\S*@\S*[a-zA-Z]', line)
    if len(x) > 0 :
        print x
...
['wagnermr@iupui.edu']
['cwen@iupui.edu']
['postmaster@collab.sakaiproject.org']
['200801032122.m03LMFo4005148@nakamura.uits.iupui.edu']
['source@collab.sakaiproject.org']
['source@collab.sakaiproject.org']
['source@collab.sakaiproject.org']
['apache@localhost']
```

注意到source@collab.sakaiproject.org这一行，正则表达式消除了字符串结尾（“>”）结尾的两个字母。原因是我们在正则表达式末尾追加了“[a-zA-Z]”，要求正则表达式解析器对找到的字符串必须以字母结尾。因此，当出现“sakaiproject.org>;” ，它会止步于匹配找到的最后一个字母，这里g是最后一个符合要求的字符匹配。

还要注意的是，该程序的结果是一个Python列表，每个字符串是一个元素。

## 11.3 将搜索与抽取结合

如果我们想要找到以“X-”开头的文本行，如下所示：

```
X-DSPAM-Confidence: 0.8475
X-DSPAM-Probability: 0.0000
```
我们不仅需要文本行中的浮点数，还需要统计符合以上语法的文本行数。

使用下面的正则表达式来挑选出符合要求的文本行：

```
^X-.*: [0-9.]+
```

解释一下，文本以“X-”开头，之后是零个或多个字符“.*”，然后是一个冒号和一个空格。空格之后是一个或多个字符，可以是一个数字(0-9)或一个句点“[0-9.]+”。需要注意的是，方括号中的句点实际匹配的是句点本身，也就是说，它在方括号内不是通配符。

这是一个非常紧凑的表达式，我们感兴趣的文本匹配如下所示：

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    if re.search('^X\S*: [0-9.]+', line) :
        print line
```

运行这个程序，经过过滤的数据仅保留如下内容：

```
X-DSPAM-Confidence: 0.8475
X-DSPAM-Probability: 0.0000
X-DSPAM-Confidence: 0.6178
X-DSPAM-Probability: 0.0000
```

现在，我们要解决抽取数值的问题，使用split方法。虽然使用split很简单，我们这里使用正则表达式的另一个功能，让搜索与解析同时进行。

括号是正则表达式的另一个特殊字符。在正则表达式中添加括号，括号的内容将在匹配时被忽略。但是，在 findall()函数中括号表示的是匹配括号内的整个表达式。在抽取与正则表达式匹配的子字符串部分，findall()函数适用。

这样，修改之后的程序代码如下：

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    x = re.findall('^X\S*: ([0-9.]+)', line)
    if len(x) > 0 :
        print x
```

与search()函数不同，我们在正则表达式中添加括号来表示浮点数，指明我们只需要findall()函数找出匹配到的字符串的浮点数。

程序运行结果如下：

```
['0.8475']
['0.0000']
['0.6178']
['0.0000']
['0.6961']
['0.0000']
```

数字仍然是存在列表中，需要把字符串转换为浮点数。这里侧重展示正则表达式可以同时进行搜索与抽取的功能实现。

如果文件包含如下形式的文本行，使用这种方法的另一个例子如下：

```
Details: http://source.sakaiproject.org/viewsvn/?view=rev&rev=39772
```

如果想要抽取所有的修订号（每一行末尾的整数值），程序代码如下：

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    x = re.findall('^Details:.*rev=([0-9]+)', line)
    if len(x) > 0:
        print x
```

解释一下这个正则表达式，以“Details:”开头，之后是任意字符“.*”，然后是“rev=”，最后是零或多个数字。我们只需要文本行最后的整数值，所以用括号把[0-9]+括起来。

程序运行结果如下：

```
['39772']
['39771']
['39770']
['39769']
...
```

请记住，“[0-9]+”是“贪婪的”，在抽取这些数字之前，它试图匹配尽可能多的符合条件的字符串。这个贪婪行为是获得5位数字的原因所在。正则表达式库进行了前后扩展，直到它在开头或结尾遇到一个非数字才停止匹配。

现在，我们可以使用正则表达式重做之前的邮件消息中的时间提取。文本内容如下：

```
From stephen.marquard@uct.ac.za Sat Jan  5 09:14:16 2008
```

此处抽取每一行中当天的小时。之前的做法是调用split两次。第一次，文本行分解为单词，取出第五个单词，将其再次用冒号分解。最后，取出我们需要的前两个字符。

虽然这样做达到了目标，但在代码编写时缺乏一定灵活性，前提是文本需要经过良好的格式化。如果增加足够的错误检查或一大块的try/except代码，确保程序在遇到格式不正确的文本行时不会出错，代码会增长到10-15行，那就不太好阅读了。

使用下面的正则表达式可以更容易地做到这一点：

```
^From .* [0-9][0-9]:
```

解释一下这个正则表达式，以“From ”开头（注意空格），之后是任意多个字符“.*”，然后空一格，接着是2位数字“[0-9][0-9]”，最后是一个冒号。这样的定义符合之前想要寻找的内容。

为了只取出小时数，使用findall()方法，在两位数字上加括号，正则表达式如下：

```
^From .* ([0-9][0-9]):
```

程序代码如下：

```
import re
hand = open('mbox-short.txt')
for line in hand:
    line = line.rstrip()
    x = re.findall('^From .* ([0-9][0-9]):', line)
    if len(x) > 0 : print x
```

程序运行结果如下：

```
['09']
['18']
['16']
['15']
...
```

## 11.4 转义字符

由于在正则表达式中使用特殊字符来匹配一行的开头与结尾，或指定通配符，那么需要一种方法来保证这些特殊字符本身的指代性，例如匹配$与^符号本身。通过在字符前使用反斜杠作为前缀可以轻松解决这个问题。例如，使用以下正则表达式找出金额数。

```
import re
x = 'We just received $10.00 for cookies.'
y = re.findall('\$[0-9.]+',x)
```

由于$符号之前有一个反斜杠，它实际上匹配的是美元符号本身，不是匹配一行的结尾，正则表达式的其他部分匹配一个或多个数字和句点。请注意，方括号内，字符没有特殊性。因此，[0-9.]实际表示数字和句点。方括号之外，句点是一个通配符，匹配任意字符。在方括号之内，句点就代表它本身。

## 11.5 小结

虽然本章只触及了正则表达式的皮毛，但我们已经对正则表达式这门语言有所了解。包含特殊字符的搜索字符串能够按照意愿，构建正则表达式来定义匹配的字符和想要抽取的内容。以下是一些特殊字符和字符序列：

^ 匹配文本行的开头。

$ 匹配文本行的结尾。

. 匹配任一字符（一个通配符）。

\s 匹配一个空白字符。

\S 匹配一个非空字符（与\s相反）。

\* 应用于前接字符，表示前接字符的零个或多个匹配。

*? 应用于前接字符，以非贪婪模式，表示前接字符的零个或多个匹配。

\+ 应用于前接字符，表示前接字符的一个或多个匹配。

+? 应用于前接字符，以非贪婪模式，表示前接字符的一个或多个匹配。

[aeiou] 匹配指定字符集中的一个字符。这里只能是“a”、“e”、 “i”、 “o”或 “u”，不接受其他字符。

[a-z0-9] 使用减号指定字符区间。这里表示一个字符，必须是小写字母或数字。

\[^A-Za-z] 第一个字符是^，它表示反向逻辑。这里匹配除了大小写字符之外的其他任意字符。

( )在正则表达式中添加括号，括号内容会丧失匹配功能，但在findall()中可以用于抽取特定部分的字符串，而不是整个字符串。

\b 匹配空字符串，仅用于单词的首尾。

\B 匹配空字符串，但不能用于单词的首尾。

\d 匹配任意十进制数字，等价于[0-9]。

\D 匹配任意非数字字符，等价于[^0-9]。

##11.6 Unix用户福利

自20世纪60年以后，Unix操作系统内置了文件搜索的正则表达式功能。它几乎在所有编程语言中通用，只是细节上有所差别。

事实上，Unix内置了一个命令行工具，称为grep（Generalized Regular Expression Parser，通用正则表达式解析器），可以实现本章search()在示例中的相同作用。如果使用Mac或Linux操作系统，你可以在命令行窗口中执行以下语句。

```
$ grep '^From:' mbox-short.txt
From: stephen.marquard@uct.ac.za
From: louis@media.berkeley.edu
From: zqian@umich.edu
From: rjlowe@iupui.edu
![Alt text](./1434462994117.png)
```

这条命令告诉grep，显示mbox-short.txt文件中以“From:”开头的字符串。如果尝试使用grep命令和阅读grep的文档，你会发现Python支持的正则表达式与grep支持的正则表达式存在一些细微差别。例如，grep不支持非空字符“\S”，所以需要使用稍微复杂一点的集合符号“\[^ ]”，表示匹配非空格的任意字符。

## 11.7 调试

Python包含一些简单明了的内置文档，有助于快速掌握和记忆特定方法的确确切名称。这些文档可以在Python解析器的交互模式下查看。

```
>>> help()

Welcome to Python 2.6!  This is the online help utility.

If this is your first time using Python, you should definitely check out
the tutorial on the Internet at http://docs.python.org/tutorial/.

Enter the name of any module, keyword, or topic to get help on writing
Python programs and using Python modules.  To quit this help utility and
return to the interpreter, just type "quit".

To get a list of available modules, keywords, or topics, type "modules",
"keywords", or "topics".  Each module also comes with a one-line summary
of what it does; to list the modules whose summaries contain a given word
such as "spam", type "modules spam".

help> modules
```

如果你知道需要使用的模块名称，使用dir()命令查找这个模块的方法，如下所示：

```
>>> import re
>>> dir(re)
[.. 'compile', 'copy_reg', 'error', 'escape', 'findall',
'finditer', 'match', 'purge', 'search', 'split', 'sre_compile',
'sre_parse', 'sub', 'subn', 'sys', 'template']
```

你也可以使用dir命令来查找特定方法的一小部分文档。

```
>>> help (re.search)
Help on function search in module re:

search(pattern, string, flags=0)
    Scan through string looking for a match to the pattern, returning
    a match object, or None if no match was found.
>>>
```
虽然内置的文档不够详尽，但在急需或没有网络浏览器和搜索引擎的情况下备用。

## 11.8 术语

**脆弱代码：**当输入数据是一种特定格式，如果格式上有一点偏差，代码很容易出问题。由于容易出错，这种代码被称为“脆弱代码”。

**贪婪匹配：**正则表达式中“+”和“*”采用外向扩展，匹配最大可能的字符串。

**grep：**在大多数Unix操作系统中可以使用的命令，搜索文本文件，通过正则表达式一行行进行匹配。该命令的全称是通用正则表达式解析器（Generalized Regular Expression Parser）。

**正则表达式：**表达复杂搜索字符串的语言。正则表达式可能包含特定字符串，指明搜索只匹配开头或结尾，以及其他许多类似的功能。

**通配符：**匹配任意字符的特殊字符。例如，在正则表达式中，句点是一个通配符。

## 11.9 练习

**习题11.1** 编写一段简单程序，模拟Unix的grep命令操作。要求用户输入一个正则表达式，统计出正则表达式匹配的文本行数。

```
$ python grep.py
Enter a regular expression: ^Author
mbox.txt had 1798 lines that matched ^Author

$ python grep.py
Enter a regular expression: ^X-
mbox.txt had 14368 lines that matched ^X-

$ python grep.py
Enter a regular expression: java$
mbox.txt had 4218 lines that matched java$
```

**习题11.2** 编写一个程序，查找“New Revision: 39772”所在的文本行。使用正则表达式的findall()函数抽取每一行中的数字，计算并输出数字的平均值。

```
Enter file:mbox.txt
38549.7949721

Enter file:mbox-short.txt
39756.9259259
```
