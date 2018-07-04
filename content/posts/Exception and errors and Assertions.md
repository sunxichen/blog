+++
date = "2018-04-10"
title = "Exception and Errors and Assertions"
tags = [
    "Java",
]
categories = [
    "Java",
]
+++

There are some typical exceptions in java such as `StackOverflowError`
`ArithmeticException`
`ArrayIndexOutOfBoundsException`
`OutOfMemoryError` and `NullPointerException`. You can also create ur own exceptions. The simplest is a `RuntimeException` that terminates execution of the program and <u>prints an error message.</u>
`throw new RuntimeException(“Error message here.”);`

![](/images/Exception-image/screenshot.png)

**Attention**:`throws` and `throw`.

**Assertions**:

An _assertion_ is a boolean expression (or a Boolean function) that u are affirming is `true` at that point in the program. If the expression is `false`, the program will terminate and report an error message.
`assert index>=0:”Negative index in method X”;`
By default, assertions are disabled. U can enable them from the command line by using the `enableassertions` flag (`-ea` for short). Assertions are for debugging.
