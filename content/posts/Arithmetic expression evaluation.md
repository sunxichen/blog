+++
date = "2018-04-10"
title = "Arithmetic Expression Evaluation"
tags = [
    "Algorithms",
    "Java",
]
categories = [
    "Algorithms",
]
+++

How java do this  calculation?:
`( 1 + ( ( 2 + 3 ) * ( 4 * 5 ) ) )`

An _arithmetic expression_ is either a number, or a left parentheses followed by an  arithmetic expression followed by an operator followed by another arithmetic expression followed by right parenthesis.

**Dijkstra’s Two-Stack Algorithm for Expression Evaluation**

* Push _operands_ onto the _operand stack._

* Push _operators_ onto the _operator stack._

* Ignore _left_ parentheses.

* On encountering a _right_ parentheses, pop an operator, pop the requisite number of operands, and push onto the operand stack the result of applying that operator to those operands.
After the final right parenthesis has been processed, there is one value on the stack, which is the value of the expression.

Any time the algorithm encounters a subexpression consisting of two operands separated by an operator, all surrounded by parentheses, it leaves the result of performing that operation on those operands on the operand stack, so we can think of replacing the subexpression by the value to get an expression that would yield the same result.

```
( 1 + ( ( 2 + 3 ) * ( 4 * 5 ) ) )
( 1 + ( 5 * ( 4 * 5 ) ) )
( 1 + ( 5 * 20 ) )
( 1 + 100 )
101
```

```java
import java.util.Arrays;
import edu.princeton.cs.algs4.*;
class Evaluate{

   public static void main(String[] args) {
     Stack<String> ops=new Stack<String>();
     Stack<Double> vals=new Stack<Double>();
     while(!StdIn.isEmpty()){
     String s = StdIn.readString();//因为每个字符之间是whitespace所以每个字符是一个字符串
     if(s.equals("("));
     else if(s.equals("+") ops.push(s);
     else if(s.equals("-") ops.push(s);
     else if(s.equals("*") ops.push(s);
     else if(s.equals("/") ops.push(s);
     else if(s.equals("sqrt") ops.push(s);
     else if(s.equals(")") {
       String op=ops.pop();
       double val=vals.pop();
       if(op.equals("+"))
         val=vals.pop()+val;
       else if(op.equals("-"))
         val=vals.pop()-val;//顺序不能反
       else if(op.equals("*"))
         val=vals.pop()*val;
       else if(op.equals("/"))
         val=vals.pop()/val;
       else if(op.equals("sqrt"))
         val=Math.aqrt(val);
       vals.push(val);
     }
     else vals.push(parseDouble(s));
   }
   StdOut.println(vals.pop());
   }
}

```
