# 记录GLR Parser的重构，GacUI的起源，论打补丁和技术债的恶性循环，以及其他软件工程上的感悟的流水账

项目的起源还得从许久以前说起。回顾这一路走来的过程，先是中学的时候为了开发游戏制作第一个游戏引擎随后对编译原理产生了兴趣；接着就是在大学时期先后实现了垃圾收集器、C语言子集一路做到汇编器、和一个Haskell子集的类型推导；毕业后又借机把type class加到了C语言上面。

这个过程没有光做编译器，还是图给所有的这些东西做编辑器，包括上下文有关着色和自动完成等一系列IDE的标准功能都是用C#做的。编译器是C++写的但是编辑器是C#写的，于是我从parser到类型推导都分别用C++和C#各做了一遍，这怎么想都是不对的。

![](Turtle_Sample_01.jpg)

不过这其实才是现实世界里大家的普遍原则。想想当时地球上爆火的几款代码编辑器，分别有正在（已经）用C#和XAML重写的Visual Studio、用Jvav写的Eclipse、用Jvav写的一系列不同语言的IDE、还有马上就要诞生的TypeScript写的vscode。它们哪个不是一个语言的前端做了好几遍？要么就是编译器和编辑器是不同地方的人做的，要么语言本身就没有自举，或者自举了但完全没考虑过编辑器的需要。比如微软明明用C++写了编译器和VS，但是VS用的却是Edison Design Group实现的C++前端；比如原本用C++写的C#编译器后面用C#重写了（Roslyn）；比如原本用JS写的TS然后换TS又写了一遍然后换go又写了一遍；似乎分开做才是业界常态。

但我自己做又没有类似的问题，于是怀揣着编译器和编辑器共享一套编译器前端的想法，就有了[vczh-libraries/GacUI](https://github.com/vczh-libraries/GacUI)，以及为了实现语法分析和给语义着色和自动完成打基础的VlppParser和VlppParser2（做了两次）。故事便从[VlppParser](https://github.com/vczh-libraries/VlppParser)和[VlppParser2](https://github.com/vczh-libraries/VlppParser2)这里发生。此时正是大学刚毕业的时候，所以VlppParser的设计还能看见当初在大学的时候诞生的很多想法。

## 一个JSON parser

Parser是怎么运行起来的呢？阅读[JSON的官网](https://www.json.org/json-en.html)我们可以迅速写出它的[词法和语法](https://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_form)：

```
TRUE_VALUE:true
FALSE_VALUE:false
NULL_VALUE:null
OBJOPEN:\{
OBJCLOSE:\}
ARROPEN:\[
ARRCLOSE:\]
COMMA:,
COLON::
NUMBER:[\-]?\d+(.\d+)?([eE][+\-]?\d+)?
STRING:"([^\\"]|\\[^u]|\\u\d{4})*"
discard SPACE:\s+
```

[VlppRegex](https://github.com/vczh-libraries/VlppRegex)会处理这些正则表达式，然后把它们合并成一个[DFA](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)。合并DFA的好处就是可以让你一个一个字符跑到token结束的时候正好知道他命中了上面的哪一行。

如果一个正则表达式已经封装好了，就很难做出这样的功能,所以当初就自己做了一个。以前用C#还是TypeScript写类似的东西，就真的一个一个正则表达式去试。正则表达式会有很多超出DFA表达能力的功能，所以这里面其实有两套实现，如果发现能用DFA那就走DFA，不能的就走另一套。但是词法分析器是不允许使用那些高级功能的，因此可以在每个正则表达式生成DFA之后继续往下做。

这个时候JSON字符串已经变成一个一个的token，而且每个token还标记出了它的名字，那么就可以跑语法了。语法里面的`@parser`标记是为了辅助生成C++代码用的，标记了`@parser`的语法都会生成几个helper，把背后的东西都打包在一起。

```
JLiteral
	::= STRING
	::= NUMBER
	::= "true"
	::= "false"
	::= "null"
	;

JField
	::= STRING ":" JValue
	;

JObject
	::= "{" {JField ; ","} "}"
	;

JArray
	::= "[" {JValue ; ","} "]"
	;

JValue
	::= JLiteral
	::= JObject
	::= JArray
	;

@parser JRoot
	::= JObject
	::= JArray
	;
```

以`JObject`为例，我们可以直接生成他的[epsilon-NFA](https://en.wikipedia.org/wiki/Nondeterministic_finite_automaton)：

![](Images/JSON_ENFA.png)

然后还是当他正则表达式那样做，把rule input和token input都先当成不一样的原子输入看，生成它的DFA。当然这并不是真的DFA，他是一个[PDA](https://en.wikipedia.org/wiki/Pushdown_automaton)，因为rule之间或者rule/token之间是会存在包含关系或者共享前缀的，但是先不管，后面自有办法：

![](Images/JSON_PDA.png)

后面还有一系列复杂的处理，但总之我们还是得到了一个能跑的东西了。于是给一个JSON字符串，现在我们可以验证他是不是一个合法的JSON了，从`JROOT`开始运行，如果把所有token都吃掉的同时正好跑到状态机的最后一个状态，就说明成功了。费了这么大劲就搞出这么个东西？当然不是，我们的目标是生成JSON的语法树。

### 语法的副作用

既然要生成语法树，那就得继续往语法上加信息。经过短暂地思考，就可以知道一行语法大概需要做的事情有：
- 构造语法树的一个对象，比如`JObject`
- 把别人的语法树拿过来，比如`JValue ::= JObject`，这样`JValue`可能会生成好几个不同的东西
- 不仅要把语法树拿过来，还要对他继续做修改，这个在JSON里暂时还不存在，后面会提到

此时语法的表达就不是特别的简洁了，慢慢罗嗦起来，但是至少为了[写上最必要的信息](https://github.com/vczh-libraries/VlppParser2/blob/master/Source/Json/Syntax/Syntax.txt)，我们还是得到了

```
...

JObject
	::= "{" {JField:fields ; ","} "}" as Object
	;

...

@parser JRoot
	::= !JObject
	::= !JArray
	;
```

为了解释加进去的信息的意思，我们还需要定义[JSON语法树的结构](https://github.com/vczh-libraries/VlppParser2/blob/master/Source/Json/Syntax/Ast.txt)：

```
class Node
{
}

...

class Object : Node
{
    var fields : ObjectField[];
}
```

最后只要把这些信息都加入到上面生成的PDA就可以了。一个很自然的想法就是，既然parse一个JSON无非就是从`JROOT`的状态机开始跑到完，那只要一边跑一边构造语法树就行了。从一个状态到另一个状态转移（transition）的时候，我们需要看下一个token是什么，从这个状态出发的那么多transition里选中我们想要的，顺便就可以做点事。

“做点事”到底做什么事呢？现在我们已经从上面的语法树的定义[生成了它的C++代码](https://github.com/vczh-libraries/VlppParser2/blob/master/Source/Json/Generated/JsonAst.h)，那就先来看看，假如我们需要生成`{"a": "b"}`的语法树，我们需要做什么：

```C++
auto obj = Ptr(new JsonObject);
{
  auto field = Ptr(new JsonField);
  field->name.value = L"a";
  {
    auto value = Ptr(new JsonString);
    value->content.value = L"b";
    field->value = value;
  }
  obj->fields.Add(field);
}
```

pattern一下就看出来了。一个很自然的想法就是，我们可以设计一个指令集，就专门用来生成语法树。每一个transition带了一些指令，而运行指令就等于运行一些特定的函数，而这些函数就可以从语法树的定义里生成出来。那首先我们要补齐读token的动作：

```C++
auto obj = Ptr(new JsonObject);
{
  NextToken();                         // {这个token是不要的，但是又必须把他读掉
  auto field = Ptr(new JsonField);
  {
    auto token = NextToken();          // "a"
    field->name = token;
  }
  NextToken();                         // :
  {
    auto str = Ptr(new JsonString);
    {
      auto token = NextToken();        // "b"
      str->content = token;
    }
    field->value = str;
  }
  obj->fields.Add(field);
  NextToken();                         // "}"
}
```

然后令他变得抽象：

```
BeginObject(Object)
  Token                  // {
  Discard
  BeginObject(Field)
    Token                // "a"
    Field(name)
    Token                // :
    Discard
    BeginObject(String)
      Token              // "b"
      Field(content)
    EndObject
    Field(value)
  EndObject
  Field(fields)
  Token                  // }
  Discard
EndObject
```

我们不妨把它称之为“BeginObject指令集”。

### 实现BeginObject指令集

<!--
- Json语法引出如何让parser做完语法分析就自动产生优雅的AST（语法的副作用，BeginObject指令）
- BeginObject指令的双堆栈实现
- 四则运算语法引出右递归、左递归的区别，以及语法的副作用如何产生AST
- if-else 歧义的解决方式
  - 自己展开成复杂的语法
  - 设定优先级但是需要引入GLR parser
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
-->
