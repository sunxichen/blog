+++
date = "2018-04-10"
title = "Infix to postfix"
tags = [
    "Algorithms",
    "Java",
]
categories = [
    "Algorithms",
]
+++

后缀表达式，指的是不包含括号，运算符放在两个运算对象的后面，所有的计算按运算符出现的顺序，严格从左向右进行（不再考虑运算符的优先规则）。

对于一个算数表达式，我们的一般写法是这样的：
`(3 + 4) * 5 - 6`
这是**中缀表达式**( Infix )
而**后缀表达式**( Postfix )是这样的，它将运算符放在操作数的后面，如：
`3 4 + 5 * 6 -`
可以看出后序表达式中没有括号, 只表达了计算的顺序, 而这个顺序恰好就是计算器中的一般计算顺序。

**前缀表达式**( Prefix )即把运算符放在操作数的前面：
` - * + 3 4 5 6 `

Although the operators moved and now appear either before or after their respective operands, <u>the order of the operands stayed exactly the same relative to one another.</u>

那么具体怎么convert Infix to Postfix and Prefix呢？

> First,  let’s consider uses the notion of a **fully parenthesized expression**.
> `A + B * C ` can be rewritten as `( A + ( B * C ) `  to show explicitly that the multiplication has precedence over the addition. On closer observation, however, you can see that <u>each parenthesis pair also denotes the beginning and the end of an operand pair with the corresponding operator in the middle.</u>
>
> Look at the <u>right parenthesis</u> in the subexpression (B * C) above. If we were to move the multiplication symbol to that position and remove the matching left parenthesis, giving us B C *, we would in effect have converted the subexpression to <u>postfix notation.</u> If the addition operator were also moved to its corresponding right parenthesis position and the matching left parenthesis were removed, the complete postfix expression would result .
>
> ![](/images/Infix-to-Postfix/moveright.png)
>
> If we do the same thing but instead of moving the symbol to the position of the right parenthesis, we move it to the <u>left (parenthesis), we get prefix notation. The position of the parenthesis pair is actually a clue to the final position of the enclosed operator.</u>
>
> So in order to convert an expression, no matter how complex, to either prefix or postfix notation, <u>fully parenthesize the expression using the order of operations. Then move the enclosed operator to the position of either the left or the right parenthesis depending on whether you want prefix or postfix notation.</u>
>
> ` (A + B) * C - (D - E) * (F + G) `
>
> ![](/images/Infix-to-Postfix/complexmove.png)
>

## General Infix-to-Postfix Conversion

> We need to develop an algorithm to convert any infix expression to a postfix expression. To do this we will look closer at the conversion process.
>
> Consider once again the expression `A + B * C.` As shown above, `A B C * +` is the postfix equivalent. We have already noted that the operands A, B, and C stay in their relative positions. <u>It is only the operators that change position.</u> Let’s look again at the operators in the infix expression. The first operator that appears <u>from left to right</u> is `+`. However, in the postfix expression, `+` is at the end since  <u>the next operator, *, has precedence(优先级） over addition.</u> The order of the operators in the original expression is reversed in the resulting postfix expression.
>
> As we process the expression, <u>the operators have to be saved somewhere since their corresponding right operands are not seen yet. Also, the order of these saved operators may need to be reversed due to their precedence.</u> This is the case with the addition and the multiplication in this example. Since <u>the addition operator comes before the multiplication operator and has lower precedence, it needs to appear after the multiplication operator is used.</u> Because of this reversal of order, it makes sense to consider using a stack to keep the operators until they are needed.
>
> What about `(A + B) * C`? Recall that `A B + C *` is the postfix equivalent. Again, processing this infix expression from left to right, we see + first. In this case, when we see `*`, `+` has already been placed in the result expression because it has precedence over * by virtue of the parentheses. We can now start to see how the conversion algorithm will work. <u>When we see a left parenthesis, we will save it to denote that another operator of high precedence will be coming. That operator will need to wait until the corresponding right parenthesis appears to denote its position (recall the fully parenthesized technique). When that right parenthesis does appear, the operator can be popped from the stack.</u>
>
> As we scan the infix expression from left to right, we will <u>use a stack to keep the operators.</u> This will provide the reversal that we noted in the first example. The top of the stack will always be the most recently saved operator. Whenever we read a new operator, <u>we will need to consider how that operator compares in precedence with the operators, if any, already on the stack.</u>
>
> Assume the infix expression is a string of tokens delimited by spaces. The operator tokens are `*`, `/` ,`+`, and `-`, along with the left and right parentheses, `(` and `)`. The operand tokens are the single-character identifiers `A`, `B`, `C`, and so on. <u>The following steps will produce a string of tokens in postfix order.</u>

1. Create an empty stack called `opstack` for keeping operators. Create an empty list for output.

2. Convert the input infix string to a list by using the string method split.

3. Scan the token list from left to right.

	* If the token is an operand, append it to the end of the output list.

	* If the token is a left parenthesis, push it on the `opstack`.

	* If the token is a right parenthesis, pop the `opstack` until the corresponding left parenthesis <u>is removed.</u> Append each operator to the end of the output list.

	* If the token is an operator, `*`, `/`, `+`, or `-`, push it on the `opstack`. However, first remove any operators already on the `opstack` that <u>have higher or equal precedence</u> ( pop them until an operator’s precedence less than the operator ) and append them to the output list.

4. When the input expression has been completely processed, check the `opstack`. Any operators still on the stack can be removed and appended to the end of the output list.

![](images/Infix-to-Postfix/intopost.png)

优先级：

“(“ : 1

“-“ or “+” : 2

“*” or “/“ : 3

## Postfix Evaluation
As you scan the postfix expression, it is the operands that must wait, not the operators as in the conversion algorithm above. Another way to think about the solution is that <u>whenever an operator is seen on the input, the two most recent operands will be used in the evaluation.</u>

To see this in more detail, consider the postfix expression `4 5 6 * +`. As you scan the expression from left to right, you first encounter the operands 4 and 5. At this point, you are still unsure what to do with them until you see the next symbol. <u>Placing each on the stack ensures that they are available if an operator comes next.</u>

In this case, the next symbol is another operand. So, as before, push it and check the next symbol. Now we see an operator, *. <u>This means that the two most recent operands need to be used in a multiplication operation.</u> By popping the stack twice, we can get the proper operands and then perform the multiplication (in this case getting the result 30).

We can now handle this result by placing it back on the stack so that it can be used as an operand for the later operators in the expression. When the final operator is processed, there will be only one value left on the stack. Pop and return it as the result of the expression. Figure below shows the stack contents as this entire example expression is being processed.

![](/images/Infix-to-Postfix/evalpostfix1.png)

1. Create an empty stack called operandStack.

2. Convert the string to a list by using the string method split.

3. Scan the token list from left to right.

	* If the token is an operand, convert it from a string to an integer and push the value onto the operandStack.

	* If the token is an operator, `*`, `/`, `+`, or `-`, it will need two operands. Pop the operandStack twice. The first pop is the second operand and the second pop is the first operand. ( Pay special attention to `/`) Perform the arithmetic operation. Push the result back on the operandStack.

4. When the input expression has been completely processed, the result is on the stack. Pop the operandStack and return the value.

```java
//Infix expression to Postfix expression

import edu.princeton.cs.algs4.*;
public class InfixtoPostfix
{
	public static int getPriority(String s){
		if(s.equals("("))
			return 1;
		else if(s.equals("+")||s.equals("-"))
			return 2;
		else if(s.equals("*")||s.equals("/"))
			return 3;
		return 0;
	}

	public static void main( String[] args){

		Stack<String> operator = new Stack<String>();
		while( !StdIn.isEmpty()){
			String s = StdIn.readString();
			if( s.equals("("))
				operator.push(s);
			else if(s.equals("+")){
				while((!operator.isEmpty()) && getPriority(s)<=getPriority(operator.peek())){
					StdOut.print(operator.pop()+" ");
				}
				operator.push(s);
			}
			else if(s.equals("-")){
				while((!operator.isEmpty()) && getPriority(s)<=getPriority(operator.peek())){
					StdOut.print(operator.pop()+" ");
				}
				operator.push(s);
			}
			else if(s.equals("*")){
				while((!operator.isEmpty()) && getPriority(s)<=getPriority(operator.peek())){
					StdOut.print(operator.pop()+" ");
				}
				operator.push(s);
			}
			else if(s.equals("/")){
				while((!operator.isEmpty()) && getPriority(s)<=getPriority(operator.peek())){
					StdOut.print(operator.pop()+" ");
				}
				operator.push(s);
			}
			else if(s.equals(")")){
				while((!operator.isEmpty()) && !operator.peek().equals("(")){
					StdOut.print(operator.pop()+" ");
				}
				operator.pop();
			}
			else
				StdOut.print(s + " ");
		}
		while(!operator.isEmpty()){
			StdOut.print(operator.pop()+" ");
		}
	}
}
```