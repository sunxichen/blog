+++
date = "2018-04-10"
title = "Linked List"
tags = [
    "Algorithms",
    "Java",
	"Data Structer",
]
categories = [
    "Data Structure",
]
+++

When writing code involving linked list, we must always be careful to properly handle the exception cases ( when the linked list is empty, when the list has only one or two nodes ) and the boundary cases ( dealing with the first or  last items ).

## Reverse a Linked List

Write a function that takes the head **Node** in a linked list as argument and  (destructively) reverses the list, returning the **head** Node in the result.

### Iterative solution

在迭代解法中，我们的思路是改变每个Node的链接关系。即，让每一个Node指向前一Node而不是后一个Node。（因为第一个Node不存在前一Node，所以使第一个Node（reverse前）指向null.)
`1 -> 2 -> 3 -> 4 -> null `   to  ` null <- 1 <- 2 <- 3 <- 4 `
因为我们不想改变head（指向第一个Node)，所以用一个变量 ` currNode` 来表示当前Node.

1. 现在我们来遍历链表，改变链接关系。

2. 首先将1.next改为null，这样1便不指向2，指向了null.

3. 然后，我们改变`currNode`, 使它指向下一个Node 2. 由于我们改变了1.next，Node 2的地址便无从知晓，所以我们还需要一个变量`nextNode`来存储下一个Node的地址信息。

4. 通过`nextNode = currNote.next`以及`currNode = nextNode`，我们便将`currNode`下移到Node 2.

5. 现在我们来改变Node 2的链接关系，使其指向Node 1. 由于Node并没有前一Node的相关信息，所以我们还需要一个变量`prevNode`用于在遍历过程中存储前一Node的相关信息(`prevNode=currNode`)。然后通过`currNode.next=prevNode`来改变链接关系。

6. 最后遍历结束后，`prevNode`指向最后一个Node 4，也就是reverse后的第一个Node，改变head，使其指向Node 4.

具体步骤：

1. 保留Next Node信息。

2. 改变连接关系。

3. 存储当前Node信息，供下一Node使用。

4. 使currNode下移。

![](/images/Linked-List/Linked-List-Reversal.png)

```java
public static Node reverse( Node head){
	Node currNode = head;
	Node prevNode = null;
	Node nextNode = null;
	while ( currNode!=null){
		nextNode = currNode.next;
		currNote.next = prevNode;
		prevNode = currNode;
		currNode = nextNode;
	}
	head = prevNode;
	return head;
}
```

### Recursive Solution:

Assuming the linked list has _N_ nodes, we recursively reserve the last _N-1_ nodes, and then carefully append the first node to the end.

```java
public Node reserve ( Node first )
{
	if ( first == null ) return null;
	if ( first.next == null ) return first; //Exit condition
	Node second = first.next;
	Node rest = reverse ( second );
	second.next = first;
	first.next = null;
	return rest;
}
```


![](/images/Linked-List/IMG_20180126_221552.jpg)

## Delete kth element in Linked List

1. Fix the links:
( k-1 )th Node.next = ( k + 1 )th Node = ( k-1 )th Node.next.next

2. Free the space.

```java
public static Node delete(Node head, int k){
	Node currNode = head;
	if(k==1){
		head = currNode.next;
		currNode=null;
	}

	for(int i =0; i< n-2;i++)
		currNode=currNode.next;//k-1th Node

	Node kNode = currNode.next;//kth Node
	currNode.next=kNode.next;//set k-1th Node to k+1th Node
	kNode=null;
	return head;
}
```

## Find()

Write a method find() that takes a linked list and a string **key** as arguments and returns **true** if some node in the list has **key** as its item field, **false** otherwise.

```java
public static boolean find(LinkedList ls, int k){
	Node current = ls.head;
	while (current!=null && current.item!=k){
		current=current.next;
	}
	if(current==null)
		return false;
	else
		return true;
}
```

## Doubly Linked List
[DoublyLinkedList](https://algs4.cs.princeton.edu/13stacks/DoublyLinkedList.java.html)