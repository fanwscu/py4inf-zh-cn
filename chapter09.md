# 第9章 字典

字典很像列表，但更通用。列表中的索引位置必须为整数，而字典中索引几乎可以是任意类型。

字典可看作是索引（这里称为*键*）的集合与值的集合之间存在的一种映射。每个键对应一个值，键与值之间的关系称之为*键值对*，有时也称为*数据项*。

举例来说，我们创建一个“英语-西班牙语”字典，其中键与值都是字符串。

dict函数可以创建一个空字典。请注意，由于dict是一个内置函数名称，不能把它用作变量名。

```
>>> eng2sp = dict()
>>> print eng2sp
{}
```

大括号{}表示一个空字典。你可以使用方括号向字典里添加数据项。

```
>>> eng2sp['one'] = 'uno'
```

这行语句创建了一个从键“one”到值“uno”映射的字典项。如果再次打印这个字典，我们会看到一个键值对，键与值之间用冒号隔开。

```
>>> print eng2sp
{'one': 'uno'}
```

字典的输入形式与输出形式是一样的。举例来说，你可以创建一个包含三个数据项的字典：

```
>>> eng2sp = {'one': 'uno', 'two': 'dos', 'three': 'tres'}
```

但是，如果你直接打印eng2sp，其结果会出人意料：

```
>>> print eng2sp
{'one': 'uno', 'three': 'tres', 'two': 'dos'}
```

输出的键值对顺序发生了改变。事实上，如果你在计算机上输入这个字典，得到的输出结果跟上面的顺序可能也不一样。一般而言，字典的顺序往往是不可预测的。

即便如此，这不会造成什么问题。因为字典项并不是用整数来索引的，而是采用键来查找对应的值。

```
>>> print eng2sp['two']
'dos'
```

键“two”对应的值是“dos”，这与字典项的顺序无关。

如果查找的键不在字典里，你会得到一个异常提示：

```
>>> print eng2sp['four']
KeyError: 'four'
```

len函数也适用于字典，它会返回字典中键值对的个数。

```
>>> len(eng2sp)
3
```

in操作符也适用于字典，它会告知你查找的东西是否作为*键*存在于字典中，但不能对是否是*值*作出很好的判断。

```
>>> 'one' in eng2sp
True
>>> 'uno' in eng2sp
False
```

为了判断要找的东西是否作为*值*存在于字典中，使用values方法，它会返回字典中所有值的一个列表，然后使用in操作符作出判断。

```
>>> vals = eng2sp.values()
>>> 'uno' in vals
True
```

列表与字典的in操作符算法上是有差别的。在列表中，Python采用线性搜索策略，搜索时间与列表长度成正比。在字典中，Python采用了一种很有效的*哈希表*算法。这种算法不管字典里面有多少数据项，时间花费上几乎没有什么差别。此处不详述哈希表的原理，更多相关知识请访问<http://wikipedia.org/wiki/Hash_table>。

**习题9.1** 编写一个程序，读取words.txt文件中的单词，将它们作为键存储在字典中。此时不需要关心键对应的值。然后，使用in操作符快速判断某一字符串是否存在于字典中。


## 9.1 字典作为计数器

假设给定一个字符串，你想要知道其中每个字符出现的次数，以下几种方式可供选择：

1. 创建26个变量，分别记录字母表中每个字母出现的次数。然后，遍历字符串，每遇到一个字母，相应的计数变量加1，或许可以采用一个链式条件来实现。

2. 创建一个包含26个元素的列表。然后，使用Python内置的ord函数，将每一个字符转换成数字，把这个数字作为索引存入列表，再增加相应的计数变量。

3. 创建一个以字符为键，其出现次数作为值的字典。首次遇到一个字符，将其作为一个数据项添加到字典里。之后，遇到字典里已存在的数据项，就对其值加1。

以上方法都实现了相同的计算结果，但每种方法的计算策略各不相同。

执行计算的一种方式称为实现（implementation）。有些实现方式优于其他实现方式。举例来说，字典实现的一个优点在于，我们不必事先知道字符串中出现了哪些字母，只需为确实会出现的字母分配空间就好了。

下面是一段示例代码：

```
word = 'brontosaurus'
d = dict()
for c in word:
    if c not in d:
        d[c] = 1
    else:
        d[c] = d[c] + 1
print d
```

我们可以有效地计算出直方图。直方图是关于计数器（频次）的统计术语。

用for循环遍历字符串。每次循环时，如果字符c不在字典中，我们就创建一个字典项，以c为键，初始值1（表示这个字母至此出现了一次）。如果c已经存在于字典中，只需将对应值d[c]加1即可。

程序运行结果如下：

```
{'a': 1, 'b': 1, 'o': 2, 'n': 1, 's': 2, 'r': 2, 'u': 2, 't': 1}
```

直方图表示字母“a”和“b”分别出现了一次，“c”出现了两次，后面的键值对含义类似，不再详述。

字典有一个get方法，该方法拥有一个键和一个默认值。如果键出现在字典中，那么它会返回此键对应的值。如果键不在字典中，返回事先给定的默认值。示例程序如下：

```
>>> counts = { 'chuck' : 1 , 'annie' : 42, 'jan': 100}
>>> print counts.get('jan', 0)
100
>>> print counts.get('tim', 0)
0
```

我们使用get方法可以把直方图循环写得更简洁。因为get方法自动处理了键不在字典中的情况。这样的话，代码的4条语句可缩减至1条，还可以去掉if语句。

```
word = 'brontosaurus'
d = dict()
for c in word:
    d[c] = d.get(c,0) + 1
print d
```

使用get方法简化计数循环的做法已经成为Python中的一种普遍做法，本书后续章节会多次用到。因此，你仔细体会一下，if条件和in操作的循环与get方法的循环之间的区别。两者可以实现同样的目的，但是后者更加简洁。

## 9.2 字典与文件

字典的常见用法之一是对书面文字的文本文件进行词频统计。首先，我们从《罗密欧与朱丽叶》中抽取一段简单的文本，参照 <http://shakespeare.mit.edu/Tragedy/romeoandjuliet/romeo_juliet.2.2.html>。

由于刚开始接触，我们使用不包含标点符号的简化后的短文本。随后会介绍包含标点符号的文本处理方法。

```
But soft what light through yonder window breaks
It is the east and Juliet is the sun
Arise fair sun and kill the envious moon
Who is already sick and pale with grief
```

编写一个Python程序，读取上述文件的每一行，并将每一行分解为由单词组成的一个列表。然后，遍历每个单词，使用字典来统计每个单词的出现次数。

你可以看到，这段程序包含两个for循环，外部循环用来读取文件中每一行，内部循环迭代出该行的每一个单词。这就是所谓的嵌套循环，其中一个为外部循环，另一个为内部循环。

由于外部循环每迭代一次，内部循环都会执行它全部的迭代。基于这一点，我们认为内部循环比外部循环迭代地“更快”。

两个嵌套循环的组合确保了输入文件的每一行的每个单词都会被统计到。

```
fname = raw_input('Enter the file name: ')
try:
    fhand = open(fname)
except:
    print 'File cannot be opened:', fname
    exit()

counts = dict()
for line in fhand:
    words = line.split()
    for word in words:
        if word not in counts:
            counts[word] = 1
        else:
            counts[word] += 1

print counts
```

运行程序，生成一个包含全部计数的原始文件，输出没有排序的哈希序列。romeo.txt文件可在<http://www.py4inf.com/code/romeo.txt>获得。

```
python count1.py
Enter the file name: romeo.txt
{'and': 3, 'envious': 1, 'already': 1, 'fair': 1,
'is': 3, 'through': 1, 'pale': 1, 'yonder': 1,
'what': 1, 'sun': 2, 'Who': 1, 'But': 1, 'moon': 1,
'window': 1, 'sick': 1, 'east': 1, 'breaks': 1,
'grief': 1, 'with': 1, 'light': 1, 'It': 1, 'Arise': 1,
'kill': 1, 'the': 3, 'soft': 1, 'Juliet': 1}
```

通过字典找到出现最多的单词及其次数，并不是那么方便。因此，我们需要增加一些Python代码，以便输出更有用的信息。

## 9.3 循环与字典

如果在for语句中把字典看做一个待循环的序列，它会遍历字典中的每一个键。下面的循环输出每一个键及其对应的值：

```
counts = { 'chuck' : 1 , 'annie' : 42, 'jan': 100}
for key in counts:
    print key, counts[key]
```
程序运行结果如下：

```
jan 100
chuck 1
annie 42
```

再次强调，字典中的键是没有固定顺序的。

我们可以将这种模式应用到之前介绍的循环的各种常用方法。例如，如果想要找到字典中所有值大于10的记录，程序代码如下：

```
counts = { 'chuck' : 1 , 'annie' : 42, 'jan': 100}
for key in counts:
    if counts[key] > 10 :
        print key, counts[key]
```

for循环是通过字典的键进行迭代。因此，我们必须使用索引操作符来检索相对应的值。程序运行的输出结果如下：

```
jan 100
annie 42
```

我们只看到了值大于10的记录。

如果按照字母顺序输出字典中的键，首先使用字典对象的keys方法，将所有的键放入一个列表。然后，对这个列表进行排序，对排序后的列表进行迭代。依照字母顺序，通过每个键输出对应的键值对：

```
counts = { 'chuck' : 1 , 'annie' : 42, 'jan': 100}
lst = counts.keys()
print lst
lst.sort()
for key in lst:
    print key, counts[key]
```

程序运行的输出结果如下：

```
['jan', 'chuck', 'annie']
annie 42
chuck 1
jan 100
```

首先，通过keys方法得到了包含键的未排序的列表。然后，通过for循环得到有序的键值对。

## 9.4 高级文本解析

上面示例程序用到的romeo.txt文件是我们手动删除所有标点符号之后得到的尽可能简化的文本。实际的文本会包含大量标点符号，如下所示：

```
But, soft! what light through yonder window breaks?
It is the east, and Juliet is the sun.
Arise, fair sun, and kill the envious moon,
Who is already sick and pale with grief,
```

Python的split函数可以识别空格，把词汇看作是由空格分隔开来的词单元，所以，“soft”和“soft!”会被视为不同的词汇，分别为它们创建一个字典项。

由于文本中还存在大写，“who”和“Who”也是不同的词，分别进行统计。

通过字符串的lower、punctuation与translate方法可以解决上述问题。其中translate是最精细的方法。以下是translate的说明文档：

string.translate(s, table[, deletechars])

*首先，从s中删除deletechars参数（如果存在的话）指定的所有字符，然后使用table参数来翻译字符。table是一个256个字符长的字符串，用来翻译每个字符值，并按照序数进行索引。如果table是None值，只会执行字符删除步骤。*

这里不指定table参数，用deletechars参数来删除所有标点符号。Python甚至可以告诉我们，标点符号包括哪些字符：

```
>>> import string
>>> string.punctuation
'!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'
```

我们对程序做出如下修改：

```
import string                                          # New Code

fname = raw_input('Enter the file name: ')
try:
    fhand = open(fname)
except:
    print 'File cannot be opened:', fname
    exit()

counts = dict()
for line in fhand:
    line = line.translate(None, string.punctuation)    # New Code
    line = line.lower()                                # New Code
    words = line.split()
    for word in words:
        if word not in counts:
            counts[word] = 1
        else:
            counts[word] += 1

print counts
```

我们使用translate方法删除了所有的标点符号，并将每一行中的字母转换为小写。程序主体并未发生改变。请注意，Python2.5及早期版本中，translate方法不接受None值作为第一个参数，因此使用下面的代码来调用该方法：

```
print a.translate(string.maketrans(' ',' '), string.punctuation
```

懂得一些“Python的艺术”和“像Python一样思考”，不难发现Python为许多常见数据分析问题内置了解决方案。随着学习的深入，通过大量的示例代码和技术文档的阅读，你会知道去哪里寻找别人是否用Python已经解决了此类问题，从而减轻一些你的工作。

以程序运行的部分输出结果如下：

```
Enter the file name: romeo-full.txt
{'swearst': 1, 'all': 6, 'afeard': 1, 'leave': 2, 'these': 2,
'kinsmen': 2, 'what': 11, 'thinkst': 1, 'love': 24, 'cloak': 1,
a': 24, 'orchard': 2, 'light': 5, 'lovers': 2, 'romeo': 40,
'maiden': 1, 'whiteupturned': 1, 'juliet': 32, 'gentleman': 1,
'it': 22, 'leans': 1, 'canst': 1, 'having': 1, ...}
```

查看这些输出仍然很费事，让Python来帮助我们找到具体要找到的信息。要做到这一点，我们需要学习Python的元组。在学习元组时会继续使用这个例子。

## 9.5 调试

当需要处理更大的数据集时，手工输出与检查数据显得不那么现实了。以下是调试大数据集的一些建议：

**减少输入：**尽可能地减少数据量的大小。例如，程序读取一个文本文件，仅选择前十行，或者选择最小的示例。你可以编辑文件本身，或者修改程序，让它只读取文件的前n行。后一种方式更好一些。

当出现错误时，通过不断地减小n值来确定错误的位置。当找到错误并纠正后，再不断增大n值。

**检查摘要与类型：**不要对整个数据集进行输出和检查，可以考虑先输出数据的摘要。例如，字典的数据项个数，或数字列表的总数。

运行错误的一个常见原因是数据类型不正确。调试此类错误，通常输出这个值的类型就可以解决了。

**编写自检程序：**有时通过编写代码来自动检查错误。例如，需要计算数字列表的平均数，你可以检查这个结果是不是处于列表的最大值与最小值之间。这种检查称为“逻辑检查”，它会检测出哪些结果是完全不符合逻辑的。

另一种检查是比较两种不同计算的结果，检查它们是否一致。这种检查称为“一致性检查”。

**工整化输出结果：**对调试的输出结果进行格式化，这有助于发现错误。

再次强调，在程序架构上花些心思能有效减少调试的时间花费。

## 9.6 术语

**字典：**一组键及其对应值的映射。

**哈希表：**Python字典的实现算法。

**哈希函数：**使用哈希表来计算字典中键的位置的函数。

**直方图：**一组计数器。

**实现：**执行计算的一种方法。

**数据项：**键值对的另一种说法。

**键：**字典中键值对的第一部分对象。

**键值对：**从键到值的映射关系表达。

**查找：**字典的一种操作，根据键找到对应的值。

**嵌套循环：**一个循环内部包括另一个或多个循环。外部循环每执行一次，内部循环会全部执行一遍。

**值：**字典中键值对的第二部分对象。这里所说的值比之前提到的“取值”更加专指。

## 9.7 练习

**习题9.2** 编写一个程序，按照接收的星期时间对邮件进行分类。首先，找出以From开头的文本行，然后，取出符合条件的每一行中的第三个单词，使用一个计数器统计一周中每一天邮件被接收的次数。在程序的末尾，输出字典的内容（不要求次序）。

```
Sample Line:
From stephen.marquard@uct.ac.za Sat Jan  5 09:14:16 2008

Sample Execution:
python dow.py
Enter a file name: mbox-short.txt
{'Fri': 20, 'Thu': 6, 'Sat': 1}
```

**习题9.3** 编写一个程序，读取邮件日志，使用字典来创建一个直方图，统计每个邮箱发出的邮件数量，然后输出字典。

```
Enter file name: mbox-short.txt
{'gopal.ramasammycook@gmail.com': 1, 'louis@media.berkeley.edu': 3,
'cwen@iupui.edu': 5, 'antranig@caret.cam.ac.uk': 1,
'rjlowe@iupui.edu': 2, 'gsilver@umich.edu': 3,
'david.horwitz@uct.ac.za': 4, 'wagnermr@iupui.edu': 1,
'zqian@umich.edu': 4, 'stephen.marquard@uct.ac.za': 2,
'ray@media.berkeley.edu': 1}
```

**习题9.4** 在上述程序中添加代码，从文件中找出谁的邮件最多。
读取完所有数据并且建立字典之后，使用最大循环（详见5.7.2）对字典进行遍历，找出谁的邮件最多，并输出他的邮件总数。

```
Enter a file name: mbox-short.txt
cwen@iupui.edu 5

Enter a file name: mbox.txt
zqian@umich.edu 195
```

**习题9.5** 编写一个程序，记录邮件发送者的域名，而不是邮件发送者的邮件地址。在程序的末尾，输出字典的内容。

```
python schoolcount.py
Enter a file name: mbox-short.txt
{'media.berkeley.edu': 4, 'uct.ac.za': 6, 'umich.edu': 7,
'gmail.com': 1, 'caret.cam.ac.uk': 1, 'iupui.edu': 8}
```
