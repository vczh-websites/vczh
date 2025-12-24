# 记录GLR Parser的重构，GacUI的起源，论打补丁和技术债的恶性循环，以及其他软件工程上的感悟的流水账

项目的起源还得从许久以前说起。
回顾这一路走来的过程，
先是中学的时候为了开发游戏制作第一个游戏引擎随后对编译原理产生了兴趣；
接着就是在大学时期先后实现了垃圾收集器、
C语言子集一路做到汇编器、
和一个Haskell子集的类型推导；
毕业后又借机把type class加到了C语言上面。

这个过程没有光做编译器，
还是图给所有的这些东西做编辑器，
包括上下文有关着色和自动完成等一系列IDE的标准功能
都是用C#做的。
编译器是C++写的但是编辑器是C#写的，
于是我从parser到类型推导都分别用C++和C#各做了一遍，
这怎么想都是不对的。

不过这其实才是现实世界里大家的普遍原则。
想想当时地球上爆火的几款代码编辑器，
分别有正在（已经）用C#和XAML重写的Visual Studio、
用Jvav写的Eclipse、
用Jvav写的一系列不同语言的IDE、
还有马上就要诞生的TypeScript写的vscode。
它们哪个不是一个语言的前端做了好几遍？
要么就是编译器和编辑器是不同地方的人做的，
要么语言本身就没有自举，
或者自举了但完全没考虑过编辑器的需要。
比如微软明明用C++写了编译器和VS，
但是VS用的却是Edison Design Group实现的C++前端；
比如原本用C++写的C#编译器后面用C#重写了（Roslyn）；
比如原本用JS写的TS然后换TS又写了一遍然后换go又写了一遍；
似乎分开做才是业界常态。

但我自己做又没有类似的问题，
于是怀揣着编译器和编辑器共享一套编译器前端的想法，
就有了vczh-libraries/GacUI，
以及为了实现语法分析和给语义着色和自动完成打基础的VlppParser和VlppParser2（做了两次）。
故事便从VlppParser和VlppParser2这里发生。
此时正是大学刚毕业的时候，
所以VlppParser的设计还能看见当初在大学的时候诞生的很多想法。

## 大纲

```mermaid
graph LR;
  S((Object))-- #quot;{#quot; -->X;
  X-->E;
  E(());
```

```mermaid
graph LR;
  E((()));
  S((Object))-- #quot;{#quot; -->1;
  1-- #quot;}#quot; -->E;
  1-- ObjectField -->2;
  2-- #quot;}#quot; -->E;
  2-- #quot;,#quot; -->3;
  3-- ObjectField -->2;
```

- Json语法引出如何让parser做完语法分析就自动产生优雅的AST（语法的副作用，BeginObject指令）
- BeginObject指令的双堆栈实现
- 四则运算语法引出右递归、左递归的区别，以及语法的副作用如何产生AST
- 自动错误恢复和局部歧义的做法（VlppParser一代的四个executor实现）
  - ParsingGeneralParser和他的子类们
  - 指令对这套实现的影响
  - 为什么自动错误恢复无法避免歧义的产生
  - 为什么无法处理更复杂的消除歧义的情况
- 如何应对天生就存在歧义的语法
  - Document第一代的C++ parser
  - VlppParser2重做C++语法分析
  - 新的序列化方案以及Lzw压缩算法的使用
- VlppParser2重新设计了歧义的实现，通过multiple passes取代上一代读一次就出结果的executor设计
- 左递归和reuse rule产生的DelayFieldAssignment/LriStore/LriFetch指令
- 为什么这个补丁对前缀合并产生了困难
- 此次重构如何解决这个问题
