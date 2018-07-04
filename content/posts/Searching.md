+++
date = "2018-05-30"
title = "Searching"
tags = [
    "Algorithms",
    "Java",
    "Data Structure",
]
categories = [
    "Algorithms",
]
+++

## 前言

我们用术语**Symbol Table**来描述我们可以通过specifying a *key* 来获得我们想要存储的信息(*value*)的一种抽象机制。

Symbol tables有时又可以称作*dictionaries*,也可以被称作*indices*. 我们将在Searching的应用一节中更深入得讨论两者。有三种典型的数据结构可以实现高效地symbol tables: <span style="border-bottom:2px dashed purple;">Binary search trees, Red-Black trees, and hash tables.</span>

**Definition**: A symbol table is a data structure for key-value pairs that supports two operations:*insert*(put) a new pair into the table and *search* for (get) the value associated with a given key.

**API**

![](/images/Searching/shot1.png)

对于实现symbol tables我们接受下面的实现准则，来使我们的代码连续，精炼且有效:

*Generics.* 对于symbol tables，我们强调key和value扮演的不同角色。在搜索中，我们准确地区分key和value的类型。而不是像在Priority queue中，将keys隐含得表达在item中。其中我们还将考虑keys是**Comparable**(Sorting中介绍的Interface)的拓展情形。

*Duplicate keys*.

* 每个key只有一个value与之对应（table中没有重复的keys)
* 当client向表中put(insert)一对已经存在的key-value对时，(key已经存在)，新的value将取代旧的那一个。

*Null keys*.Keys must not be null.

*Null values*. No key can be associated with the value **null**.这样做可以有两个好处：首先，当我们给定一个key时，我们想要检测table中该key值有没有一个value与之对应，可以通过检查get()方法有没有返回一个null值。第二，在删除操作中，可以直接对给定的key赋其value值为null来实现。

*Deletion*.删除操作大体上有两种实现方式：*lazy* deletion, where we associate keys in the table with null, then perhaps remove all such keys at some later time; and *eager* deletion, where we remove the key from the table immediately. The code `put(key, null)` is an lazy implementation of `delete(key)` In our symbol-table implementations that do not use the default delete(), the put() implementations on the book site begin with the defensive code
`if (val == null) {delete(key); return;}`
To ensure that no key in the table is associated with null.

*Iteration*.为了能够让client处理所有的keys和values，我们需要加上`implements Iterable<Key>`语句。对于symbol tables我们使用更简单的可替代的方法，用keys()方法返回一个Iterable<key>对象来使client能够使用iterate through keys.

*Key equality*.Object equality.

![](/images/Searching/shot2.png)

**Ordered symbol tables**
一些Symbol table的实现方法中，利用了隐含在key中的Comparable属性来实现put()和get()方法。我们可以将这些symbol table想象成能够保持keys in order的table.

实现Comparable interface的symbol table有以下的API:

![](/images/Searching/shot3.png)

**Searching cost model**.
在比较不同实现方法时，我们计算compares. In (rare) cases where compares are not in the inner loop, we count array accesses.


**Sample clients.**
We consider two clients: a test client that we use to trace algorithm behavior on small inputs and a performance client.

* Test client. The main() client in each of our symbol table implementations reads in a sequence of strings from standard input, builds a symbol table by associating the value i with the ith key in the input, and then prints the table.

* Frequency counter. Program [FrequencyCounter.java](https://algs4.cs.princeton.edu/31elementary/FrequencyCounter.java.html) is a symbol-table client that finds the number of occurrences of each string (having at least as many characters as a given threshold length) in a sequence of strings from standard input, then iterates through the keys to find the one that occurs the most frequently.

首先我们先考虑实现两种比较简单的实现方法：

* [Sequential search in an unordered linked list.](https://algs4.cs.princeton.edu/31elementary/SequentialSearchST.java.html)

To implement get(), we scan through the list, using equals() to compare the search key with the key in each node in the list. If we find the match, we return the associated value; if not, we return null. To implement put(), we also scan through the list, using equals() to compare the client key with the key in each node in the list. If we find the match, we update the value associated with that key to be the value given in the second argument; if not, we create a new node with the given key and value and insert it at the beginning of the list. This method is known as sequential search.

![](/images/Searching/shot4.png)

代码实现由上面的超链接给出。

**Proposition A**. Unsuccessful search and insert in an (unordered) linked-list symbol table both use N compares, and successful search uses N compares in the worst case. In particular, inserting N keys into an initially empty linked-list symbol table uses ~N^2/2 compares.

* [Binary search in an ordered array.](https://algs4.cs.princeton.edu/31elementary/BinarySearchST.java.html)

The underlying data structure is two parallel array, with the keys kept in order. The heart of the implementation is the rank() method, which returns the number of keys smaller than a given key. For get(), the rank tells us precisely where the key is to be found if it is in the table (and, if it is not there, that it is not in the table). For put(), the rank tells us precisely where to update the value when the key is in the table, and precisely where to put the key when the key is not in the table. We move all larger keys over one position to make room (working from back to front) and insert the given key and value into the proper positions in their respective arrays.

![](/images/Searching/shot5.png)

**Binary Search.**

The basic idea is simple: we maintain indices into the sorted key array that delimit the subarray that might contain the search key. To search, we compare the search key against the key in the middle of the subarray. If the search key is less than the key in the middle, we search in the left half of the subarray; if the search key is greater than the key in the middle we search in the right half of the subarray; otherwise the key in the middle is equal to the search key.

![](/images/Searching/shot6.png)

**Proposition B.** Binary search in an ordered array with N keys uses no more than lg N + 1 compares for a search (successful or unsuccessful) in the worst case.

**Proposition C.** Inserting a new key into an ordered array uses ~ 2N array accesses in the worst case, so inserting N keys into an initially empty table uses ~ N^2 array accesses in the worst case.

## Binary Search Tree

在一个 *binary tree* 中，我们做出以下限定条件：每一个结点(Node)只有一个parent结点（除了root结点，其没有parent结点。）;然后，每个结点都有两个*links*,一个是*left link* 一个是 *right link* ，这两个links指向的结点叫做该结点的*left child* and *right child*.我们可以还可以将link看做指向两个subtrees, the trees whose root is the referenced node.这两个links要么指向null,要么指向一个左/右子树。

**Definition**. A *binary search tree* (BST) is a binary tree where each node has a Comparable key (and an associated value) and satisfies the restriction that <span style="border-bottom:2px dashed purple;">the key in any node is larger than the keys in all nodes in that node’s left subtree and smaller than the keys in all nodes in that node’s right subtree.</span>

![](/images/Searching/shot7.png)

### Basic implementation

*Representation*. 每一个Node中都含有一个key, 一个value, 一个left link, 一个right link和一个统计node个数的变量N. The left link指向一个由所有比当前结点要小(key)的结点组成的BST. The right link 同理指向一个bigger BST. 变量N用来统计所有的root结点是当前结点的sub-BSTs(左、右)中Node的个数, 我们将0赋值给null links.我们保证
`size(x) = size(x.left) + size(x.right) + 1`
对于所有结点x都成立。
注意到，对于同一个set of keys,可能有不同的BSTs来展示同一个set. 我们总能保证这些keys in sorted order.

![](/images/Searching/shot8.png)

（结点E的N为6，是因为其左右子树的结点数各为2和3）

*Search*. 如果一个结点中含有我们给定的key, 就说*search hit*, 然后我们返回该结点中对应的value值。否则我们得到一个*search miss*(and return null).我们能够得到一个 **递归** 的search算法：if the tree is empty, we have a search miss; if the search key is equal to the key at the root, we have a search hit. Otherwise, we search (recursively) in the appropriate subtree, moving left if the search key is smaller, right if it is larger.

![](/images/Searching/shot9.png)

对于search hit, 递归path在含有key的结点处停止。对于search miss, 递归在null link处停止。

*Insert*.要搜索的key不在tree中，我们就会在一个null link中停止。我们要做的就是用一个新建的包含该key的结点来代替该null link.put()方法也是一个递归的过程。You can think of the code before the recursive calls as happening on the way down the tree. Then think of the code after the recursive calls as happening on the way up the tree. In simple BSTs, the only new link is the one at the bottom.

![](/images/Searching/shot10.png)
（在way up过程中要更新每个结点的N)

### Analysis

BST算法的running time取决于tree的形状，换种说法，也就是取决于keys被insert的次序。

![](/images/Searching/shot11.png)

### Order-based methods and deletion

BSTs能被广泛运用的原因之一就是它能让我们keep keys in order.

*Minimum and maximum*. If the left link of the root is null, the smallest key in a BST is the key at the root; if the left link is not null, the smallest key in the BSTs is the smallest key in the subtree rooted at the node referenced by the left link.这就产生了一个 **递归** 的寻找最小值的方法min(), move left直到找到一个null link. 寻找最大值也是类似的，move right直到找到一个null link.

*Floor and ceiling*. If a given key key is *less* than the key at the root of a BST, then the floor of key (the largest key in the BST less than or equal to key) *must* be in the left subtree. If key is greater than the key at the root, then the floor of key *could* be in the right subtree, but only if there is a key smaller than or equal to key in the right subtree; if not (or if key is equal to the key at the root) then the key at the root is the floor of key. Finding the ceiling is similar, interchanging right and left(and less and larger).

*Selection*.BST中的selection与我们在selection sort中用到的partition有些类似。假设我们想要寻找rank为k的key. 如果当前结点t的左子树的N(size(t.left))大于k，我们就递归得在该左子树中寻找rank k的key。如果size(t.left)等于k, 那么就返回在root处的key(找到了)。如果小于k，那么我们也同样递归得在右子树中寻找rank为k-t-1的key.

![](/images/Searching/shot12.png)

*Rank*.如果给定的key等于当前root结点(t)的key,那么则返回size(t.left);如果给定的key小于t的key，则返回rank(key, t.left)（递归调用rank(),在t的左子树中继续寻找对应的rank值);如果大于t的key, 则返回`size(t.left) + 1 + rank(key, x.right)`(递归)

*Deleting the minimum/maximum*. 对于deleteMin()方法，我们go left直到找到一个left link是null的结点(m)，然后我们将 <span style="border-bottom:2px dashed purple;">指向m的link</span> 替换为m.right. deleMax()也是相应对称的。

![](/images/Searching/shot13.png)

*Delete*. 我们要delete一个结点(x)，为了保证delete过后整个tree保持keys in order的特性，我们需要用x的successor来替代m.因为x的successor即是所有比x大的最小的那个(smallest in x.right). 这个替代过程可以简单地总结为下面的几步：

* Save a link to the node to be deleted in t.

* Set x to point to its successor min(x.right).

* Set the right link of x (which is supposed to point to the BST containing all the keys larger than x.key) to deleteMin(t.right), the link to the BST containing all the keys that are larger than x.key after deletion.

* Set the left link of x (which is null) to t.left (all the keys that are less than both the deleted key and its successor).

![](/images/Searching/shot14.png)

*Range queries*.To implement the keys() method that returns the keys in a given range, we begin with a basic recursive BST traversal method, known as inorder traversal. To illustrate the method, we consider the task of printing all the keys in a BST in order. To do so, print all the keys in the left subtree (which are less than the key at the root by definition of BSTs), then print the key at the root, then print all the keys in the right subtree, (which are greater than the key at the root by definition of BSTs).

```java
private void print(Node x) {
   if (x == null) return;
   print(x.left);
   StdOut.println(x.key);
   print(x.right);
}
```

To implement the two-argument keys() method, we modify this code to add each key that is in the range to a Queue, and to skip the recursive calls for subtrees that cannot contain keys in the range.

![](/images/Searching/shot15.png)

[Full code for BST algorithm.](https://algs4.cs.princeton.edu/32bst/BST.java.html)

## Red-black BSTs

BST在大多数应用中都能有良好的性能，但是在最坏情况下表现糟糕。Red-black BSTs能够保证在任何情况下的开销都是logarithmic.

### 2-3 search trees

BST中每个结点都是2-nodes类型的，即每个结点都包含两个links，包含一个key. 在2-3 search trees中，我们允许树除了有2-nodes以外，还可以有3-nodes(有3 links和2个keys)

![](/images/Searching/shot16.png)

***Search***.与BST类似，我们逐个将要search的key与树中的key比较。如果与其中任何一个key相等，则search hit.否则，我们根据结点中两个keys确定的区间，相应地跟随link所确定的subtrees中search.

![](/images/Searching/shot17.png)


***Insert into a 2-node***. 2-3 tree的插入操作比较复杂，需要分多种情况考虑。为了维持2-3 tree perfect balance的特性，我们在向2-node插入时，可以这样做： <span style="border-bottom:2px dashed purple;">如果search在一个2-node处停止了</span> (search miss, 需要插入了)，我们只需要将这个2-node替换为3-node，并包含原来的key以及新插入的key.

![](/images/Searching/shot18.png)


***Insert into a tree consisting of a single 3-node***.首先我们先考虑最简单的一种情况，如果一个数只有一个3-node.在向这个node插入时，我们先将这个3-node变位4-node(3 keys, 4 links).然后将这个4-node split成三个2-node(3个keys中，中间的做root). 在插入之前，树的高度是0.插入之后，树的高度是1.

![](/images/Searching/shot19.png)


***Insert into a 3-node whose parent is a 2-node***. 对于这种情形，我们依然是先将3-node变为一个4-node,然后split成三个2-node.同时将处于中间的node与parent的2-node组合成一个3-node.

![](/images/Searching/shot20.png)

***Insert into a 3-node whose parent is a 3-node***.同上一种情况类似，split成三个2-node, 同时将中间node与parent组成一个4-node，之后再将这个4-node继续split下去…直到遇见一个2-node(插入中间node后，变成3-node,不用再split了)或者遇见一个根节点的3-node(然后该根节点变为4-node).

![](/images/Searching/shot21.png)


***Splitting the root***.如果我们根据上面的几个步骤，最后我们可能会有一个根节点是4-node的tree.我们要做的就是将这个4-node split成3个2-node, <span style="border-bottom:2px dashed purple;">使树的高度增加1</span>.(上面几步的操作都没有使树的高度变化).

![](/images/Searching/shot22.png)

所有的这些转换都是local的，也就是实现这些转变要做的只是更改某一被指定的node和links.而不用去改变树中的其他部分。

这些转变同时保证了2-3 tree的一个重要的全局特征：the tree is perfectly balanced. 上述所有的转变都维持了这一特征。即所有null link到root的distance相同。在split root时候，将这个distance增加了1.

![](/images/Searching/shot23.png)

接下来，我们要考虑怎么如何具体实现2-3 trees了，直接实现也可以实现，但是上述insert的多种情况会让直接实现变得很复杂。这时，我们就有了Red-black tree这种数据结构。

### Red-black BSTs

***Encoding 3-nodes***.首先要考虑的是我们如何实现2-3 trees中的3-node?我们将links看做两种不同的类型：*red links*, which bind together two 2-nodes to present 3-nodes, and *black links*, which bind together the 2-3 tree.特别地, <span style="border-bottom:2px dashed purple;">we represent 3-nodes as two 2-nodes connected by a single red link that *leans left*</span>(one of the 2-nodes is the left child of the other). 这种表示方法能让我们不加修改地直接使用标准BST算法中的get()方法。

![](/images/Searching/shot24.png)


***A 1-1 correspondence***.每一个红黑树都对应着一个2-3 trees.红黑树既是一个BST又是一个2-3 trees.所以它既具有BST搜索方法简单高效的特性，又有2-3 trees的insertion-balancing的特性。

![](/images/Searching/shot25.png)

***Color representation***.下面，我们就该考虑如何表示links的颜色了。既然每一个结点都有一个唯一的link指向它(该link来自这个结点的parent).我们通过在结点中增加一个boolean变量( <span style="border-bottom:2px dashed purple;">true is red</span> )来表示这条来自parent的link的颜色，同时，这条link的颜色也是该link所连child node的颜色。Null links is black. When we refer to the color of a node, we are referring to the color of the link pointing to it and vice versa.

![](/images/Searching/shot26.png)

***Rotations***.这一操作是Red-black BSTs算法中最核心的部分了。我们在将一个2-3 trees转换为红黑树的实现过程中，可能会遇到right-leaning red links或者一行中有两个two red links.我们需要用rotation操作来转变red links的方向。假设我们有一个right-leaning red link，我们要将该link转变为left-leaning, 这个操作叫做*left rotation*.

![](/images/Searching/shot27.png)

Rotate left过程实际上是一个switch root的过程，将root switch到larger key上。

*Right rotate* 与left rotate过程类似

![](/images/Searching/shot28.png)

***Flipping colors***.Flipping color的过程实际上就是split 4-node的过程。除了将两个child node的颜色flip为黑色外，还要将parent(root)的color flip为黑色。

![](/images/Searching/shot32.png)

***Keeping the root black***.在插入操作中(尤其是在向3-node插入中)，flip color会将root的颜色变位红色。严格来说，一个红色的root表示这个root是一个3-node的一部分。但只不是我们想要的(我们想要一个树的根节点只有一个结点)，所以我们在每一个插入操作之后(root变红是在插入操作过程中)将root再flip black.

***Insert into a single 2-node***.
如果新的key比当前key要小(search终止处的key)，我们只需要新建一个red link和一个对应的新node.如果新的key比当前树中的key要大，我们附加一个新node和一个right-leaning link并`root = rotateLeft(root)`

![](/images/Searching/shot29.png)

***Insert into a 2-node at the bottom***.

在底部插入一个新node的步骤与上面类似。

![](/images/Searching/shot30.png)

***Insert into a tree with two keys (in a 3-node).***

![](/images/Searching/shot31.png)

（在一个3-node中插入一个新结点有三种情况。）

***Insert into a 3-node at the bottom***.我们在一个3-node的底部插入一个新的结点，就像上面的过程一样。Flipping color导致指向中间结点的link为红色，也就是将这个中间结点传递给了其parent(组成一个3-node/4-node)，于是对于parent来说，又将我们带到了上面所述的相同情形，我们可以move up the tree to fix.

![](/images/Searching/shot33.png)

***Passing a red link up the tree***. After doing any necessary rotations, we flip colors, which turns the middle node to red. From the point of view of the parent of that node, that link becoming red can be handled in precisely the same manner as if the red link came from attaching a new node: we pass up a red link to the middle node.
We can accomplish the insertion by performing the following operations, one after the other on each node as we pass up the tree from the point of insertion:

* If the right child is red and the left child is black, rotate left.

* If both the left child and its left child are red, rotate right.

* If both children are red, flip colors.

This sequence(顺序不能变) handles each of the cases just described. The first operation handles both the rotation necessary to lean the 3-node to the left when the parent is a 2-node and the rotation necessary to lean the bottom link to the left when the new red link is the middle link in a 3-node.

### Red-black BSTs Deletion
删除操作要比Insert操作更复杂一些，因为我们既要way down the search path (to allow for a node to be deleted), 并且又要way up the search path, where we split any leftover 4-node.
在开始deletion实现之前，我们先看一下另一种数据结构。

***Top-down 2-3-4 trees***.对于2-3-4 trees来说，在插入算法中，允许4-node的存在。insert过程中，在way down时，我们也要保证当前的结点不是一个4-node(保证当前结点不是4-node，与2-3-4 trees允许4-node存在并不矛盾，为了保证当前结点不是4-node，我们在split过程中可能会导致别的结点变成4-node.这时我们不需要像2-3 trees一样将这个”别的”结点也split,而是允许它暂存，留到way up过程中再split)，来保证在bottom时总是有room来插入新的结点。之后，在way up过程中做必要的转换来平衡2-3-4 trees, 将way down过程中可能产生的的4-node split掉。在way down split 4-node的转变跟2-3 trees是一样的。

![](/images/Searching/shot34.jpg)
这样下来，在bottom 一定是一个2-node或是3-node,使我们能够有空间插入新的结点。

要实现上面的算法，需要：

* Represent 4-nodes as a balanced subtree of three 2-nodes, which both the left and right child connected to the parent with a red link.

* Split 4-nodes on the way *down* the tree <span style="border-bottom:2px dashed purple;">with color flips</span>.

* Balance 4-nodes on the way *up* the tree <span style="border-bottom:2px dashed purple;">with rotations</span>, as for insertion.

(在way down过程中只用flip colors来split 4-node.在way up时用rotation来split 4-node)

要实现这种算法，我们只需要更改基于2-3 trees的Red-black BSTs算法中的put()方法中的一行代码：move the `flipColors()` call (and accompanying test) to before the recursive calls (between the test for null and the comparison).

***Delete the minimum***.我们先看删除最小结点的操作(from a 2-4 tree)。该操作是基于一个这样的观察：如果在path的bottom我们遇见的是个3-node/4-node，删除最小的结点就是删除这个3-node/4-node最左边的结点。所以这就给了我们这样的思路：我们在way down过程中，保证当前结点不是一个2-node。我们可以这样做：

* If the left child of the current node is not a 2-node, there’s nothing to do.(因为我们在找最小值的过程中，都是在左子树中找，所以search path都是在左边，故不用管右边的)

* If the left child is a 2-node and **its** immediate sibling is not a 2-node, move a key from the sibling to the left child.

* If the left child and its immediate sibling are 2-nodes, then combine them with the smallest key in the parent to make a 4-node, changing the parent from a 3-node to a 2-node or from a 4-node to a 3-node.

Way down结束后，我们就会在bottom有一个3-node/4-node,删去最小的即可。之后再在way up the tree, split unused temporary 4-node.

![](/images/Searching/shot35.jpg)

```java
private Node moveRedLeft(Node h) {
    //Assuming that h is red and both h.left and h.left.left are black,
    //make h.left or one of its children red.总结了所有可能的情况。
    flipColors(h);
    if(isRed(h.right.left))
    {
        h.right = rotateRight(h.right);
        h = rotateLeft(h);
        flipColors(h);
    }
    return h;
}

public void deleteMin(){
    //if both children of root are black,set root to red.
    if (!isRed(root.left) && !isRed(root.right))
        root.color = RED;
    root = deleteMin(root);
    if (!isEmpty()) root.color = BLACK;//递归结束后再把root color重设回来。
}

public Node deleteMin(Node h){
    //delete h(the minimum)
    if(h.left == null)
        return null;
    if(!isRed(h.left) && !isRed(h.left.left))
        h = moveRedLeft(h); //保证左边都不是2-node
    h.left = deleteMin(h.left);
    return balance(h);
}

//balance()维持红黑树的性质，即在way up时Split暂存的4-node
public Node balance(h){
    if (isRed(h.right)) h = rotateLeft(h);
    if(isRed(h.left) && isRed(h.left.left))
        h = rotateRight(h);
    if(isRed(h.left) && isRed(h.right))
        flipColors(h);
    h.size = size(h.left) + size(h.right) + 1;
    return h;
}

//注意在删除中flipColors()与在insert中不同，在删除中，we set the
//parent to black and the two children to red.
//如果将这两种情况综合起来，我们得到下面的flipColors()方法。
private void flipColors(Node h){
    h.color = !h.color;
    h.left.color = !h.left.color;
    h.right.color = !h.right.color;
}
```

***Delete***.The same transformations along the search path just described for deleting the minimum are effective to ensure that the current node is not a 2-node during a search for any key. If the search key is at bottom, we can just remove it. If the key is not at the bottom, then we have to **exchange** it with its successor as in regular BSTs. Then, since the current node is not a 2-node, we have reduced the problem to deleting the minimum in a subtree whose root is not a 2-node, and we can use the procedure just described for that subtree. After the deletion, as usual, we split any remaining 4-nodes on the search path on the way up the tree.

```java
private Node moveRedRight(Node h){
    //Assuming that h is red and both h.right and h.right.lrft
    //are black, make h.right or one of its children red.
    flipColors(h);
    if (isRed(h.left.left))
        h = rotateRight(h);
    flipColors(h);
    return h;
}

public void deleteMax(){
    if (!isRed(root.left) && !isRed(root.right))
        root.color = RED;
    root = deleteMax(root);
    if (!isEmpty()) root.color = BLACK;
}

private void deleteMax(Node h)
{
    if (isRed(h.left))
        h = rotateRight(h);
    if(h.right == null)
    return null;
    if(!isRed(h.right) && !isRed(h.right.left))
        h =moveRedRight(h);
    h.right = deleteMax(h.right);
    return balance(h);
}

public void delete(Key key)
{
    if (!isRed(root.left) && !isRed(root.right))
        root.color = RED;
    root = delete(root,key);
    if(!isEmpty())  root.color = BLACK;
}

private Node delete(Node h, Key key){
    if (key.compareTo(h,key) < 0){
        if(!isRed(h.left) && !isRed(h.left.left))
            h = moveRedLeft(h);//保证way down时左边都不是2-node
        h.left = delete(h.left,key);
    }
    else
    {
        if (isRed(h.left))
            h = rotateRight(h);
        if (key.compareTo(h.key) == 0 && (h.right == null))
            return null;//是最大值，直接删除
        if (!isRed(h.right) && !isRed(h.right.left))
            h = moveRedRight(h);
        if (key.compareTo(h.key) == 0)
        {
            h.val = get(h.right, min(h.right).key);//找successor
            h.key = min(h.right).key;
            h.right = deleteMin(h.right);//删掉successor
        }
        else h.right = delete(h.right,key);
    }
    return balance(h);
}
```

[Full implement for Red-black BSTs](https://algs4.cs.princeton.edu/33balanced/RedBlackBST.java.html).

![](/images/Searching/shot36.png)

## Hash Tables

### 概述

如果在key-value对中，key是数值比较小的整型，那么我们可以用数组来实现unordered symbol table.用数组的index来表示key,这样可以使我们很快地获得与key相关联的value.本节，我们将考虑使用hashing来实现将key-value对用数组实现。

首先我们需要考虑实现一个hash function来实现将search key转变为array index.理想情况下，不同的Key被map为不同的index.然而实际情况往往是两个或更多的key被hash成了相同的array index.于是，这就产生了我们要处理的第二个部分—*collision-resolution*.我们将考虑两种解决collision的方法*separate chaining* and *linear probing*

![](/images/Searching/shot37.png)


Hashing 是一个典型的 *space-time tradeoff* 例子。如果我们没有存储空间的限制，那么我们可以取一个尽可能大的数组来尽可能把所有不同的key map成不同的Index.如果我们没有时间的限制，我们就可以使用很少一部分的空间，通过一系列在unordered数组之间搜索的操作来实现Hash Table. Hashing提供了一种能在两者之间取得很好的平衡的方法。通过Hahing，我们能实现insert and search只需要constant(amortized) time的symbol table.使得它成为在实现基本的symbol table时，是个很好的选择。

### Hash function

如果我们有一个能存储M个key-value对的数组，那么我们需要一个function, 能将所有的key map成[0,M-1]之间的整数。我们需要寻找一个function, 不仅能计算出hash值，还能要保证所有的key都是uniformly distribution的，也就是对于任意一个key, 任何0-M-1之间的整数都是等可能出现的。

此外，hash function还应当依据key的类型而定。也就是说，对不同类型的key，我们要有不同的hash function.

***Positive integer***. 对于key是正整数类型的，最常见的hash function是 *modular hashing*: 我们取一个质数M(即数组的大小, in particular, not a power of 2)，对于任何一个正整数k, 选取k/M后的余数作为hash. (k%M) 如果M不是一个质数，it may be the case that not all of the bits of the key play a role, which amounts to missing an opportunity to disperse the values evenly. 比如说，如果key是十进制的，而我们选取了M=10的k次幂，那么就会导致key中k个最不重要(least significant)的数字被用到了。

![](/images/Searching/shot38.png)

（注意在M=100中，所有801,701,601等以1结尾的都没map成了1, 实际上在这些三位数中，只有最后一位1被利用了，而其他两位数字没有作用）

***Floating-point numbers***. 如果key的类型是介于0-1之间的实数，我们可以将key乘以M然后四舍五入(round off)取最近的整数，来获得[0, M-1]之间的整数index. 虽然这种方法很直观，但却是defective的。因为这种方法给key中most significant的位更多的比重，而那些least significant位play no role.一种解决这种问题的方法是在这些浮点类型的二进制表示上使用正整数的modular hashing, 这正是Java所使用的方法。

***Strings***. Modular hashing对像string一样的long keys也一样适用。比如下面的代码就计算了一个string类型的字符串s的hash:

```java
int hash = 0;
for (i = 0; i < s.length(); i++)
	hash = ( R * hash + s.charAt(i) ) % M;
```

`charAt()`返回一个16位的非负整数`char`的值。 If **R** is greater than any character value, this computation would be equivalent to treating the String as an <span style="border-bottom:2px dashed purple;">N-digit base-R</span> integer, computing the remainder that results when dividing that number by M. 像 *Horner’s method* 这样的算法，如果R足够小以至于不会导致溢出，那么我们就能得到一个[0, M-1]之间的整数。像R=31这样的质数即能保证key的所有数字位都能发挥作用。

***Compound keys***. 如果我们的key由多个integer field组成，那么我们可以将他们混在一起当做一个String来对待。比如说key算是一个Date类型(DDMMYY)，我们可以这样计算hash:

`int hash = ((( day * R + month ) % M ) * R + year ) % M;`

如果R足够小以至于不会导致溢出，那么我们就能得到一个[0, M-1]之间的整数。像R=31这样的质数即能保证key的所有数字位都能发挥作用。

***Java convention***. 那么在Java中是怎么实现这一基本的功能呢？Java通过让每种类型的data都继承一个叫`hashCode()`的方法来实现让每种类型都能实现上述功能。hashCode()必须保证 *consistent with equal* 即，如果a.equals(b), 那么a.hashCode()也必须等于b.hashCode()。注意如果两个objects有相同的hashCode()，这两个objects不一定相同，必须用equals()方法来判定。所以当我们自己实现user-defined type的hashCode()时，必须重写(override)hashCode()和equals()两个方法。

***Converting a hashCode() to an array index***. 因为我们的目标是产生一个32位的整数作为array index. hashCode()方法只是把任意一种类型的数据产生一个32位整数，这个整数并不能满足直接被拿来作为array index的要求(均匀分布、[0-M-1]等)。我们可以通过下面的步骤来实现将hashCode()转换为index:

```java
	private int hash(Key x){
		return (x.hashCode() & 0x7fffffff) % M
} //7是为了屏蔽掉符号位，与1 & 保持不变，与0 & 变0
```

***User-defined hashCode()***. 当我们需要自己定义一种类型的数据时，需要我们重写hashCode()和equals()方法。下面以我们自己定义的Transaction类型为例。

```java
	public class Transaction
{
		...
		private final String who;
		private final Date when;
		private final double amount;

		public int hashCode(){
			int hash = 17;
			hash = 31 * hash + who.hashCode();
			hash = 31 * hash + when.hashCode();
			hash = 31 * hash + ((Double) amount).hashCode(); //要讲primitive type转化为对应的Wrapper type
			return hash;
		}
}
```

We have three primary requirements in implementing a good hash function for a given data type:

* It should be deterministic—equal keys must produce the same hash value.

* It should be efficient to compute.

* It should uniformly distribute the keys.

为了能够预测hash table的性能，我们要对hush function做出如下的假设：
**Assumption J (uniform hashing assumption)**. The hash function that we use uniformly distributes keys among the integer values between 0 and M-1.

### Hashing with separate chaining
在解决collision问题时（即两个或多个key被hash为同一个index），一种直接的解决方法是，将所有冲突的key-value对，用一个linked list链接(chain)起来（即将那些所有Index值相同的key放到一个linked list中）。这种方法基本的想法是，选取一个M足够大的数组并且list被充分地sort过以保证高效地搜索。搜索一个key包含两个步骤: 先hash以便找到那个可能存放着key的list, 再在list中搜索那个key.

![](/images/Searching/shot39.png)

（数组st是M行的，即有M个lists, 这M个list共同含有N个keys）

因为从hash得到index再在数组中搜索这一步骤已经很快了，关键在于如何高效地实现在list中的搜索。在本章第一节中我们实现了[SequentialSearchST](https://algs4.cs.princeton.edu/34hash/SequentialSearchST.java.html)类(Sequential search in an unordered linked list)所以我们可以直接将list用这个类的对象来定义。list的平均长度是N/M, 即每个list平均都包含有N/M对key-value对。
[SeparateChainingHashST.java](https://algs4.cs.princeton.edu/34hash/SeparateChainingHashST.java.html)

**Proposition K**. In a separate-chaining hash table with M lists and N keys, the probability (under Assumption J) that the number of keys in a list is within a small constant factor of N/M is extremely close to 1. of N/M is extremely close to 1. (Assumes an idealistic hash function.)

This classical mathematical result is compelling, but it completely depends on Assumption J. Still, in practice, the same behavior occurs.

**Property L**. In a separate-chaining hash table with M lists and N keys, the number of compares (equality tests) for search and insert is proportional to N/M.

### Hashing with linear probing
另一种实现Hash table地方法是用一个有M entries的数组存放N对key-value对(M>N). 因为M>N, 所以这种实现方法是利用这些空出来的『位置』来解决collision.

这种方法叫做linear probing: 当collision发生时，我们check next entry. Linear probing 有三种可能：

* key equal to search key: search hit

* empty position (null key at indexed position): search miss

* key not equal to search key: try next entry

![](/images/Searching/shot40.png)

注意linear probing在实现时，我们实际上使用了 <span style="border-bottom:2px dashed purple;">两个平行的数组</span> ，这两个数组共享同一个index,一个数组用来存放key, 灵液个数组用来存放value.

***Deletion***. 下面我们来考虑这种方法的删除操作。假设我们想删除上图中的C, 接着搜索H. H的Hash是4，但是它在表中实际地位置是7。如果我们仅简单得将位置5的C设置为null来实现删除，那么在搜索H时，H发现位置4已经被A占用了，将会向后移动一位，此时发现位置5是null,会停止搜索，并以为是search miss.这显然是错误的。所以，我们应该将被删除的key(C)右边的所有 <span style="border-bottom:2px dashed purple;">在同一cluster</span> (A, C, S, H, L在同一cluster, P, M另一cluster…)的key重新插入表中(S重新插入表中还是在位置6，因为S的hash是6，H重新插入表中到位置5，L-7)

[LinearProbingHashST.java](https://algs4.cs.princeton.edu/34hash/LinearProbingHashST.java.html)

**Proposition M**. In a linear-probing has table of size M with N = α M keys, the average number of probes (under Assumption J) is ~ 1/2 (1 + 1 / (1 - α)) for search hits and ~ 1/2 (1 + 1 / (1 - α)^2) for search misses or inserts.

## Application
本节中，我们将考虑symbol table的具体应用：

* A dictionary client and an indexing client that enable fast and flexible access to information in comma-separated-value files (and similar formats), which are
widely used to store data on the web.

* An indexing client for building an inverted index of a set of files.

* A sparse-matrix data type that uses a symbol table to address problem sizes far beyond what is possible with the standard implementation.

### 我应该选用那种实现方式？
在选择时，应首先考虑hashing和BST(包括Red-Black BST)。Hashing的优点在于代码更简单且能有最优的搜索次数(constant). BST的优点在于它基于更简单的抽象接口，不需要特别单独设计hash function. Red-Black BST能够保证最坏情况下的性能表现。并且BST支持更多的操作(比如rank, select, sort and range search).

![](/images/Searching/shot41.png)


### Set
一些Symbol table不需要有value, 只需要key.而我们有个约束条件是不允许有重复的key，所以这实际上就是实现了一个集合(Set).

![](/images/Searching/shot42.png)

[SET.java](https://algs4.cs.princeton.edu/35applications/SET.java.html)

### Whitelist and blacklist

一个最简单的symbol table的应用叫做白名单(whitelist), 任何在白名单文件中的key都被视为是”good”, client会从标准输入中传递过来一个包含许多key的文件，白名单应用即是输出这个文件中任何不在白名单中的key, 而那些在白名单中的key则忽视掉(或者反过来，忽视掉不在白名单中的key)。因为我们只关心key所以实际上使用的是上面提到的Set.

[WhiteFilter.java](https://algs4.cs.princeton.edu/35applications/WhiteFilter.java.html)

### Dictionary clients

另一个最常见的symbol table就是『字典』了。它能让我们很容易地查询到想要的信息，并且更新字典中的信息。

[LookupCSV.java](https://algs4.cs.princeton.edu/35applications/LookupCSV.java.html)

（注意，The command-line arguments are the file name and two integers, one specifying the field to serve as the key and the other specifying the field to serve as the value.）

### Indexing client
与Dictionary不同的是，indexing client是每一个key有多个value. 一种实现方法是将所有的value放在一个队列（或者Bag, 或者Set）里，然后将key与这个队列关系起来。Inverted index是将value, key的角色互换一下，也就是说解决的是『如果给定一个value，那么我们能找到与其对应的key么？』

[MovieIndex.java](https://algs4.cs.princeton.edu/35applications/MovieIndex.java.html)
[FileIndex.java](https://algs4.cs.princeton.edu/35applications/FileIndex.java.html)

### Sparse vectors
稀疏矩阵的计算是Google搜索PageRank算法的一个核心。它是基于一个稀疏矩阵与一个向量之间的『点乘』运算的。

![](/images/Searching/shot43.png)

（假设有一个N×N的矩阵和一个大小为N的向量，则正常点乘运算需要正比于N²的运算，而在实际情况中，N是非常大的，比如Google的搜索中，有数量庞大的网页需要被我们检索以返回符合搜索内容的网页。）

幸运的时，大多数情况下这个矩阵是稀疏的(spares)，即矩阵中大多数元素都是0. 在Google的例子中，也是符合这个现象的。平均下来，每一行的非零元素是一个很小的常数。实际上所有的web网页只会link到一小部分网页上。
我们可以用多个由稀疏向量构成的一个数组来表示一个稀疏矩阵。

![](/images/Searching/shot44.png)

[SparseVector.java](https://algs4.cs.princeton.edu/35applications/SparseVector.java.html)

那么在计算稀疏矩阵和向量的点乘时，可以参考下面的代码:

```java
	SparseVector[] a;
	a = new SparseVector[N];
	double[] x = new double[N]; //用作乘数的向量
	double[] b = new double[N]; //用作存储结果的向量
	...
	// 初始化a[]和x[]
	...
	for (int i = 0; i < N; i++)
		b[i] = a[i].dot(x); //用矩阵中的每一行（稀疏向量）与乘数向量点乘。
```


___

**All content of this article is from the book—— *Algorithms Fourth Edition by Robert Sedgewick and Kevin Wayne* and its website https://algs4.cs.princeton.edu/home/. This article is my notes while studying the book.**
