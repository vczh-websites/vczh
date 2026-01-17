# Mermaid Flowcharts

## JSON_ENFA.png

```mermaid
graph LR
  S((Object))-.->1
  1((1))-- #quot;{#quot; -->2
  2((2))-.->3
  3((3))-- JFIELD -->4
  4((4))-.->5
  5((5))-- #quot;,#quot; --> 6
  6((6))-.->3
  4-.->7
  2-.->7
  7((7))-- #quot;}#quot; --> 8
  8((8))-.->E((( )))
```

## JSON_PDA.png

```mermaid
graph LR
  S((Object))-- #quot;{#quot; -->1
  1((1))-- #quot;}#quot; -->4
  1-- JField -->2
  2((2))-- #quot;}#quot; -->4
  2-- #quot;,#quot; -->3
  3((3))-- JField -->2
  4((4))-.->E((( )))
```

## JSON_PDA2.png

```mermaid
graph LR
  S((Object))-- #quot;{#quot; : +BeginObject(JsonObject) Discard-->1
  1((1))-- #quot;} :
    Discard#quot; -->4
  1-- JField :
    Field(fields) -->2
  2((2))-- #quot;}#quot; :
    Discard -->4
  2-- #quot;,#quot; :
    Discard -->3
  3((3))-- JField :
    Field(fields) -->2
  4((4))-. : EndObject .->E((( )))
```

## JSON_PDA3.png

```mermaid
graph LR
  S((Object))-- JObject :
    ReopenObject -->1
  1((1))-. : EndObject .->E((( )))
  S-- JArray :
    ReopenObject -->2
  2((2))-. : EndObject .->E
```

## Lrec_TermR.png

```mermaid
graph LR
  S((TermR))-- Factor :
ReopenObject --> 1
  1((1))-. : EndObject .->E((( )))
  S-- Factor :
    +BeginObject(MulExpr)
    Field(left) -->2
  2((2))-- #quot;*#quot; :
    Discard -->3
  3((3))-- TermR :
    Field(right) --> 4
  4((4))-. : EndObject .->E
```

## Lrec_TermL.png

```mermaid
graph LR
  S((TermL))-- Factor :
    ReopenObject --> 1
  1((1))-. : EndObject .->E((( )))
  S-- TermL :
    +BeginObject(MulExpr) Field(left) -->2
  2((2))-- #quot;*#quot; :
    Discard -->3
  3((3))-- Factor :
    Field(right) --> 4
  4((4))-. :
    EndObject .->E
```

## Lrec_TermL2.png

```mermaid
graph LR
  S((TermL))-- Factor :
    ReopenObject --> 1
  1((1))-. : EndObject .->E((( )))
  2((2))-- #quot;*#quot; :
    Discard -->3
  3((3))-- Factor :
    Field(right) --> 4
  4((4))-. : EndObject .->E
  1-. [leftrec] : ?? .->2
  4-. [leftrec] : ?? .->2
```

## Lrec_TermL3.png

```mermaid
graph LR
  S((TermL))-- Factor :
    ReopenObject --> 1
  1((1))-. : EndObject .->E((( )))
  2((2))-- #quot;*#quot; :
    Discard -->3
  3((3))-- Factor :
    Field(right) --> 4
  4((4))-. : EndObject .->E
  1-. [leftrec] : +EndObject
    +BeginObject(MulExpr)
    Field(left) .->2
  4-. [leftrec] : +EndObject
    +BeginObject(MulExpr)
    Field(left) .->2
```

## Calc_PDA1.png

```mermaid
graph LR
  fS((Factor))-- NUM :
    +BeginObject(NumExpr)
    Field(value) --> f1
  f1((f1))-. : EndObject .->fE((( )))
  fS-- #quot;(#quot; :
    Discard --> f2
  f2((f2))-- Expr :
    ReopenObject --> f3
  f3((f3))-- #quot;)#quot; :
    Discard -->f4
  f4((f4))-. :
    EndObject .->fE

  tS((Term))-- Factor :
    ReopenObject --> t1
  t1((t1))-. : EndObject .->tE((( )))
  t2((t2))-- #quot;*#quot; :
    Discard -->t3
  t3((t3))-- Term :
    Field(right) --> t4
  t1-. [leftrec] :
    +EndObject
    +BeginObject(BinaryOpExpr)
    Field(left) .->t2
  t4-. [leftrec] :
    +EnumItem(Multiply)
    +Field(op)
    +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t2
  t7-. [leftrec] :
    +EnumItem(Divide)
    +Field(op)
    +EndObject+BeginObject(BinaryOpExpr)
    Field(left) .->t2
  t4((t4))-. : EnumItem(Multiply)
    Field(op)
    EndObject .->tE
  t5((t5))-- #quot;/#quot; :
    Discard -->t6
  t6((t6))-- Term :
    Field(right) --> t7
  t7((t7))-. : EnumItem(Divide)
    Field(op) EndObject .->tE
  t1-. [leftrec] : +EndObject
    +BeginObject(BinaryOpExpr) Field(left) .->t5
  t4-. [leftrec] :
    +EnumItem(Multiply)
    +Field(op)
    +EndObject
    +BeginObject(BinaryOpExpr)
    Field(left) .->t5
  t7-. [leftrec] :
    +EnumItem(Divide)
    +Field(op)
    +EndObject+BeginObject(BinaryOpExpr) 
    Field(left) .->t5

  eS((Expr))-- Term :
    ReopenObject --> e1
  e1((e1))-. : EndObject .->eE((( )))
  e2((e2))-- #quot;+#quot; :
    Discard -->e3
  e3((e3))-- Expr :
    Field(right) --> e4
  e4((e4))-. : EnumItem(Add)
    Field(op)
    EndObject .->eE
  e1-. [leftrec] :
    +EndObject
    +BeginObject(BinaryOpExpr) 
    Field(left) .->e2
  e4-. [leftrec] :
    +EnumItem(Add)
    +Field(op)
    +EndObject
    +BeginObject(BinaryOpExpr)
    Field(left) .->e2
  e7-. [leftrec] :
    +EnumItem(Minus)
    +Field(op)
    +EndObject
    +BeginObject(BinaryOpExpr)
    Field(left) .->e2
  e5((e5))-- #quot;-#quot; :
    Discard -->e6
  e6((e6))-- Expr :
    Field(right) --> e7
  e7((e7))-. : EnumItem(Minus)
    Field(op)
    EndObject .->eE
  e1-. [leftrec] :
    +EndObject
    +BeginObject(BinaryOpExpr)
    Field(left) .->e5
  e4-. [leftrec] :
    +EnumItem(Add)
    +Field(op)
    +EndObject
    +BeginObject(BinaryOpExpr)
    Field(left) .->e5
  e7-. [leftrec] :
    +EnumItem(Minus)
    +Field(op)
    +EndObject
    +BeginObject(BinaryOpExpr)
    Field(left) .->e5
```

## Calc_PDA2.png

```mermaid
graph LR
  subgraph Factor
    fS((Factor))-- NUM :
      +BeginObject(NumExpr)
      Field(value) --> f1
    f1((f1))-. : EndObject .->fE((( )))
    fS-- #quot;(#quot; :
      Discard --> f2
    f2((f2))-. : ReopenObject .-> f3
    f3((f3))-- #quot;)#quot; :
      Discard -->f4
    f4((f4))-. : EndObject .->fE
  end

  subgraph Term
    tS((Term))-. : ReopenObject .-> t1
    t1((t1))-. : EndObject .->tE((( )))
    t2((t2))-- #quot;*#quot; :
      Discard -->t3
    t3((t3))-. : Field(right) .-> t4
    t4((t4))-. : EnumItem(Multiply)
      Field(op)
      EndObject .->tE
    t1-. [leftrec] : +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->t2
    t4-. [leftrec] :
      +EnumItem(Multiply)
      +Field(op)
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->t2
    t7-. [leftrec] : +EnumItem(Divide)
      +Field(op)
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->t2
    t5((t5))-- #quot;/#quot; :
      Discard -->t6
    t6((t6))-. : Field(right) .-> t7
    t7((t7))-. : EnumItem(Divide)
      Field(op)
      EndObject .->tE
    t1-. [leftrec] : +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->t5
    t4-. [leftrec] :
      +EnumItem(Multiply)
      +Field(op)
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->t5
    t7-. [leftrec] : +EnumItem(Divide)
      +Field(op)
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->t5
  end

  subgraph Expr
    eS((Expr))-. : ReopenObject .-> e1
    e1((e1))-. : EndObject .->eE((( )))
    e2((e2))-- #quot;+#quot; :
      Discard -->e3
    e3((e3))-. : Field(right) .-> e4
    e4((e4))-. : EnumItem(Add)
      Field(op)
      EndObject .->eE
    e1-. [leftrec] : +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->e2
    e4-. [leftrec] : +EnumItem(Add)
      +Field(op)
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->e2
    e7-. [leftrec] : +EnumItem(Minus)
      +Field(op)
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->e2
    e5((e5))-- #quot;-#quot; :
      Discard -->e6
    e6((e6))-. : Field(right) .-> e7
    e7((e7))-. : EnumItem(Minus)
      Field(op)
      EndObject .->eE
    e1-. [leftrec] :
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->e5
    e4-. [leftrec] :
      +EnumItem(Add)
      +Field(op)
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->e5
    e7-. [leftrec] :
      +EnumItem(Minus)
      +Field(op)
      +EndObject
      +BeginObject(BinaryOpExpr)
      Field(left) .->e5
  end

  f2-- NUM
    +BeginObject(NumExpr)
    Field(value)
    >Term->t1
    >Expr->e1
    >f2->f3 -->f1
  f2-- #quot;(#quot; :
    Discard
    >Term->t1
    >Expr->e1
    >f2->f3 -->f2

  tS-- NUM
    +BeginObject(NumExpr)
    Field(value)
    >Term->t1 -->f1
  tS-- #quot;(#quot; :
    Discard >Term->t1 -->f2
  t3-- NUM
    +BeginObject(NumExpr)
    Field(value)
    >Term->t1
    >t3->t4 -->f1
  t3-- #quot;(#quot; :
    Discard
    >Term->t1
    >t3->t4 -->f2
  t6-- NUM
    +BeginObject(NumExpr)
    Field(value)
    >Term->t1
    >t5->t6 -->f1
  t6-- #quot;(#quot; :
    Discard
    >Term->t1
    >t5->t6 -->f2

  eS-- NUM
    +BeginObject(NumExpr)
    Field(value)
    >Term->t1
    >Expr->e1 -->f1
  eS-- #quot;(#quot; :
    Discard
    >Term->t1
    >Expr->e1 -->f2
  e3-- NUM
    +BeginObject(NumExpr)
    Field(value)
    >Term->t1
    >Expr->e1
    >e3->e4 -->f1
  e3-- #quot;(#quot; :
    Discard
    >Term->t1
    >Expr->e1
    >e3->e4 -->f2
  e6-- NUM
    +BeginObject(NumExpr)
    Field(value)
    >Term->t1
    >Expr->e1
    >e5->e6 -->f1
  e6-- #quot;(#quot; :
    Discard
    >Term->t1
    >Expr->e1
    >e5->e6 -->f2
```

## Calc_PDA3.png

```mermaid
graph LR
  fS((Factor))-- NUM :
    +BeginObject(NumExpr)
    Field(value) --> f1
  f1((f1))-. : EndObject .->fE((( )))
  fS-- #quot;(#quot; :
    Discard --> f2
  f2((f2))-. : ReopenObject .-> f3
  f3((f3))-- #quot;)#quot; :
    Discard -->f4
  f4((f4))-. : EndObject .->fE

  f2-- NUM
    +BeginObject(NumExpr)
    Field(value)
    >Term->t1
    >Expr->e1
    >f2->f3 -->f1
  f2-- #quot;(#quot; :
    Discard
    >Term->t1
    >Expr->e1
    >f2->f3 -->f2
```

## Trace_Shape.png

```mermaid
graph LR
  subgraph Yes_1
    direction TB
    a1-->b1-->c1
  end

  subgraph Yes_2
    direction TB
    a2 & b2 --> c2 --> d2 & e2
  end

  subgraph No
    direction TB
    a3 & b3 --> c3 & d3 --> e3 & f3
  end

  Yes_1~~~Yes_2~~~No
```

## Trace_Shape2.png

```mermaid
graph TD
  a-- predecessors.siblingNext -->b
  b-- predecessors.siblingNext -->c
  c-- predecessors.siblingPrev -->b
  b-- predecessors.siblingPrev -->a
  d-- predecessors.first -->a
  d~~~b
  d-- predecessors.last -->c
```

## Trace_Shape3_1.png

```mermaid
graph TD
  a1[[a1]]-->b1 & c1
  b1-->f1(((f1)))
  c1-->d1 & e1-->f1
```

## Trace_Shape3_2.png

```mermaid
graph TD
  a2[[a1]]-->b2 & c2 & d2
  b2-->f2(((f2)))
  c2 & d2-->e2-->f2
```

## Trace_Shape3_3.png

```mermaid
graph TD
  subgraph Single Trace
    a3-->b3
  end
  b3-->c3 & d3-->e3(((e3)))
  a3-->f3
  e3 & f3-->g3(((g3)))
```

## Trace_Shape3_4.png

```mermaid
graph TD
  subgraph Ambiguity 1
    subgraph Ambiguity 2
      b4[[b4]]-->c4-->d4 --> e4 & f4-->g4(((g4)))
    end
    a4[[a4]]-->b4
    c4-->h4
    g4 & h4-->i4(((i4)))
  end
  S-->a4
  i4-->E
```

## DFA_PDA1.png

```mermaid
graph LR
  S((Declaration))-- ClassDeclaration :
    ReopenObject -->1
  1((1))-. EndObject .->E((( )))
```

## DFA_PDA2.png

```mermaid
graph LR
  S((Declaration))-- ClassDeclaration :
    ReopenObject -->1
  S-- Attribute :
    Field(attributes) -->S
  1((1))-. EndObject .->E((( )))
```

## DFA_PDA3.png

```mermaid
graph LR
  S((Declaration))-- ClassDeclaration :
    +DelayFieldAssignment
    ReopenObject -->1
  S-- Attribute :
    +DelayFieldAssignment
    Field(attributes) -->2
  2((2))-- Attrbute :
    Field(attributes) -->2
  2-- ClassDeclaration :
    +DelayFieldAssignment
    ReopenObject -->1
  1((1))-. EndObject .->E((( )))
```

## New_Lrec_TermL3.png

```mermaid
graph LR
  S((TermL))-- Factor :
    StackBegin --> 1
  1((1))-. : StackEnd .->E((( )))
  2((2))-- #quot;*#quot; :
    Discard --> 3
  3((3))-- Factor :
    StackSlot(2) --> 4
  4((4))-. : CreateObject(MulExpr)
    Field(left, 0)
    Field(right, 2)
    StackEnd .->E
  1-. [leftrec] :
    StackEnd
    StackBegin
    StackSlot(0) .->2
  4-. [leftrec] :
    CreateObject(MulExpr)
    Field(left, 0)
    Field(right, 2)
    StackEnd
    StackBegin
    StackSlot(0) .->2
```

## Trace_Shape3_4_1.png

```mermaid
graph LR
  b4[[b4]]-->c4-->d4 --> e4 & f4 --> g4(((g4)))
```

## Trace_Shape3_4_2.png

```mermaid
graph LR
  S[[BEGIN]]-->b4_1[b4]-->c4_1[c4]-->d4_1[d4]-->e4_1[e4]-->R_1[[BRANCH]]
  S-->b4_2[b4]-->c4_2[c4]-->d4_2[d4]-->f4_2[f4]-->R_2[[BRANCH]]
  R_1 & R_2 --> R[[RESOLVE]] --> g4(((g4)))
```

## Trace_Shape3_4_3.png

```mermaid
graph LR
  S[[BEGIN]]-->b4_1[b4]-->c4_1[c4]-->d4_1[d4]-->e4_1[e4]-->R_1[[BRANCH]]
  R_1-->b4_2[b4]-->c4_2[c4]-->d4_2[d4]-->f4_2[f4]-->R_2[[BRANCH]]
  R_2-->R[[RESOLVE]]-->g4(((g4)))
```

## Trace_Shape3_4_4.png

```mermaid
graph LR
  a4[[a4]]-->b4_g4[["b4..g4"]]
  a4-->b4-->c4-->h4
  b4_g4 & h4-->i4(((i4)))
```

## Trace_Shape3_4_5.png

```mermaid
graph LR
  S[[BEGIN]]-->a4_1[a4]-->b4_g4[["b4..g4"]]-->R_1[[BRANCH]]
  S-->a4_2[a4]-->b4-->c4-->h4-->R_2[[BRANCH]]
  R_1 & R_2 --> R[[RESOLVE]] --> i4(((i4)))
```

## Trace_Shape3_4_6.png

```mermaid
graph LR
  S[[BEGIN]]-->a4_1[a4]-->b4_g4[["b4..g4"]]-->R_1[[BRANCH]]
  R_1-->a4_2[a4]-->b4-->c4-->h4-->R_2[[BRANCH]]
  R_2-->R[[RESOLVE]]-->i4(((i4)))
```

## Parser_Compile.png

```mermaid
graph TD
  AST_FILE_1[[Ast1.txt]] -- TypeParser::ParseFile() --> AST_1[GlrAstFile]
  AST_FILE_2[[Ast2.txt]] -- TypeParser::ParseFile() --> AST_2[GlrAstFile]
  AST_1 & AST_2 -- CompileAst --> AST_MGR[AstSymbolManager]

  LEXER_FILE[[Lexer.txt]] -- CompileLexer() --> LEXER_MGR[LexerSymbolManager]
  
  SYNTAX_FILE_1[[Syntax1.txt]] -- RuleParser::ParseFile() --> SYNTAX_1[GlrSyntaxFile]
  SYNTAX_FILE_2[[Syntax2.txt]] -- RuleParser::ParseFile() --> SYNTAX_2[GlrSyntaxFile]
  AST_MGR & LEXER_MGR & SYNTAX_1 & SYNTAX_2 -- CompileSyntax() --> AST_MGR_1[SyntaxSymbolManager]

  AST_MGR_1 -- SyntaxSymbolManager::BuildCompactNFA() --> AST_MGR_2[w/ prefix merging]
  AST_MGR_2 -- SyntaxSymbolManager::BuildCrossReferencedNFA() --> AST_MGR_3[w/ only token transition ]
  AST_MGR_3 -- SyntaxSymbolManager::BuildAutomaton() --> AUTOMATON[Executable + Metadata]

  AUTOMATON -- Executable::Serialize --> BINARY[[Compressed Automaton]]
  AST_MGR -- WriteAstFiles() --> AST_GEN_FILE[["Ast.cpp + Assembler.cpp"]]
  LEXER_MGR -- WriteLexerFiles() --> LEXER_GEN_FILE[["Lexer.cpp"]]
  BINARY -- WriteSyntaxFile() --> SYNTAX_GEN_FILE[["Syntax.cpp"]]
```

## Parser_Executte.png

```mermaid
graph TD
  LEXER_GEN_FILE_2[["Lexer.cpp"]] --> LEXER[RegexLexer]
  INPUT[[Input.txt]] & LEXER -- Tokenize() --> TOKENS[List#lt;RegexToken#gt;]
  SYNTAX_GEN_FILE_2[["Syntax.cpp"]] -- Executable::Executable() --> EXECUTABLE[[Executable]]
  EXECUTABLE -- CreateExecutor() --> TRACE_MGR[TraceManager]
  TOKENS & TRACE_MGR -- Input/EndOfInput --> TRACE_MGR2[w/ Traces]
  TRACE_MGR2 -- PrepareTraceRoute() --> TRACE_MGR3[w/ Partial Execution]
  TRACE_MGR3 -- CheckMergeTraces() --> TRACE_MGR4[w/ TraceAmbiguity]
  TRACE_MGR4 -- BuildExecutionOrder() --> STEPS[ExecutionStep]
  TRACE_MGR2 -. (if not ambiguity involved) .-> STEPS
  AST_GEN_FILE_2[["Assembler.cpp"]] & TOKENS & STEPS --> AST(((Parsed AST)))
```