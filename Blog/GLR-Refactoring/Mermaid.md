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
  1((1))-- #quot;} : Discard#quot; -->4
  1-- JField : Field(fields) -->2
  2((2))-- #quot;}#quot; : Discard -->4
  2-- #quot;,#quot; : Discard -->3
  3((3))-- JField : Field(fields) -->2
  4((4))-. : EndObject .->E((( )))
```

## JSON_PDA3.png

```mermaid
graph LR
  S((Object))-- JObject : ReopenObject -->1
  1((1))-. : EndObject .->E((( )))
  S-- JArray : ReopenObject -->2
  2((2))-. : EndObject .->E
```

## Lrec_TermR.png

```mermaid
graph LR
  S((TermR))-- Factor : ReopenObject --> 1
  1((1))-. : EndObject .->E((( )))
  S-- Factor : +BeginObject(MulExpr) Field(left) -->2
  2((2))-- #quot;*#quot; : Discard -->3
  3((3))-- TermR : Field(right) --> 4
  4((4))-. : EndObject .->E
```

## Lrec_TermL.png

```mermaid
graph LR
  S((TermL))-- Factor : ReopenObject --> 1
  1((1))-. : EndObject .->E((( )))
  S-- TermL : +BeginObject(MulExpr) Field(left) -->2
  2((2))-- #quot;*#quot; : Discard -->3
  3((3))-- Factor : Field(right) --> 4
  4((4))-. : EndObject .->E
```

## Lrec_TermL2.png

```mermaid
graph LR
  S((TermL))-- Factor : ReopenObject --> 1
  1((1))-. : EndObject .->E((( )))
  2((2))-- #quot;*#quot; : Discard -->3
  3((3))-- Factor : Field(right) --> 4
  4((4))-. : EndObject .->E
  1-. [leftrec] : ?? .->2
  4-. [leftrec] : ?? .->2
```

## Lrec_TermL3.png

```mermaid
graph LR
  S((TermL))-- Factor : ReopenObject --> 1
  1((1))-. : EndObject .->E((( )))
  2((2))-- #quot;*#quot; : Discard -->3
  3((3))-- Factor : Field(right) --> 4
  4((4))-. : EndObject .->E
  1-. [leftrec] : +EndObject +BeginObject(MulExpr) Field(left) .->2
  4-. [leftrec] : +EndObject +BeginObject(MulExpr) Field(left) .->2
```

## Calc_PDA1.png

```mermaid
graph LR
  fS((Factor))-- NUM : +BeginObject(NumExpr) Field(value) --> f1
  f1((f1))-. : EndObject .->fE((( )))
  fS-- #quot;(#quot; : Discard --> f2
  f2((f2))-- Expr : ReopenObject --> f3
  f3((f3))-- #quot;)#quot; : Discard -->f4
  f4((f4))-. : EndObject .->fE

  tS((Term))-- Factor : ReopenObject --> t1
  t1((t1))-. : EndObject .->tE((( )))
  t2((t2))-- #quot;*#quot; : Discard -->t3
  t3((t3))-- Term : Field(right) --> t4
  t1-. [leftrec] : +EndObject +BeginObject(BinaryOpExpr) Field(left) .->t2
  t4-. [leftrec] : +EnumItem(Multiply) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t2
  t7-. [leftrec] : +EnumItem(Divide) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t2
  t4((t4))-. : EnumItem(Multiply) Field(op) EndObject .->tE
  t5((t5))-- #quot;/#quot; : Discard -->t6
  t6((t6))-- Term : Field(right) --> t7
  t7((t7))-. : EnumItem(Divide) Field(op) EndObject .->tE
  t1-. [leftrec] : +EndObject +BeginObject(BinaryOpExpr) Field(left) .->t5
  t4-. [leftrec] : +EnumItem(Multiply) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t5
  t7-. [leftrec] : +EnumItem(Divide) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t5

  eS((Expr))-- Term : ReopenObject --> e1
  e1((e1))-. : EndObject .->eE((( )))
  e2((e2))-- #quot;+#quot; : Discard -->e3
  e3((e3))-- Expr : Field(right) --> e4
  e4((e4))-. : EnumItem(Add) Field(op) EndObject .->eE
  e1-. [leftrec] : +EndObject +BeginObject(BinaryOpExpr) Field(left) .->e2
  e4-. [leftrec] : +EnumItem(Add) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->e2
  e7-. [leftrec] : +EnumItem(Minus) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->e2
  e5((e5))-- #quot;-#quot; : Discard -->e6
  e6((e6))-- Expr : Field(right) --> e7
  e7((e7))-. : EnumItem(Minus) Field(op) EndObject .->eE
  e1-. [leftrec] : +EndObject +BeginObject(BinaryOpExpr) Field(left) .->e5
  e4-. [leftrec] : +EnumItem(Add) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->e5
  e7-. [leftrec] : +EnumItem(Minus) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->e5
```

## Calc_PDA2.png

```mermaid
graph LR
  subgraph Factor
    fS((Factor))-- NUM : +BeginObject(NumExpr) Field(value) --> f1
    f1((f1))-. : EndObject .->fE((( )))
    fS-- #quot;(#quot; : Discard --> f2
    f2((f2))-. : ReopenObject .-> f3
    f3((f3))-- #quot;)#quot; : Discard -->f4
    f4((f4))-. : EndObject .->fE
  end

  subgraph Term
    tS((Term))-. : ReopenObject .-> t1
    t1((t1))-. : EndObject .->tE((( )))
    t2((t2))-- #quot;*#quot; : Discard -->t3
    t3((t3))-. : Field(right) .-> t4
    t4((t4))-. : EnumItem(Multiply) Field(op) EndObject .->tE
    t1-. [leftrec] : +EndObject +BeginObject(BinaryOpExpr) Field(left) .->t2
    t4-. [leftrec] : +EnumItem(Multiply) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t2
    t7-. [leftrec] : +EnumItem(Divide) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t2
    t5((t5))-- #quot;/#quot; : Discard -->t6
    t6((t6))-. : Field(right) .-> t7
    t7((t7))-. : EnumItem(Divide) Field(op) EndObject .->tE
    t1-. [leftrec] : +EndObject +BeginObject(BinaryOpExpr) Field(left) .->t5
    t4-. [leftrec] : +EnumItem(Multiply) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t5
    t7-. [leftrec] : +EnumItem(Divide) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->t5
  end

  subgraph Expr
    eS((Expr))-. : ReopenObject .-> e1
    e1((e1))-. : EndObject .->eE((( )))
    e2((e2))-- #quot;+#quot; : Discard -->e3
    e3((e3))-. : Field(right) .-> e4
    e4((e4))-. : EnumItem(Add) Field(op) EndObject .->eE
    e1-. [leftrec] : +EndObject +BeginObject(BinaryOpExpr) Field(left) .->e2
    e4-. [leftrec] : +EnumItem(Add) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->e2
    e7-. [leftrec] : +EnumItem(Minus) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->e2
    e5((e5))-- #quot;-#quot; : Discard -->e6
    e6((e6))-. : Field(right) .-> e7
    e7((e7))-. : EnumItem(Minus) Field(op) EndObject .->eE
    e1-. [leftrec] : +EndObject +BeginObject(BinaryOpExpr) Field(left) .->e5
    e4-. [leftrec] : +EnumItem(Add) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->e5
    e7-. [leftrec] : +EnumItem(Minus) +Field(op) +EndObject+BeginObject(BinaryOpExpr) Field(left) .->e5
  end

  f2-- NUM +BeginObject(NumExpr) Field(value) >Term->t1 >Expr->e1 >f2->f3 -->f1
  f2-- #quot;(#quot; : Discard >Term->t1 >Expr->e1 >f2->f3 -->f2

  tS-- NUM +BeginObject(NumExpr) Field(value) >Term->t1 -->f1
  tS-- #quot;(#quot; : Discard >Term->t1 -->f2
  t3-- NUM +BeginObject(NumExpr) Field(value) >Term->t1 >t3->t4 -->f1
  t3-- #quot;(#quot; : Discard >Term->t1 >t3->t4 -->f2
  t6-- NUM +BeginObject(NumExpr) Field(value) >Term->t1 >t5->t6 -->f1
  t6-- #quot;(#quot; : Discard >Term->t1 >t5->t6 -->f2

  eS-- NUM +BeginObject(NumExpr) Field(value) >Term->t1 >Expr->e1 -->f1
  eS-- #quot;(#quot; : Discard >Term->t1 >Expr->e1 -->f2
  e3-- NUM +BeginObject(NumExpr) Field(value) >Term->t1 >Expr->e1 >e3->e4 -->f1
  e3-- #quot;(#quot; : Discard >Term->t1 >Expr->e1 >e3->e4 -->f2
  e6-- NUM +BeginObject(NumExpr) Field(value) >Term->t1 >Expr->e1 >e5->e6 -->f1
  e6-- #quot;(#quot; : Discard >Term->t1 >Expr->e1 >e5->e6 -->f2
```

## Calc_PDA3.png

```mermaid
graph LR
  fS((Factor))-- NUM : +BeginObject(NumExpr) Field(value) --> f1
  f1((f1))-. : EndObject .->fE((( )))
  fS-- #quot;(#quot; : Discard --> f2
  f2((f2))-. : ReopenObject .-> f3
  f3((f3))-- #quot;)#quot; : Discard -->f4
  f4((f4))-. : EndObject .->fE

  f2-- NUM +BeginObject(NumExpr) Field(value) >Term->t1 >Expr->e1 >f2->f3 -->f1
  f2-- #quot;(#quot; : Discard >Term->t1 >Expr->e1 >f2->f3 -->f2
```

## Trace_Shape.png

```mermaid
graph TD
  subgraph Yes 1
    a1-->b1-->c1
  end

  subgraph Yes 2
    a2 & b2 --> c2 --> d2 & e2
  end

  subgraph No
    a3 & b3 --> c3 & d3 --> e3 & f3
  end
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

## Trace_Shape3.png

```mermaid
graph TD
  subgraph 1
    a1[[a1]]-->b1 & c1
    b1-->f1(((f1)))
    c1-->d1 & e1-->f1
  end

  subgraph 2
    a2[[a1]]-->b2 & c2
    b2-->f2(((f2)))
    c2-->d2 & e2-->f2
  end
```