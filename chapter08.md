# 第8章 列表

## 8.1 列表即序列

与字符串类似，列表是由若干值组成的序列。字符串中的值是字符；列表中的值可以是任何类型。列表中的值称为元素或数据项。

列表的创建方法有好几种，其中最简单的是用方括号[]将元素括起来：

```
[10, 20, 30, 40]
['crunchy frog', 'ram bladder', 'lark vomit']
```
第一个例子是由四个整数组成的列表，第二个例子是由三个字符串组成的列表。列表的元素不要求是同一类型的。下面的列表包含一个字符串，一个浮点数，一个整数，以及另一个列表：

```
['spam', 2.0, 5, [10, 20]]
```

一个列表作为另一个列表的元素称为列表嵌套。

不含任何元素的列表称为空列表，使用空的方括号（[]）创建一个空列表。

正如你可能期望的，可以把列表值赋给变量：

```
>>> cheeses = ['Cheddar', 'Edam', 'Gouda']
>>> numbers = [17, 123]
>>> empty = []
>>> print cheeses, numbers, empty
['Cheddar', 'Edam', 'Gouda'] [17, 123] []
```

## 8.2 列表是可变的

列表元素的访问与字符串中字符的访问语法是一样的，使用方括号操作符。方括号内的表达式指定索引位置。请注意，索引从0开始：

```
>>> print cheeses[0]
Cheddar
```

与字符串不同，列表是可变的。你可以改变列表中元素的顺序，或者对列表中的元素重新赋值。当括号运算符出现在赋值语句的左边时，就可以给列表中指定的元素赋值。

```
>>> numbers = [17, 123]
>>> numbers[1] = 5
>>> print numbers
[17, 5]
```

numbers列表的第二个元素之前是123，现在是5。

可以这样理解，列表是索引与元素之间的一种关系。这种关系称为映射，每一个索引对应一个元素。

列表与字符串的索引用法相同：

* 任何整数表达式都可作为索引。
* 试图读写一个不存在的元素时，你会得到IndexError索引错误提示。
* 如果索引值为负，表示从列表的尾部算起。

in运算符也适用于列表：

```
>>> cheeses = ['Cheddar', 'Edam', 'Gouda']
>>> 'Edam' in cheeses
True
>>> 'Brie' in cheeses
False
```

## 8.3 列表的遍历

遍历列表元素最常用的方法是使用for循环。遍历语法与字符串遍历相同：

```
for cheese in cheeses:
    print cheese
```

如果只需遍历列表的元素，这个方法就足够了。但如果想写入或更新元素，这时就需要索引。一个常见方法是range函数和len函数的结合使用：

```
for i in range(len(numbers)):
    numbers[i] = numbers[i] * 2
```

这个循环可以遍历并更新列表的每个元素。len函数返回列表中的元素个数。range函数返回一个索引列表（取值从0到n-1），其中n是列表的长度。对i进行循环，得到下一个元素的索引。函数体中的赋值语句使用i读取元素的旧值，然后给它赋予新值。

对于空列表来说，for循环不会执行函数体。

```
for x in empty:
    print 'This never happens.'
```

尽管一个列表可以包含另一个列表，但被包含的列表只能被看作一个元素。以下列表的长度为4：

```
['spam', 1, ['Brie', 'Roquefort', 'Pol le Veq'], [1, 2, 3]]
```

## 8.4 列表的操作

 “+”运算符连接多个列表：

```
>>> a = [1, 2, 3]
>>> b = [4, 5, 6]
>>> c = a + b
>>> print c
[1, 2, 3, 4, 5, 6]
```

类似地，“*”运算符对列表进行给定次数的重复：

```
>>> [0] * 4
[0, 0, 0, 0]
>>> [1, 2, 3] * 3
[1, 2, 3, 1, 2, 3, 1, 2, 3]
```

第一个例子将列表[0]重复了四次，第二个例子将列表[1,2,3]重复了三次。

## 8.5 列表的分割

切片操作也适用于列表：

```
>>> t = ['a', 'b', 'c', 'd', 'e', 'f']
>>> t[1:3]
['b', 'c']
>>> t[:4]
['a', 'b', 'c', 'd']
>>> t[3:]
['d', 'e', 'f']
```

如果省略第一个索引，切片将从列表头部开始；如果省略第二个索引，切片将处理到列表的末尾。如果两个索引参数都被省略，将会得到整个列表。

```
>>> t[:]
['a', 'b', 'c', 'd', 'e', 'f']
```

由于列表是可变的，在进行列表折叠、拉长和截断等操作之前，最好保留一份列表的副本。

赋值语句左边的切片操作可以更新多个元素：

```
>>> t = ['a', 'b', 'c', 'd', 'e', 'f']
>>> t[1:3] = ['x', 'y']
>>> print t
['a', 'x', 'y', 'd', 'e', 'f']
```

## 8.6 列表的操作方法

Python提供了一些操作列表方法。例如，使用append在列表尾部添加一个新元素。

```
>>> t = ['a', 'b', 'c']
>>> t.append('d')
>>> print t
['a', 'b', 'c', 'd']
```

extend可以将列表作为参数，并把一个列表的所有元素添加到另一个列表的尾部。

```
>>> t1 = ['a', 'b', 'c']
>>> t2 = ['d', 'e']
>>> t1.extend(t2)
>>> print t1
['a', 'b', 'c', 'd', 'e']
```

这个示例中t2没有发生改变。

sort可以将列表元素从低到高排序。

```
>>> t = ['d', 'c', 'e', 'b', 'a']
>>> t.sort()
>>> print t
['a', 'b', 'c', 'd', 'e']
```

大多数列表方法是没有返回值的，它们会修改列表，但返回为None。如果你不小心写了下面这样的语句`t = t.sort()`，得到的t不是你所预期的排序。

## 8.7 删除元素

删除列表元素的方法有好几种。如果知道元素的索引位置，使用pop方法进行删除：

```
>>> t = ['a', 'b', 'c']
>>> x = t.pop(1)
>>> print t
['a', 'c']
>>> print x
b
```

上面的程序使用pop修改了列表，打印输出了被删除的元素。如果没有指定索引位置，pop会删除并返回最后一个元素。

如果无需返回删除过的元素，可以直接使用del操作符。

```
>>> t = ['a', 'b', 'c']
>>> del t[1]
>>> print t
['a', 'c']
```
如果只知道要删除的元素，但不知道它的索引位置，可以使用remove方法：

```
>>> t = ['a', 'b', 'c']
>>> t.remove('b')
>>> print t
['a', 'c']
```

remove方法返回值为None。

当需要删除多个元素，可以根据切片索引，使用del来实现：

```
>>> t = ['a', 'b', 'c', 'd', 'e', 'f']
>>> del t[1:5]
>>> print t
['a', 'f']
```

一般认为，切片会删除两个索引位置之间的所有元素，实际上切片不会删除第二个索引参数所对应的元素。

## 8.8 列表与函数

列表有许多内嵌函数可用来遍历，无需另写循环代码：

```
>>> nums = [3, 41, 12, 9, 74, 15]
>>> print len(nums)
6
>>> print max(nums)
74
>>> print min(nums)
3
>>> print sum(nums)
154
>>> print sum(nums)/len(nums)
25
```
当列表元素为数字时，sum()函数才会起作用。其他函数如max()、len()等对字符串列表和其他可进行比较的数据类型才会起作用。

我们重写先前计算数字列表平均值的程序。用户输入一个列表，程序输出列表的平均值。

首先，不使用列表计算平均值的程序如下：

```
total = 0
count = 0
while ( True ) :
    inp = raw_input('Enter a number: ')
    if inp == 'done' : break
    value = float(inp)
    total = total + value
    count = count + 1

average = total / count
print 'Average:', average
```

在这个程序中，不断提示用户输入数据时，我们使用count和sum两个变量来记录数值个数以及求和。

我们只需记录下用户输入的数据，在循环结束后，利用内嵌的函数计算总和和个数。

```
numlist = list()
while ( True ) :
    inp = raw_input('Enter a number: ')
    if inp == 'done' : break
    value = float(inp)
    numlist.append(value)

average = sum(numlist) / len(numlist)
print 'Average:', average
```

在循环开始之前，首先建立一个空列表，每得到一个数据，就把它添加到列表的尾部。在程序最后，只需将列表中数据的总和除以列表的长度，就可以求出平均值。

## 8.9 列表与字符串

字符串是字符的序列，而列表是一系列值的序列。字符列表与字符串是不同的。我们可利用list方法，把字符串转换成字符列表：

```
>>> s = 'spam'
>>> t = list(s)
>>> print t
['s', 'p', 'a', 'm']
```

由于list是一个内置函数，应避免使用list作为变量名。笔者也会避免使用l作为变量名，因为容易和数字1搞混，这也就是为什么选择t作为变量名的原因。

list函数将一个字符串转化成一些单独的字母。如果想把一个字符串分成单独的单词，使用split函数：

```
>>> s = 'pining for the fjords'
>>> t = s.split()
>>> print t
['pining', 'for', 'the', 'fjords']
>>> print t[2]
the
```

一旦使用split函数将字符串分解成由单词组成的序列，你就可以利用索引操作符（方括号）来访问列表中的特定单词了。

split函数有一个可选参数，称为分隔符（delimiter）。它可以指定特定字符作为单词之间的界限。以下示例将连字符“-”作为分隔符：

join函数与split函数的作用相反。它使用字符串列表，把列表元素连接起来。join是字符串方法，所以必须指定分隔符，将列表作为参数。

```
>>> t = ['pining', 'for', 'the', 'fjords']
>>> delimiter = ' '
>>> delimiter.join(t)
'pining for the fjords'
```

在这个例子中，分隔符是一个空格。所以join函数会在两个单词之间加一个空格。如要连接字符串无需间隔，可以使用空字符串（''）作为分隔符。

## 8.10 行间解析

```
From stephen.marquard@uct.ac.za  Sat Jan  5 09:14:16 2008
```

split方法处理此类问题非常有效。先编写一个小程序，找到以“from”开头的句子，然后把这些句子分解，最后输出句子中的第三个单词：

```
fhand = open('mbox-short.txt')
for line in fhand:
    line = line.rstrip()
    if not line.startswith('From ') : continue
    words = line.split()
    print words[2]
```

我们也可以通过在If语句的同一行放置continue，作为if语句的简化形式。从作用上讲，if函数的这种简化形式与continue在下一行的缩进形式是一样的。

程序运行结果如下：

```
Sat
Fri
Fri
Fri
    ...
```

后续章节会介绍更复杂的行间文本提取技术，以及如何分解文本行，从中找到我们所需要的准确信息。

## 8.11 对象与值

执行以下赋值语句：

```
a = 'banana'
b = 'banana'
```

a和b都指向一个字符串，但它们所指向的是不是同一个字符串呢？存在以下两种情况：

![enter image description here](http://www.pythonlearn.com/html-270/book012.png)

一种情况是，a和b指向具有相同值的两个不同对象。另一种情况是，它们指向同一个对象。

为检验到底属于哪一种情况，使用is运算符;

```
>>> a = 'banana'
>>> b = 'banana'
>>> a is b
True
```

在这个例子中，Python只创建了一个对象，a和b都指向它。

如果创建两个列表就会得到两个对象：

```
>>> a = [1, 2, 3]
>>> b = [1, 2, 3]
>>> a is b
False
```

这种情况下，因为它们拥有相同的元素，我们可以说这两个列表是等价的，但不能说它们是同一个。它们不是相同的对象。当然，如果两个对象是同一个，那它们肯定是等价的，反之不成立。

截止目前，我们还在交替使用“对象”和“值”。更严谨的提法是，对象拥有值。当执行语句`a = [1,2,3]`，a会指向一个列表对象。这个列表对象的值是一个特定元素序列。此时，如果有另外一个列表与它的元素相同，那只能说它们拥有相同的值。

## 8.12  别名引用

如果a指向一个对象，当执行`b=a`后，两个变量都将指向同一个对象：

```
>>> a = [1, 2, 3]
>>> b = a
>>> b is a
True
```

变量与对象之间的关系称为引用。在这个例子中，同一个对象有两个引用。

拥有多个引用的对象就会有多个名称，这种现象称作对象被赋予了别名。

如果别名化的对象是可变的，那么一个别名的变化，将会影响到其他别名的引用。

```
>>> b[0] = 17
>>> print a
[17, 2, 3]
```

尽管这一行为是有用的，但也容易造成错误。一般而言，可变的对象最好不要使用别名。

像字符串这类不可变对象来说，别名并不会造成很大问题。如下所示：

```
a = 'banana'
b = 'banana'
```

a和b是否指向同一个字符串，这几乎没差别。

## 8.13 列表参数

当把一个列表传递给一个函数时，函数就会得到该列表的一个引用。如果这个函数改变了一个列表参数，调用者会知道这个变化。例如，delete_head删除了列表的第一个元素：

```
def delete_head(t):
    del t[0]
```

下面是它的用法：

```
>>> letters = ['a', 'b', 'c']
>>> delete_head(letters)
>>> print letters
['b', 'c']
```

上述参数t与变量letters是同一个对象的别名。

区分修改列表与新建列表的操作，这是非常重要的。例如，下面的示例中，append方法修改了列表，而“+”运算符新建了一个列表：

```
>>> t1 = [1, 2]
>>> t2 = t1.append(3)
>>> print t1
[1, 2, 3]
>>> print t2
None

>>> t3 = t1 + [3]
>>> print t3
[1, 2, 3]
>>> t2 is t3
False
```

在编写列表的修改函数时，这种区别显得非常重要。例如，下面的函数就并没有达到删除列表头元素的目的：

```
def bad_delete_head(t):
    t = t[1:]              # WRONG!
```

切片操作会新建一个列表，赋值语句将t作为列表的引用。但这对于之前作为参数的列表来说没有任何影响。

另一种可行的办法是编写一个函数，创建并且返回一个新列表。例如，tail函数会返回一个新的列表，其包含除第一个元素之外的其他所有元素。

```
def tail(t):
    return t[1:]
```

这个函数并不会改变原始列表。以下是它的用法：

```
>>> letters = ['a', 'b', 'c']
>>> rest = tail(letters)
>>> print rest
['b', 'c']
```

**习题8.1** 编写chop函数，移除列表的头元素和尾元素，并返回None值。

然后，编写middle函数，移除列表的头元素和尾元素，返回一个新列表。

## 8.14 调试

列表及其他可变对象的不谨慎使用，可能会造成长时间的调试。下面是一些常见陷阱以及如何避免的方法：

1） 大多数列表的方法会修改参数并返回None值，这与字符串的方法是相同。字符串的方法通常会返回一个新字符串，并不会改变原始的字符串。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果你习惯如下的字符串代码的编写方式：

```
word = word.strip()
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;那你有可能写出如下的列表代码：

```
t = t.sort()           # WRONG!
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为sort函数返回None值，所以接下来赋予变量t会执行失败。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用列表的方法和操作符之前，你应该仔细阅读文档，然后在Python的交互模式中测试。列表与其他序列（如字符串）共同拥有的方法和操作文档位于<http://docs.python.org/lib/typesseq.html>。可变序列独有的方法与操作文档位于<http://docs.python.org/lib/typesseq-mutable.html>。

2） 坚持并养成一种编写习惯

列表的部分问题是由于多种方式都可以达到相同目的造成的。比如，删除列表元素的方法有pop、remove、del甚至分片等。

若添加一个元素，你也可以使用append方法或“+”运算符。请不要忘记，下面的写法是正确的：

```
t.append(x)
t = t + [x]
```

而下面的写法是错误的：

```
t.append([x])          # WRONG!
t = t.append(x)        # WRONG!
t + [x]                # WRONG!
t = t + x              # WRONG!
```

请在Python交互模式中逐一练习，确保自己搞清楚了。还有，上面例子中只有最后一个会造成运行错误，其他三个可以运行，只不过没有按照我们的预期运行。

3）保留列表副本，避免直接引用

如果使用像sort这样会改变参数的方法，还要保留原始列表，复制一份列表副本。

```
orig = t[:]
t.sort()
```

在这个例子中，你也可以使用列表内置的sorted函数。这个函数会返回一个排序后的新列表，而不会改变原始列表。在这种情况下，应避免将sorted用于变量命名。

4） 列表、切片与文件

当读取和解析文件时，可能有一些输入会破坏我们的程序。编写一个程序来读取文件，进行“大海捞针”式找寻，这时打开守护模式是很有必要的。

让我们重温之前的行间读取程序，找出以From开头的行中包含的星期时间：

```
From stephen.marquard@uct.ac.za  Sat Jan  5 09:14:16 2008
```

由于我们将一行分解成了一个个单词，可以使用startwith函数，只需查看每一行的第一个单词，就可以决定它是否符合要求。另外，还可以使用continue来跳过不是以From开头的文本行，代码如下所示：

```
fhand = open('mbox-short.txt')
for line in fhand:
    words = line.split()
    if words[0] != 'From' : continue
    print words[2]
```

这看起来更简单了，甚至都不需要运行rstrip来删除文件末尾的换行符。但这样就真得更好了吗？

```
python search8.py
Sat
Traceback (most recent call last):
  File "search8.py", line 5, in <module>
    if words[0] != 'From' : continue
IndexError: list index out of range
```

这个程序会部分运行成功，我们可以顺利地把第一个符合要求的行的第一个单词输出，但随后程序会中止，输出一个追踪错误。哪里出现问题了？到底是什么样的混乱数据，使得看似优雅灵活的Python程序出错？

你也许会较真，陷入深深的思考，或寻求他人帮助。最快捷和有效的方法是，添加一个print语句。添加语句的最佳位置在程序出错那一行之前，这样有可能打印出导致出错的数据。

虽然这个方法会输出大量的行，但至少可以快速找到问题解决的一些线索。因此，我们在第五句前面添加了一条打印变量words的语句。我们甚至还加了一个“Debug:”前缀，这样做是为了与其他正常的输出区分开来。

```
for line in fhand:
    words = line.split()
    print 'Debug:', words
    if words[0] != 'From' : continue
    print words[2]
```

运行这个程序，屏幕会出现大量滚动式输出。在输出的末尾，我们只需要查看调试输出和追踪错误信息，便可知道在追踪模块之前发生了什么。

```
Debug: ['X-DSPAM-Confidence:', '0.8475']
Debug: ['X-DSPAM-Probability:', '0.0000']
Debug: []
Traceback (most recent call last):
  File "search9.py", line 6, in <module>
    if words[0] != 'From' : continue
IndexError: list index out of range
```

每一个调试行都会输出单词列表，这些单词是对文本行进行分解后得到的。当程序出错，单词列表为空[]。这时，在文本编辑器中打开文件，内容显示如下：

```
X-DSPAM-Result: Innocent
X-DSPAM-Processed: Sat Jan  5 09:14:16 2008
X-DSPAM-Confidence: 0.8475
X-DSPAM-Probability: 0.0000

Details: http://source.sakaiproject.org/viewsvn/?view=rev&rev=39772
```

程序遇到了一个空行，错误原来在这里。很显然，空行里是没有单词的。我们在写代码时怎么就没有考虑到这个问题呢？当程序试着对空行的第一个单词（word[0]）进行“From”匹配时，由于没有找到单词，所以就出现“索引范围超出”的错误。

在第五行添加守护代码，避免对不存在第一个单词的空行进行比对，这个位置是最好的。还有许多方法来守护这个代码，我们会选择在查看第一个词之前，检查单词个数：

```
fhand = open('mbox-short.txt')
count = 0
for line in fhand:
    words = line.split()
    # print 'Debug:', words
    if len(words) == 0 : continue
    if words[0] != 'From' : continue
    print words[2]
```

首先，我们注释掉用于调试的print语句，不要直接删除它。这样做可以让修改失败时不必再重新调试。然后，我们添加一个守护语句，检查是否存在空单词，如果有的话，使用continue语句跳到文件的下一行。

这里使用了两个continue语句，过滤出我们感兴趣并希望继续处理的文本行集合。没有单词的行不是我们想要的，所以跳到下一行。不是以From开头的行也不符合要求，所以跳过。

修改后的程序能够成功运行，也许它就是正确的了。守护语句确实有效地避免了空行问题，但那也许还远远不够，当我们编写程序的时候，编程时我们必须经常思考“那里可能出问题了”。

**习题8.2** 找出以上程序中哪一行仍然没有得到有效守护。你也可以试着创建一个导致程序运行失败的文本文件，然后修改程序，以确保每一行都得到了相应的守护，确保该程序能够正确处理你创建的文本文件。

**习题8.3** 重写上例中的守护语句。使用复合逻辑表达式，在if语句中使用and逻辑运算符。

## 8.15 术语

**别名：**两个或多个变量指向同一个对象的情形。

**分隔符：**用来确定字符串如何被分割的字符或子字符串。

**元素：** 列表（或其他序列）中的一个值，也称为数据项。

**等价：**拥有相同的值。

**同一性：**同一个对象，可以推断出等价。

**列表：**一系列值的序列。

**列表遍历：**按顺序依次访问列表中的元素。

**嵌套列表：**一个列表作为另一个列表的一个元素存在。

**引用：**变量与其取值之间的关联。

## 8.16 练习

**习题8.4**  从<http://www.py4inf.com/code/romeo.txt>下载一个文本文件。

编写一个程序，打开romeo.txt文件，按行读取。对每一行使用split函数，将其分解成一系列的单词列表。对于每一个单词，检查它是否已经存在于列表之中，若单词还未出现在列表中，把它添加进来。

程序运行结束，按字母顺序输出最终的单词清单。

```
Enter file: romeo.txt
['Arise', 'But', 'It', 'Juliet', 'Who', 'already',
'and', 'breaks', 'east', 'envious', 'fair', 'grief',
'is', 'kill', 'light', 'moon', 'pale', 'sick', 'soft',
'sun', 'the', 'through', 'what', 'window',
'with', 'yonder']
```

**习题8.5** 编写一个程序，读取邮箱数据，当遇到一个以From开头的文本行，使用split函数将该行子分解成单词。我们需要抽取From开头的行中第二个单词，即发信人。

```
From stephen.marquard@uct.ac.za Sat Jan 5 09:14:16 2008
```

解析以From开头的行，打印出每行中第二个单词，还可以统计发信人数，在结尾输出总数。

下面是程序运行结果的部分输出：

```
python fromcount.py
Enter a file name: mbox-short.txt
stephen.marquard@uct.ac.za
louis@media.berkeley.edu
zqian@umich.edu

[...some output removed...]

ray@media.berkeley.edu
cwen@iupui.edu
cwen@iupui.edu
cwen@iupui.edu
There were 27 lines in the file with From as the first word
```

**习题8.6** 改写前面那个提示用户输入数据，并在用户输入“done”后，输出最大值与最小值的程序。编写一个程序，使用列表储存用户输入的数据，在循环结束后，利用max()和min()函数分别计算出最大值和最小值。

```
Enter a number: 6
Enter a number: 2
Enter a number: 9
Enter a number: 3
Enter a number: 5
Enter a number: done
Maximum: 9.0
Minimum: 2.0
```
