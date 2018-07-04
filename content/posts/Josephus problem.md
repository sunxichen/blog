+++
date = "2018-04-10"
title = "Josephus Problem"
tags = [
    "Algorithms",
    "Java",
]
categories = [
    "Algorithms",
]
+++

In the Josephus problem from antiquity, N people are in dire straits and agree to the following strategy to reduce the population. They arrange themselves in a circle (at positions numbered from 0 to N-1) and proceed around the circle, eliminating every Mth person until only one person is left. Legend has it that Josephus figured out where to sit to avoid being eliminated. Write a Queue client Josephus.java that takes M and N from the command line and prints out the order in which people are eliminated (and thus would show Josephus where to sit in the circle).

## Queue Solution:
```java
import edu.princeton.cs.algs4.*;
public class Josephus {
	public static void main(String[] args) {
		Queue<Integer> queue = new Queue<Integer>();
		int N = Integer.parseInt(args[0]); //接受参数人数N
		int M = Integer.parseInt(args[1]); //接受参数要被处置的第几个人

		for( int i=0;i<N;i++)
			queue.enqueue(i); //初始化队列

		while( !queue.isEmpty() ){
			for ( int i=0;i<M-1;i++){
				queue.enqueue(queue.dequeue()); //skipped persons; re-attach them to the end of queue
			}
            StdOut.print(queue.dequeue() + " "); //Mth person being executed.
		}
		StdOut.println();
	}
}
```

在遍历队列的过程中，将已遍历过的元素再放到队列尾部，这样就能形成一个圆。或许涉及到遍历圆的问题都可以这样考虑？👀

## Recursive Solution

This solution is written for people numbered zero to N-1. The process is iterated until a single person remains. Practicing with examples motivates the general recursive solution. Let us examine P(5, 3) where there are five people and every third person is removed.

![](/images/Josephus-problem/screenshot.png)

On the first round of elimination the 2 is removed and the remaining people in 3, 4, 0, 1 are renumbered as 0, 1, 2, and 3. ( Why 3 renumbered as 0? Cause the second round start at 3 ) Note, the new position is related to the old position by the formula:
` (new # + M) mod N = old# `
For example, we have (1 + 3) mod 5 = 4 and (3 + 3) mod 5 = 1 in figure 2.
On the second round of elimination the new 2 has been eliminated and the people in new numbers 3, 0 and 1 are renumbered from 0 to 2 (Figure 3). <u>Note that N is now 4 since we began this round with only people._ In figure 3 we see that (0 + 3) mod 4 = 3 and (1 + 3) mod 4 = 0. If we continue this pattern the person in the fourth position is the last to remain.</u>

From above we have the observed pattern `(new # + M) mod N = (old #)`
Notice that `(new #)` can be represented by P(N-1, M) and `(old #) `can be represented by P(N, M).
By substitution into the observed pattern we get the following recursive formula when N ≥ 2;
`(P(N-1, M) + M)mod N = P(N, M)`.

<u>Thus, the recursive solution is P(N, M) = 0 if N = 1 OR P(N, M) = (P(N-1, M) + M)mod N for N ≥ 2.</u>
For P(5, 3) we work down the column on the left and then back up the column on the right.
P(5, 3) = (P(4, 3) + 3) mod 5              which is (0 + 3) mod 5 = 3
P(4, 3) = (P(3, 3) + 3) mod 4              which is (1 + 3) mod 4 = 0
P(3, 3) = (P(2, 3) + 3) mod 3              which is (1 + 3) mod 3 = 1
P(2, 3) = (P(1, 3) + 3) mod 2              which is (0 + 3) mod 2 = 1
P(1, 3) = 0 since N = 1.

```java
import edu.princeton.cs.algs4.*;
public class Josephus {
	public static int P(int n,int m){
		if (n==1) {
			return 0;
		}
		else
			return ((P(n-1,m)+m)%n);
	}
	public static void main(String[] args) {
	    int N = Integer.parseInt(args[0]);
		int M = Integer.parseInt(args[1]);
		StdOut.println(P(N,M));
	}
}
```
