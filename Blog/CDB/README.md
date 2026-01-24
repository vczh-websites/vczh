# 微软命令行调试器CDB光速入门（LLM适用版）

这里假设读者都是用Visual Studio和msbuild的用户，为了安装这个调试器，你需要打开VS的安装程序，切换到`Individual Components`，勾上`Windows Driver Kit`，然后把他装上去。
接着就是到[Download the Windows Driver Kit (WDK)]([Download the Windows Driver Kit (WDK)](https://learn.microsoft.com/en-us/windows-hardware/drivers/download-the-wdk))把WDK再装一遍。
VS自带的只是WDK的库，第二个就是WDK的所有UI工具了。
装完后就可以在`C:\Program Files (x86)\Windows Kits\10\Debuggers\x64\cdb.exe`找到，命令行就可以用起来了。

## 启动CDB

我们用CDB还是想要传统的按代码行的方式来调试。默认情况下VS会帮你把exe和pdb都生成在一起，那你什么都不需要做就可以加载符号。一般来说我们可以：
```
cdb -c ".lines; l+t; l+s" -o "你的exe 你的参数"
```
启动CDB。这个时候程序时处于暂停状态的，你可以趁这个机会打断点，然后按`g`运行。如果你想直接运行，可以在-c的命令最后加上`; g`。

## 下断点

- `lm`：列出所有加载的模块。如果你的程序很简单就只有一个`UnitTest.exe`，那你就不用列了，模块名就是`UnitTest`。
- `bl`：列出所有断点
- `be 数字`：enable断点
- `bd 数字`：disable断点
- `bc 数字`：删掉断点

下断点用的是`bp`命令，他需要你给出指令的地址。但是我们想直接按照行号来也可以：
```
bp `file.cpp:line`
bp `UnitTest!file.cpp:line`
```

## 单步执行

有了断点之后就可以`g`（也就是VS的F5）然后停下来，这个时候：
- `p`：运行到下一行，也就是VS的F10
- `t`：进函数里面，也就是VS的F11
- `pt`：退出函数

## 查看堆栈

不管是程序炸了还是命中断点，这个时候就要开始看变凉了。你可以用`kn`查看完整的callstack，如果太长的话你也可以用`kn 10`只显示10行。callstack的左边是从0开始排列的数字，看看你想进哪个函数里面看，然后`.frame 数字`。

## 查看变量

CDB牛逼的地方是他可以和VS用相同的[.natvis](https://github.com/vczh-libraries/Vlpp/blob/master/Release/vlpp.natvis)文件。你可以`.nvload`它。

首先用`dv`列出当前函数的所有变量，接着就可以用`dx`去看。调试一个C++程序的话，`dx`甚至可以用C语言的语法来看一个表达式的值。如果是.net程序或者用`dx`查看内置的变量，你甚至可以用C#的linq来进行快速筛选。

## 退出

最后用`q`就可以退出了，顺便把被调试的程序杀了。

## 实战演示

## 让LLM也能用CDB