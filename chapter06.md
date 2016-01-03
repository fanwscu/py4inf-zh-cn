# 第6章 字符串

## 6.1 字符串是字符的序列

字符串是若干字符的序列。你可以用方括号运算符逐一访问每个字符：

```
>>> fruit = 'banana'
>>> letter = fruit[1]
```
第二条语句从fruit变量中提取索引位置为1的字符，并把它赋予letter变量。

方括号里的表达式称为索引。索引可以指向字符序列中你想要的字符，作用如其名。

但是这并不是你想要的结果：

```
>>> print letter
a
```

大多数人认为'banana'的第一个字符是b，而不是a。但在Python中，索引是从字符串头部算起的一个偏移量，第一个字母的偏移量为0。

```
>>> letter = fruit[0]
>>> print letter
b
```

因此，b是'banana'的第零个字母，a是第一个字母，n是第二个字母。

![enter image description here](http://www.pythonlearn.com/html-270/book009.png)

索引可以是任何表达式，包括变量与运算符在内。但是，索引的值必须是整数，否则会得到如下错误信息：

```
>>> letter = fruit[1.5]
TypeError: string indices must be integers
```

## 6.2 使用len函数得到字符串的长度

len是内置函数，返回字符串的字符数：

```
>>> fruit = 'banana'
>>> len(fruit)
6
```

为了得到字符串中的最后一个字母，你可能会这样做：

```
>>> length = len(fruit)
>>> last = fruit[length]
IndexError: string index out of range
```

IndexError的错误依据是字符串’banana’没有与索引6对应的字母。既然是从零开始算起，六个字母的编号就是0到5。为了得到最后一个字母，对length值减1。

```
>>> last = fruit[length-1]
>>> print last
a
```

另一种方法是使用负索引，从字符串结尾倒过来计算。表达式fruit[-1]表示最后一个字母，fruit[-2]是倒数第二个字母，以此类推。

## 6.3 通过循环遍历字符串

按照一次一个字符的方式处理一个字符串需要花费大量的计算。通常从字符串头部开始，逐个选择每个字符，对其进行操作，然后继续下一个，直到结束。这种处理模式称为遍历（traversal）。遍历的一种写法是使用while循环:

```
index = 0
while index < len(fruit):
    letter = fruit[index]
    print letter
    index = index + 1
```

这个循环遍历了整个字符串，每行显示一个字母。循环条件是`index < len(fruit)` ，当index等于字符串长度时，循环条件为假，那么语句体就不会被执行。字符串最后一个字符的索引是`len(fruit)-1`。

**习题6.1** 编写一个while循环，从字符串的最后一个字符开始，反向逐一处理，直到字符串的第一个字符，一行显示一个字母。

遍历的另一种写法是用for循环：

```
for char in fruit:
    print char
```

每一次循环中字符串的下一个字符赋值给char变量。继续循环下去，直到没有字符了。

## 6.4 字符串分割

字符串的一个片段称为切片，字符切片与字符选择的方法相似：

```
>>> s = 'Monty Python'
>>> print s[0:5]
Monty
>>> print s[6:12]
Python
```

运算符[n:m] 返回字符串从第n到第m之间的字符，包括第一个字符，但不包括最后一个字符。

如果忽略第一个索引值(冒号之前)，切片就从字符串第一个字符开始计算。如果忽略第二个索引值，切片就计算到最后一个字符：

```
>>> fruit = 'banana'
>>> fruit[:3]
'ban'
>>> fruit[3:]
'ana'
```

如果第一个索引值大于第二个索引值导致空字符串，只会输出两个引号：

```
>>> fruit = 'banana'
>>> fruit[3:3]
"
```

空字符串不包含字符，其长度为0。除此之外，它和其它字符串没有差别。

**习题6.2**  假设fruit是一个字符串，那么fruit[:]表示什么？

## 6.5 字符串是不可变的

在赋值语句的左边使用[]运算符，尝试改变字符串中的字符。举例如下：

```
>>> greeting = 'Hello, world!'
>>> greeting[0] = 'J'
TypeError: object does not support item assignment
```

这个例子的对象是字符串，数据项就是你想要赋值的字符。现在，一个对象相当于一个值，等下会修正这个定义。数据项是序列中的一个值。

出错原因在于字符串是不可改变的。这意味着，你不能改变已经存在的字符串。最好的办法是在原字符串基础上新建一个字符串。

```
>>> greeting = 'Hello, world!'
>>> new_greeting = 'J' + greeting[1:]
>>> print new_greeting
Jello, world!
```

这个例子将新的首字母与greeting的切片连接在一起。这不会对原先的字符串造成影响。

## 6.6 循环与统计

下面的程序统计了字母a在字符串中出现的次数：

```
word = 'banana'
count = 0
for letter in word:
    if letter == 'a':
        count = count + 1
print count
```

这个程序演示了另一种计算模式，称为计数器。变量count初始值为0，每当发现一个a，count值就加一。当循环停止时，count就得到了结果，即a出现的总次数。

**习题6.3** 定义一个count函数并封装这段代码，对其进行通用化改造，能够接收字符串和字母作为参数。

## 6.7 in运算符

单词in是一个布尔运算符，对两个字符串进行比较，如果第一个字符串是第二个字符串的子串，则返回True。

```
>>> 'a' in 'banana'
True
>>> 'seed' in 'banana'
False
```

## 6.8 字符串比较

比较运算符适用于字符串。如何判断两个字符串等价：

```
if word == 'banana':
    print  'All right, bananas.'
```

其它比较运算符适用于字母排序：

```
if word < 'banana':
    print 'Your word,' + word + ', comes before banana.'
elif word > 'banana':
    print 'Your word,' + word + ', comes after banana.'
else:
    print 'All right, bananas.'
```

Python不能像人一样，区分大写字母和小写字母。所有的大写字母都在小写字母之前，因此结果如下：

```
Your word, Pineapple, comes before banana.
```

解决这个问题的常见方法是将字符串转换成一种标准格式，例如，在比较之前都转化为小写。

## 6.9 字符串方法

字符串是一种Python对象。一个对象包括数据（即字符串本身）和方法。这些方法是内置在对象中的有效函数，可以作用于对象的任一实例。

Python有一个dir函数，它可以列出对象所有可用的方法。type函数显示对象的类型，dir函数显示的是对象可用的方法。

```
>>> stuff = 'Hello world'
>>> type(stuff)
<type 'str'>
>>> dir(stuff)
['capitalize', 'center', 'count', 'decode', 'encode',
'endswith', 'expandtabs', 'find', 'format', 'index',
'isalnum', 'isalpha', 'isdigit', 'islower', 'isspace',
'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip',
'partition', 'replace', 'rfind', 'rindex', 'rjust',
'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines',
'startswith', 'strip', 'swapcase', 'title', 'translate',
'upper', 'zfill']
>>> help(str.capitalize)
Help on method_descriptor:

capitalize(...)
    S.capitalize() -> string

    Return a copy of the string S with only its first character
    capitalized.
>>>
```

当dir函数列出这些方法，你就可以用help获取关于这些方法的文档。有关字符串方法比较全面的文档详见<http://docs.python.org/library/string.html>。

调用方法与调用函数类似，都是传入参数，返回结果，但它们的语法是不同的。调用方法的语法是，使用句点作为分隔，在变量名后面跟上方法名。

例如，upper方法接收一个字符串，返回一个全部是大写字母的新字符串：

这次不使用upper(word)函数，换做word.upper()方法。

```
>>> word = 'banana'
>>> new_word = word.upper()
>>> print new_word
BANANA
```

这种点标记形式指明方法名为upper，将此方法应用于变量word。空的圆括号表示这个方法没有参数。

召唤一个方法称为调用。这个例子中，在word对象上调用了upper方法。

例如，字符串方法find，找到字符串中字符的所在位置：

```
>>> word = 'banana'
>>> index = word.find('a')
>>> print index
1
```

在这个例子中，我们在word对象上调用find方法，将待寻找的字母作为参数。

find方法不仅适用字符，还可以用于寻找子串：

```
>>> word.find('na')
2
```

find方法还可以设置第二个参数，从哪个索引位置开始查找：

```
>>> word.find('na', 3)
4
```

一个常见任务是利用strip方法移除字符串首尾的空白（包括空格、制表符和换行符）。

```
>>> line = '  Here we go  '
>>> line.strip()
'Here we go'
```

像startswith这样的方法返回的是布尔值。

```
>>> line = 'Please have a nice day'
>>> line.startswith('Please')
True
>>> line.startswith('p')
```

你会注意到，startswith方法对大小写敏感，在检查之前，使用lower方法将其全部转换为小写字母。

```
>>> line = 'Please have a nice day'
>>> line.startswith('p')
False
>>> line.lower()
'please have a nice day'
>>> line.lower().startswith('p')
True
```

最后一个例子中调用了lower方法，然后用startswith方法检查小写转换后的字符串是否以字母p开头。只要留意次序，我们就可以在一个表达式上运用多种方法。

**习题6.4** 字符串方法count与之前练习过的函数相似。请访问<http://docs.python.org/library/string.html>，查看这个方法的文档，编写一个方法调用，统计a在'banana'中出现的次数。

## 6.10 字符串解析

通常，我们想要在一个字符串中寻找它的子串。如下是一行结构化的字符串：

```
From stephen.marquard@ uct.ac.za Sat Jan  5 09:14:16 2008
```

我们只想抽出电子邮件的第二部分（即uct.ac.za），可以通过find方法和字符串切片来实现。

首先，在字符串中找到@符号的位置。其次，找到@符号之后第一个空格所在的位置。最后，再用字符串切片来提取字符串中我们需要的部分。

```
>>> data = 'From stephen.marquard@uct.ac.za Sat Jan  5 09:14:16 2008'
>>> atpos = data.find('@')
>>> print atpos
21
>>> sppos = data.find(' ',atpos)
>>> print sppos
31
>>> host = data[atpos+1:sppos]
>>> print host
uct.ac.za
>>>
```

这里使用的是find方法的一种用法，让我们能指定find方法从何处开始寻找。当切分字符串时，我们提取的字符是“位于@符号之后但不包括空格”的字符。

有关find方法的文档，请访问<http://docs.python.org/library/string.html>。


## 6.11 格式操作符

格式操作符%可以构建字符串，使用变量中存储的数据来替代字符串的一部分。对整数而言，%是模运算符。如果第一个操作对象是字符串，那么%就是格式操作符。

第一个操作对象是格式字符串，它包含一个或多个格式化序列，用来指定第二个操作对象的格式。最终处理结果是字符串。

例如，格式序列'%d'表示第二个操作对象会被格式化为整数型（d表示十进制）：

```
>>> camels = 42
>>> '%d' % camels
'42'
```

运行的结果是字符串'42'，不要与整数42搞混了。

格式序列可以出现在字符串中的任意位置，所以你可以在一个语句中嵌入一个值：

```
>>> camels = 42
>>> 'I have spotted %d camels.' % camels
'I have spotted 42 camels.'
```

如果字符串中存在多个格式序列，那么第二个参数必须是元组。每个格式序列与元组的元素依次对应。

下面的例子使用'%d'格式化整数，'%g'格式化浮点数（不要问为什么），'%s'格式化字符串：

```
>>> 'In %d years I have spotted %g %s.' % (3, 0.1, 'camels')
```
元组中元素的数量必须与字符串中的格式序列数量一致。另外，元素的类型也要与格式序列匹配：

```
>>> '%d %d %d' % (1, 2)
TypeError: not enough arguments for format string
>>> '%d' % 'dollars'
TypeError: illegal argument type for built-in operation
```

第一个例子中元素的数量不够，第二个例子中元素的格式是错的。

格式运算符很强大，但不那么容易上手。如需了解更多，请访问<http://docs.python.org/lib/typesseq-strings.html>。

## 6.12 调试

编程时经常问问自己， “这里可能出现什么样的错误？”或者“我们的用户可能会做怎样疯狂的事情使（看似）完美的程序崩溃？”。这是需要长期培养的编程意识。

例如，第5章迭代中介绍while循环的程序示例如下：

```
while True:
    line = raw_input('> ')
    if line[0] == '#' :
        continue
    if line == 'done':
        break
    print line

print 'Done!'
```

当用户输入一个空行会发生什么：

```
> hello there
hello there
> # don't print this
> print this!
print this!
>
Traceback (most recent call last):
  File "copytildone.py", line 3, in <module>
    if line[0] == '#' :
```

输入空行之前代码运行正常。由于没有第0位字符，我们得到了异常信息反馈。两种方法可以解决这个问题，即使这一行为空，仍然能保证“安全”运行。

一种方法是使用startswith方法，如果字符串为空就返回False。

另一种方法是使用守护模式，通过一条if语句进行控制，保证第二个逻辑表达式只有在字符串中至少有一个字符时进行判断。

```
if len(line) > 0 and line[0] == '#' :
```

## 6.13 术语

计数器：用来统计的变量。通常初始化为零，然后累增。

空字符串：不包含字符、长度为零的字符串，用两个引号表示。

格式操作符：%操作符对格式字符串和元组进行操作，根据特定格式字符串，对元组元素进行格式化后生成一个字符串。

格式序列：格式字符串中的字符序列，例如，%d，它表示一个值应该如何进行格式化。

格式字符串：使用了格式操作符的字符串，它包含了格式序列。

标记：用来表示条件是否为真的布尔变量。

调用：方法的召唤语句。

不可变：序列的一种属性，序列中的数据项不能被赋值。

索引：用来选择序列中数据项的一个整数值，例如，表示字符串中一个字符的位置。

数据项：序列中的一个值。

方法：与对象相关的函数，使用句点来调用。

对象：变量可以引用的东西。现在，你可以交替使用“对象”或者“值”。

搜索：找到所要找的内容才会停止的一种遍历模式。

序列：一个有序集合，集合中的每个值通过一个整数索引定位。

切片：根据索引区间指定字符串的一部分。

遍历：遍历序列中的数据项，对每个数据项执行类似的操作。

## 6.14 练习

**习题6.5** 使用以下语句存储一个字符串：

```
str = ’X-DSPAM-Confidence:  0.8475’
```

使用find方法和字符串切片，提取出字符串中冒号后面的部分，然后使用float函数，将提取出来的字符串转换为浮点数。

**习题6.6** 访问<http://docs.python.org/lib/string-methods.html>，阅读字符串方法的文档。 你可能想要拿它们操作一下，以便理解它们的工作原理。字符串方法中的strip和replace特别有用。

文档中使用的语法可能会使人困惑。例如，在find(sub[, start[, end]])中，方括号表示可选的参数。sub是必需的，start是可选的；如果包含了start，那么end就是可选的。
