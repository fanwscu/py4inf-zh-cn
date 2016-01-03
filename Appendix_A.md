# 附录A Windows平台上的Python编程

本附录介绍在Windows上运行Python的一系列步骤。你可以采用许多种方法做到这一点，但这是一种让事情保持简单的方法。

首先，你需要安装一个程序员专用的编辑器。不要使用记事本或微软的Word字处理软件来编辑Python程序。程序必须是“平面的”文本文件，因此你需要的是一个擅长编辑文本文件的编辑器。

我们在Windows平台上推荐NotePad++，从以下网址下载与安装：
<http://sourceforge.net/projects/notepad-plus/files/>

从www.python.org网站下载Python 2的最新版本。
<http://www.python.org/download/releases/2.7.5/>

安装好Python之后，计算机上会出现类似的新文件夹C:\Python27。

新建一个Python程序，从开始菜单运行NotePad++，采用“.py”后缀保存文件。这个练习中，在桌面创建一个py4inf文件夹。文件夹名越短越好，不要在文件夹和文件名中留空格。

编写第一个Python程序如下：
```
print 'Hello Chuck'
```
这里你能做的就是修改成你自己的名字。将程序文件保存在Desktop\py4inf\prog1.py。

在命令行运行程序，Windows不同版本的操作有些许差别。
* Windows Vista与Windows-7: 按下“开始”按钮，在命令搜索窗口输入“command”一词，然后按回车键。
* Windows-XP: 按下“开始”按钮，然后点“运行”，在对话框输入cmd，然后点“OK”。

此时出现一个文本窗口，并提示当前所在的文件夹。
* Windows Vista与Windows-7的文件夹位置是：C:\Users\csev
* Windows XP的文件夹位置是：C:\Documents and Settings\csev

这就是你的“主目录”。现在我们需要进入刚才保存Python程序的文件夹，使用以下命令：

```
C:\Users\csev\> cd Desktop
C:\Users\csev\Desktop> cd py4inf
```

然后输入

```
C:\Users\csev\Desktop\py4inf> dir
```

列出当前文件下的所有文件。当输入dir命令后，你应该看到prog1.py文件了。执行这个程序，只需在命令行输入文件名，然后按回车键。

```
C:\Users\csev\Desktop\py4inf> prog1.py
Hello Chuck
C:\Users\csev\Desktop\py4inf>
```

你可以在NotePad++里编写文件，保存它。然后，切换到命令行窗口，在命令行提示符处，输入文件名再次执行程序。

如果你被命令行窗口搞混了，没关系，关闭它再打开一个即可。

提示：在命令行使用“向上箭头”可以回滚和执行之前输入的命令。

另外，建议在NotePad++的偏好设置中将Tab字符设置为4个空格。这将节省大量由于缩进错误导致的时间花费。

Python程序编辑与执行的更多信息，请参见<http://www.py4inf.com>。
