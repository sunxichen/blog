+++
date = "2018-04-08"
title = "Union-find"
tags = [
    "Algorithms",
    "Java",
]
categories = [
    "Algorithms",
]
+++
# Union-find

问题引入：输入是一系列整型数字对。(p,q) 意思为p与q相连。它具有如下性质:

* 反身性( Reflexive )：p与p自己也相连。
* 对称性( Symmetric )：p与q相连，同时q也与p相连。
* 传递性( Transitive )：p与q相连，q与r相连，那么p与r相连。

当两个对象相连时，则他们就属于同一类( equivalence class )。我们的目标是区分出来没有关系的那些数值对( pairs )

To achieve the desired goal, we need to devise a data structure that can remember sufficient information about the pairs it has seen to be able to decide whether or not a new pair of objects is connected. This task is called _dynamic connectivity_.

Dynamic connectivity可以有一下的几种用途：

* Networks: 这些实数对可以代表一些在大型网络中相连的计算机，所以我们可以通过程序来决定是否需要架设新的线路来实现p和q之间的信息交流（如果p和q相连则不用架设新的线路）。或者，这些实数对代表在社交网络中的人，(p,q)代表着friendship.
* _Mathematical sets_: 在更抽象的层面上，我们可以将这些数值归于数学集合。当我们处理(p,q)时，我们实际上是在考虑它们是否属于同一集合里。如果p, q不在同一集合，那么我们就将两个集合合并，使它们属于同一集合。


![](/images/union-find-images/dynamic-connectivity-tiny.png)

We refer to the objects as _sites_, the pairs as _connections_, and the equivalence classes as _connected components_
**Union-find API**
```java
public class UF
	UF(int N)		//initialize N sites with integer names (0 to N-1)
	void union( int p, int q) //merge two components if the two sites are in different components( add connections between p and q )
	int find( int p ) //returns an integer component identifier for a given site
	boolean connected( int p, int q)	//determines whether two sites are in the same components.
	int count() //returns the number of components.
/*
	We start with N components, and each union() that merge two components decrements the number of components by 1.
*/
```

**Implementation**
```java
public class UF {
	private int[] id;
	private int N;

	public UF(int N) {
		this.N = N;
		id = new int[N];
		for (int i=0; i<N; i++) {
			id[i]=i;
		}
	}
	public int count() {
		return N;
	}

	public boolean connected(int p, int q){
		return find(p) == find(q);
	}
	public int find(int p){}
	public void union(int p, int q){}

	public static void main(String[] args) {
		int N = StdIn.readInt();
		UF uf = new UF(N);
		while(!StdIn.isEmpty()){
			int p = StdIn.readInt();
			int q = StdIn.readInt();
			if (uf.connected(p,q)) continue;
			uf.union(p,q);
			StdOut.println(p + " " + q);
		}
		StdOut.println(uf.count() + " components");
	}
}
```

_Quick-find_:
```java
	public int find(int p){
		return id[p];
	}
	public void union(int p, int q){
		int pID = find(p);
		int qID = find(q);

		if (pID == qID)	return;

		for (int i=0; i<id.length;i++){
			if(id[i] == pID)	id[i]==pID;
		}
		N--;
	}
```
因为find()只访问一次数组，所以叫做quick-find.
算法思想: id[p]代表着p所在的component, union()的时候，遍历数组，将所有component值等于pID的节点都改为qID.

_Quick-Union_:
```java
	public int find(int p){
		while(p!=id[p]) p=id[p];
		return p;
	}
	public void union(int p, int q){
		int pRoot = find(p);
		int qRoot = find(q);
		if (pRoot == qRoot)	return;

		id[pRoot] = qRoot;
		N--;
	}
```
算法思想：Specifically, the id[] entry for each site is the name of another site in the same component (possibly itself)--we refer to this connection as a _link_. We start at the given site, follow its link to another site, follow that site’s link to yet another site, and so forth, following links until reaching a _root_, a site that has a link to itself. _Two sites are in the same component if only if this process leads them to the same root. To union, we follow links to find the roots associated with p and q, then rename one of the components by linking one of these roots to the other.

_Weighted quick-union_:
```java
public class UF {
	private int[] id;
	private int[] sz;
	private int N;

	public UF(int N) {
		this.N = N;
		id = new int[N];
		sz = new int[N];
		for (int i=0; i<N; i++) {
			id[i]=i;
			sz[i]=1;
		}
	}
	public int count() {
		return N;
	}

	public boolean connected(int p, int q){
		return find(p) == find(q);
	}
	public int find(int p){
		while(p!=id[p]) p=id[p];
		return p;
	}
	public void union(int p, int q){
		int pRoot = find(p);
		int qRoot = find(q);
		if (pRoot == qRoot)	return;

		if(sz[pRoot]<sz[qRoot]){ id[pRoot]=qRoot; sz[qRoot]+=sz[pRoot];}
		else	{ id[qRoot]=pRoot; sz[pRoot]+=sz[qRoot];}
		N--;
	}

	public static void main(String[] args) {
		int N = StdIn.readInt();
		UF uf = new UF(N);
		while(!StdIn.isEmpty()){
			int p = StdIn.readInt();
			int q = StdIn.readInt();
			if (uf.connected(p,q)) continue;
			uf.union(p,q);
			StdOut.println(p + " " + q);
		}
		StdOut.println(uf.count() + " components");
	}
}
```
算法思想：在合并地时候，我们并不是像quick-union那样任意选择合并方向，而是总是将较小的树合并到较大的树上。所以需要一个数组sz[]来记录数的大小。该数的大小有根节点保存，即sz[root] = size.

_Weighted quick union with path compression_:
```java
	public int find(int p){
		int root = p;
		while(root != id[root]) //寻找点p的root
			root = id[root];
		while(p != root){	//将p->root这个过程中遇到的所有点(即p的parent, grandparent...)的parent改为root
			int newp = id[p];
			id[p] = root; //将p的parent改为root
			p = newp; //p移向p的parent
		}
		return root;
	}
	public void union(int p, int q){
		int pRoot = find(p);
		int qRoot = find(q);
		if (pRoot == qRoot)	return;

		if(sz[pRoot]<sz[qRoot]){ id[pRoot]=qRoot; sz[qRoot]+=sz[pRoot];}
		else	{ id[qRoot]=pRoot; sz[pRoot]+=sz[qRoot];}
		N--;
	}
```
**WQUPC**算法思想：
Ideally, we would like every node to link directly to the root of its tree.
Note that, id is a forest, id[I] is the parent of i. If id[i]=i, it means that i is a root.

**Analysis**

**_Quick-find_**:

每个find()访问一次数组，每个union()访问N+3~2N+1次数组
每个union()先调用两次find(), 两次访问。遍历数组，访问N次。然后改变数组的值，要写数组1~N-1次。
所以对于一个有N对的问题集，如果我们以单一component结束的话，至少要调用N-1次union(), 于是dynamic connectivity with quick-find can be quadratic-time process.

**_Quick-union_**:

Definition: The _size_ of a tree is its number of nodes. The _depth_ of a node in a tree is the number of links on the path from it to the root. The _height_ of a tree is the maximum depth among its nodes.

The number of array accesses used by find() is 1 plus the twice the depth of the node corresponding to the given site.(Immediate from code) The number of array accesses used by union() and connected() is the cost of the two find() operations.(plus 1 for union() if the given sites are in different trees)

**_Weighted quick-union_**:
对于Quick-union，由于合并树的方向是任意的，可能会导致树过高，在一些情况下算法的时间复杂度并不一定比quick-find优秀。所以提出了改进的Weighted quick-union.

The weighted algorithm can guarantee logarithmic performance.

**Proposition**. The depth of any node in a forest built by weighted quick-union for N sites is at most lg N.

**Proof**: 归纳法.

![](/images/union-find-images/IMG_20180318_141018.jpg)

**Corollary**.

For weighted quick-union with N sites, the worst-case order of growth of the cost of find(), connected(), and union() is log N.

**Summary**:

![](/images/union-find-images/screenshot.png)

WQUPC is very close to the best that we can do for this problem.
