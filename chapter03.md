# 第3章 条件执行

## 3.1 布尔表达式

布尔表达式是具有真或假状态的一种表达式。==运算符用来比较两个运算对象，若两者相等返回True，否则返回False：

```
>>> 5 == 5
True
>>> 5 == 6
False
```

True和False是布尔类型的两个取值，它们不是字符串：

```
>>> type(True)
<type 'bool'>
>>> type(False)
<type 'bool'>
```

==运算符是比较运算符中的一个，其他的比较运算符如下：

```
x != y               # x is not equal to y
x > y                # x is greater than y
x < y                # x is less than y
x >= y               # x is greater than or equal to y
x <= y               # x is less than or equal to y
x is y               # x is the same as y
x is not y           # x is not the same as y
```

虽然你可能很熟悉这些运算符，但要注意这些Python符号并不等同于数学符号。一个常见的错误是用单等号（=），而没有用双等号（==）。请记住，=是赋值运算符，==是比较运算符。不存在=<或=>这样的运算符。

## 3.2 逻辑运算符

逻辑运算符包括and（与）、or（或）与not（非）三种。这些运算符的语义与它们的英文含义相似。例如，

```
x > 0 and x < 10
```

若x大于0且小于10，则为真。

若`n%2 == 0 or n%3 == 0`其中有一个条件为真，即这个数字能被2或3整除，则为真。

not运算符是表示否定的布尔表达式。若`x > y`为假，即x小于或者等于y，那么`not (x > y)` 为真。

严格讲，逻辑运算符的运算对象应该是布尔表达式，但在Python中并不是很严格。任何非零数字都可看作是“真”。

```
>>> 17 and True
True
```

这种灵活性的存在是有用的，但也会产生一些微妙的困惑。除非你清楚自己在做什么，否则不要乱用。

## 3.3 条件执行

为了编写出有用的程序，几乎总是需要根据条件，修改程序相应的行为。条件语句赋予我们这种能力。最简单的条件形式是if语句：

```
if x > 0 :
    print 'x is positive'
```

if语句后的布尔表达式称为条件。if语句的末尾用冒号 (:)，if语句之后的语句要缩进。

![enter image description here](http://www.pythonlearn.com/html-270/book005.png)

若逻辑条件为真，那么缩进的语句得以执行。若逻辑条件为假，那么缩进的语句就会被跳过。

if语句与for循环[^1]的函数定义具有相同的结构。语句由一个以冒号（:）结束的标题行和随后的一个代码缩进区域构成。由于包含多行，这样的语句被称为复合语句。

语句块内没有语句的数量限制，但至少要有一行。有时候，空白的语句块也是有用的，通常是为还未写的代码预留空间。在这种情况下，使用pass语句，表示什么也不做。

```
if x < 0 :
    pass          # need to handle negative values!
```

如果在Python解释器里输入一个if语句，提示符会从三个“&gt;”变成三个“.”，表明正处在一个语句块内，如下所示：

```
>>> x = 3
>>> if x < 10:
...    print 'Small'
...
Small
>>>
```

## 3.4 分支执行

if语句的第二种形式是选择执行。语句中存在两种可能，条件决定了执行哪一种。语法如下：

```
if x%2 == 0 :
    print 'x is even'
else :
    print 'x is odd'
```

若x除以2的余数为0，则x是偶数，程序就执行第一个print语句，显示“x is even”。若余数不为0，则else之后的第二个print语句得以执行。

![enter image description here](http://www.pythonlearn.com/html-270/book006.png)

由于条件必须为真或假，所以两条备选语句中总有一条会被执行。这些备选语句称为执行流程中的分支。

## 3.5 链式条件

有时候存在两种以上的可能，那么就需要两个以上的分支。链式条件可以处理这种情况。

elif是“else if”的缩写，表示又有一个分支将被执行。

![enter image description here](http://www.pythonlearn.com/html-270/book007.png)

elif语句没有数量限制。如果还有一个else子句，那它必须是最后一个，但是else子句不是必须的。

```
if choice == 'a':
    print 'Bad guess'
elif choice == 'b':
    print 'Good guess'
elif choice == 'c':
    print 'Close, but not correct'
```

依次检查每个条件。如果第一个为假，就检查第二个，以此检查下去。如果其中一个条件为真，则执行相应的分支，至此语句结束。即使不止一个条件为真，也只执行第一个为真的分支。

## 3.6 嵌套条件

一个条件语句也可以嵌套到另一个条件语句中。我们可以写出三分条件的例子：

```
if x == y:
    print 'x and y are equal'
else:
    if x < y:
        print 'x is less than y'
    else:
        print 'x is greater than y'
```

外面的条件语句包含了两个分支。第一条分支包含了一个简单语句。第二条分支包含了另一个由两个分支组成的if语句。这两个分支都是简单语句，虽然这两个分支包含条件语句，但它们都是简单语句。

![enter image description here](http://www.pythonlearn.com/html-270/book008.png)

虽然缩进让语句结构变得清晰，但是嵌套条件还是很难快速地阅读。一般情况下，还是尽量避免使用。

逻辑运算符通常提供一种方法来简化嵌套条件语句。例如，使用单个条件改写以下代码：

```
if 0 < x:
    if x < 10:
        print 'x is a positive single-digit number.'
```

当两个条件都满足时，print语句才会执行。使用and运算符也能达到相同的效果：

```
if 0 < x and x < 10:
    print 'x is a positive single-digit number.'
```

## 3.7 try与except异常捕获

之前我们看到过一段代码，使用raw_input和int函数读取和解析用户输入的整数。由此带来的潜在危险是：

```
>>> speed = raw_input(prompt)
What...is the airspeed velocity of an unladen swallow?
What do you mean, an African or a European swallow?
>>> int(speed)
ValueError: invalid literal for int()
>>>
```

在Python解释器中执行这些语句，提示 “值错误”，然后会新起一行，等待下一条语句的输入。

如果把这段代码放在Python脚本文件中，当错误发生时，脚本的执行会立即停止在这一行，并返回错误消息，之后的语句不会被执行。

```
inp = raw_input('Enter Fahrenheit Temperature:')
fahr = float(inp)
cel = (fahr - 32.0) * 5.0 / 9.0
print cel
```

如果我们执行这段代码，输入一个无效的值，它会停止执行，并返回一个不友好的错误信息：

```
python fahren.py
Enter Fahrenheit Temperature:72
22.2222222222

python fahren.py
Enter Fahrenheit Temperature:fred
Traceback (most recent call last):
  File "fahren.py", line 2, in <module>
    fahr = float(inp)
ValueError: invalid literal for float(): fred
```

Python内置了“try/except”条件执行结构，用来解决意料之中和意料之外的错误。你知道程序可能存在问题，希望在错误发生时增加一些语句，这时try 和 except就派上用场了。如果没有出错，这些额外的语句（except语句块）就会被忽略掉。

你可以把Python 的try 和 except功能看作是程序的保险单。

温度转换程序重新编写如下：

```
inp = raw_input('Enter Fahrenheit Temperature:')
try:
    fahr = float(inp)
    cel = (fahr - 32.0) * 5.0 / 9.0
    print cel
except:
    print 'Please enter a number'
```

Python首先执行try语句块。如果一切顺利，它就会跳过except语句块。如果在try语句块里发生意外，Python就会跳出try语句块，执行except语句块。

```
python fahren2.py
Enter Fahrenheit Temperature:72
22.2222222222

python fahren2.py
Enter Fahrenheit Temperature:fred
Please enter a number
```

用try语句处理异常的行为称为异常捕获。这个示例中except子句打印了一条错误提示信息。一般来说，捕捉到异常，就是给你一个机会去解决它，或者是再试一次，至少程序能正常结束。

## 3.8 逻辑表达式的短路评估

当Python处理诸如`x > = 2 and (x / y)> 2`这样的逻辑表达式时，从左至右进行判断。根据and的含义，如果x小于2，则表达式`x>＝2`为假，那么整个表达式即为假，不管后面的`（x / y）> 2`的判断是真或假。

当Python发现逻辑表达式剩余部分的判断没有意义了，它就停止对剩余部分的判断。在已知逻辑表达式整体结果的情况下停止判断，这称为短路评估。

这看起来是一个细节，短路行为带来了一种灵活的处理方式，称为**守护者模式**。如下是Python解释器中的一段代码：

```
>>> x = 6
>>> y = 2
>>> x >= 2 and (x/y) > 2
True
>>> x = 1
>>> y = 0
>>> x >= 2 and (x/y) > 2
False
>>> x = 6
>>> y = 0
>>> x >= 2 and (x/y) > 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero
>>>
```

第三次计算出错了。由于y等于0，Python计算`（x/y）`时出现运行错误。但是，第二次计算没有出错。由于表达式第一部分`x > = 2`判断为假，所以根据短路规则，`(x / y)`根本没有被执行，所以没有报错。

我们可以在错误发生之前，策略性地放置一个守护评估，代码如下：

```
>>> x = 1
>>> y = 0
>>> x >= 2 and y != 0 and (x/y) > 2
False
>>> x = 6
>>> y = 0
>>> x >= 2 and y != 0 and (x/y) > 2
False
>>> x >= 2 and (x/y) > 2 and y != 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero
>>>
```

第一个逻辑表达式中`x > = 2`为假，所以在and之前判断就停止了。第二个逻辑表达式中`x > = 2`为真，但`y != 0`为假，所以`(x/y)`没有机会得到判断。

第三个逻辑表达式中`y != 0`位于`(x/y)`之后，所以这个表达式的判断失败了。

第二个表达式中`y != 0`作为一个守护，确保y不等于0时只执行(x/y)。

## 3.9 调试

当错误发生时，Python的追踪（Traceback）显示很多信息，但有可能消息过多，特别是栈中有很多栈帧的情况。其中最有用的部分是：

* 错误的类型是什么
* 错误发生的位置

语法错误通常很容易找到，但也存在一些陷阱。空格错误非常棘手，由于空格和制表符是不可见的，常常会被忽视。

```
>>> x = 5
>>>  y = 6
  File "<stdin>", line 1
    y = 6
    ^
SyntaxError: invalid syntax
```

这个例子中问题出在第二行缩进只用了一个空格。但是错误消息指向了y，这其实是一种误导。一般情况下，错误信息会显示问题在何处被发现，但是实际错误可能会在所显示的代码之前，有时候还会在前一行。

运行时错误也是如此。假设用分贝计算信噪比，公式是\\(SNR~{db} = 10 log~{10}(\frac{P~{signal}}{P~{noise}})\\)，Python代码如下:

```
import math
signal_power = 9
noise_power = 10
ratio = signal_power / noise_power
decibels = 10 * math.log10(ratio)
print decibels
```

但是当你执行这段代码，就会收到如下错误信息[^2]：

```
Traceback (most recent call last):
  File "snr.py", line 5, in ?
    decibels = 10 * math.log10(ratio)
OverflowError: math range error
```

错误信息显示第5行出错，但那一行没有错误。为了找到真正的错误，将ratio值打印出来可能会有用。错误原来是ratio为0，问题出现在第4行，原因是两数相除采用的是地板除法（只取整数）。解决方法是用浮点数表示信号功率和噪声功率。

一般情况下，错误信息会告诉你问题出现在哪，但这个出错位置并不准确。

## 3.10 术语

**主体：**复合语句中的一组语句。

**布尔表达式：**取值只有真（True）或假（False）其中之一的表达式。

**分支：**条件语句中可供选择的一组语句。

**链式条件：**带有多个可选分支的条件语句。

**比较运算符：**对运算对象进行比较的一种运算符，包括==、!=、 >、 <、 >=,和 <=。

**条件语句：**根据某些条件来控制程序执行顺序的语句。

**条件：**条件语句中的布尔表达式，用来决定执行哪一个分支。

**复合语句：**含有头部和主体的一组语句。代码头部以冒号（:）结尾。代码主体相对于代码头部进行缩进。

**守护模式：**通过额外的比较来构造逻辑表达式，充分利用短路行为优势。

**逻辑运算符：**组合布尔表达式的运算符，包括and、or和not。

**嵌套条件：**一个条件语句作为另一个条件语句的分支。

**追踪：**正在执行的函数列表，当出现异常时，在屏幕上显示出来。

**短路：**在判断逻辑表达式的过程中，如果Python已经知道了最终结果，则会停止，不会对剩余的表达式进行判断。

## 3.11 练习

**习题3.1** 重写薪水计算公式，如果员工工作时间超过40小时，按平常薪水的1.5倍支付。

```
Enter Hours: 45
Enter Rate: 10
Pay: 475.0
```

**习题3.2**  运用try和except重写支付程序，让程序可以正常处理非数字输入的情况，如果是非数字输入，打印消息并退出程序。以下是程序的两种执行结果：

```
Enter Hours: 20
Enter Rate: nine
Error, please enter numeric input

Enter Hours: forty
Error, please enter numeric input
```

**习题3.3** 编写一个程序，提示分数在0.0和1.0之间。如果分数超出这个范围则打印出错误。如果分数在0.0和1.0之间，使用下面的表格打印分数：

```
Score   Grade
>= 0.9     A
>= 0.8     B
>= 0.7     C
>= 0.6     D
< 0.6    F

Enter score: 0.95
A

Enter score: perfect
Bad score

Enter score: 10.0
Bad score

Enter score: 0.75
C

Enter score: 0.5
F
```

重复运行这个程序，测试不同的输入值。

[^1]: 第4章介绍函数，第5章介绍循环。

[^2]: Python3.0中，不再给出错误消息。即使是整数对象，除法运算符执行的也是浮点除法。
