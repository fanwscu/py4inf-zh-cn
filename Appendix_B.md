#附录B Mac平台上的Python编程

本附录介绍在Mac上运行Python的系列步骤。由于Mac操作系统已经包含了Python，所以只需学习如何编辑Python文件和在终端窗口执行Python程序。

在Python程序编辑与执行的众多方法中，这仅是我们找到的一种非常简单的方法。

首先，需要安装一个程序员用的文本编辑器。不要使用TextEdit或微软的Word字处理软件来编辑程序。程序必须是“平的”文本文件，因此你需要的是一个善于编辑文本文件的编辑器。

我们在Mac平台上推荐TextWrangler，从以下网址下载与安装：
<http://www.barebones.com/products/TextWrangler/>

新建一个Python程序，从应用文件夹中运行TextWrangler。

编写第一个Python程序：

```
print 'Hello Chuck'
```

这里你能做的就是修改成你自己的名字。将程序文件保存在桌面，命名为py4inf。文件夹名越短越好，不要在文件夹和文件名中留空格。

在终端窗口执行程序。最简单的方法是点击屏幕后上角的Spotlight图标（放大镜），输入“terminal”（也可输入中文“终端”）启动终端窗口。

这就是你的“主目录”。在终端窗口输入pwd命令查看当前目录。

```
67-194-80-15:~ csev$ pwd
/Users/csev
67-194-80-15:~ csev$ 
```

必须在包含Python程序的文件夹下执行该程序。我们使用cd命令移动到一个新的文件夹，然后用ls命令列出文件夹下的文件。

```
67-194-80-15:~ csev$ cd Desktop
67-194-80-15:Desktop csev$ cd py4inf
67-194-80-15:py4inf csev$ ls
prog1.py
67-194-80-15:py4inf csev$ 
```

在命令提示符处，只需输入python命令来执行程序，后面跟程序文件名，然后按回车键。

```
67-194-80-15:py4inf csev$ python prog1.py
Hello Chuck
67-194-80-15:py4inf csev$ 
```

在TextWrangler编辑Python程序文件，保存它，然后切换到命令行窗口，在命令行提示符处，键入文件名再次执行程序。

提示：在命令行使用“向上箭头”可以回滚和执行之前输入的命令。

另外，建议在TextWrangler的偏好设置中将Tab字符设置为4个空格。这将节省大量由于缩进错误导致的时间花费。

Python程序编辑与执行的更多信息，请参见<http://www.py4inf.com>。