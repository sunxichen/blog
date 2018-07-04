+++
date = "2018-04-10"
title = "Circular Rotation"
tags = [
    "Algorithms",
    "Java",
]
categories = [
    "Algorithms",
]
+++

> A string _s_ is a _circular rotation_ of a string _t_ if it matches when the characters are _circularly shifted_ by any number of positions; e.g., **ACTGACG** is a circular shift of **TGACGAC**, and vice versa. Detecting this condition is important in the study of genomic sequences. Write a program that checks whether two given strings s and t are circular shifts of one another.
> _Hints_: The solution is a one-liner with indexOf(), length(), and sting concatenation(串联).

回环变位
有两种思路:

**思路一**：
将字符串s按任意位置拆成左右两个子串，交换位置后再拼接起来与t比较，相等即是回环变位。

```java
class test{
   public static void main(String[] args) {
     String s="ACTGACG";
     String t="TGACGAC";

     public static boolean isCircular(String s,String t){
       if(s.length()!=t.length())
         return false;
       else{
         int length=s.length()
         for(int i=1;i<=length;i++){
           String left=s.substring(0,i);
           String right=s.substring(i,length);
           if((right+left).equals(t))
             return true;
         }
         return false;
       }
     }
}
```

**思路二**：
将s与自己拼接起来，如果拼接后的字符串包含字符串t，则是回环变位

```java
public static void main(String[] args) {
     String s="ACTGACG";
     String t="TGACGAC";
     public static boolean isCircular(String s,String t){
       if(s.length()==t.length()&&s.concat(s).indexOf(t)>=0)//(s+s).indexOf(t)
         return true;
       else
         return false;
     }
```

另外**求一个字符串的逆序的递归方法**：

```java
public static String reverse(String s){
       int N=s.length();
       if(N<=1) return s;
       String a=s.substring(0,N/2);
       String b=s.substring(N/2,N);
       return reverse(b)+reverse(a);
     }
```

![](/images/Circular-Rotation-image/IMG_20171112_200650.jpg)