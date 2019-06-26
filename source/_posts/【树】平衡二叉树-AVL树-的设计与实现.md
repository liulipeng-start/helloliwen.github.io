---
title: 【树】平衡二叉树(AVL树)的设计与实现
categories:
  - 算法与数据结构
  - 树
abbrlink: ff266de3
date: 2019-06-24 21:21:25
tags:
keywords:
description: 普通BST存在的问题，AVL树的定义、设计与实现，AVL四种旋转算法的实现，AVL插入、删除操作的实现，并介绍了AVL最少结点数和最多结点数的问题。
---

## 一、普通二叉查找树的问题

　　基于二叉查找树的特性，即对于树中的每个结点T（T可能是父结点）,它的左子树中所有项的值小T中的值，而它的右子树中所有项的值都大于T中的值。这意味着该树所有的元素可以用某种规则进行排序(取决于Comparable接口的实现)，以致于二叉树查找树能够胜任快速地查找过程，这个查找的过程的时间复杂度为O(logN)。但是这个时间复杂度并不是严格意义上的O(logN)，在某些情况下还是会上升到O(N)。显然这并不是一件好事情，因此本篇我们将来讨论另一种更为稳定的二叉树，它就是AVL树。

<!--more-->

如果利用上篇实现的二叉搜索树将一段已排序好的数据一个个插入后，就会发现如下情况了：

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4cl1rvnrmj20rd0f2af1.jpg)

　　从图中我们可以发现，把已排序的1-9数据进行正序和倒序插入后，树的结构已变成单向左子树或者右子树了。如果我们在往里插入已排序的数据，那么单向左子树或者右子树越来越长，此时已跟单链表没有什么区别了。因此对此结构的操作时间复杂度自然就由O(logn)变成O(n)了，这也就是普通二叉查找树不是严格意义上O(logn)的原因。

　　那么该如何解决这个问题呢？事实上一种解决的办法就是要有一个称为平衡的附加结构条件：任何结点的深度不得过深。而这种数据结构就是我们本篇要分析的平衡二叉树（AVL），它本身也是一种二叉查找树，只不过不会出现前面我们分析的情形罢了。

## 二、平衡二叉树的定义

　　通过上面的分析，我们明白的普通二叉查找树的不足，也知道了如何去解决这个缺点，即构建树时要求任何结点的深度不得过深（子树高度相差不超过1），而最终这棵树就是平衡二叉树（Balanced Binary Tree）。它是G.M. Adelson-Velsky 和 E.M. Landis在1962年在论文中发表的，因此又叫AVL树。这里我们还需要明确一个概念，AVL树只是实现平衡二叉树的一种方法，它还有很多的其他实现方法如红黑树、替罪羊树、Treap、伸展树等。

　　接着来了解一下AVL树的特性：<font color="red">一棵AVL树是其每个结点的左子树和右子树的高度最多相差1的二叉查找树(空树的高度为-1)，这个差值也称为平衡因子</font>（其取值可以是1，0，-1，平衡因子是某个结点左右子树层数的差值，有的书上定义是左边减去右边，有的书上定义是右边减去左边，这样可能会有正负的区别，但是这个并不影响我们对平衡二叉树的讨论）。如下图

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4cl7r2x5yj20rq0dcgqr.jpg)

　　理解了平衡二叉树的概念后，我们再思考一下，那些操作可能引起平衡发生变化呢？显然只有那些引起结点数量变化的操作才可能导致平衡被改变，也就是删除和插入操作了。如下图，我们把6插入到图a后，结构变成了图b，这时原本的平衡二叉树就失去平衡了。

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4clbhkd95j20rz0ehq7b.jpg)

　　显然图b已失去平衡。如果发生这样的情况，我们就必须考虑插入元素后恢复二叉树的平衡性质。实际上也总是可以通过对树进行简单的修复来让其重新恢复到平衡，而这样的简单操作我们就称之为**旋转**。当然旋转也有单旋转和双旋转之分，下面我们将会一一分析。

　　这里有点需要明白的是，无论是插入还是删除，只有那些从插入或者删除点到根结点路径上的结点的平衡才有可能被改变，上图中也就是从结点6到根结点5的平衡被改变了。因为只有这些结点的子树才可能发生变化，所以最终也只需针对这些点进行平衡修复操作即可。

## 三、平衡二叉树的设计与实现

　　ok~，有了旋转的概念后，我们接着了解如何通过旋转来修复一棵失衡的二叉树。这里假设结点X是失衡点，它必须重新恢复平衡。由于任意结点的孩子结点最多有两个，而且导致失衡的必要条件是X结点的两棵子树高度差为2(大于1)，因此一般只有以下4种情况可能导致X点失去平衡：

① 在结点X的左孩子结点的左子树中插入元素 

② 在结点X的左孩子结点的右子树中插入元素 

③ 在结点X的右孩子结点的左子树中插入元素 

④ 在结点X的右孩子结点的右子树中插入元素 

　　以上4种情况，其中第①情况和第④情况是对称的，可以通过单旋转来解决。而第②种情况和第③情况是对称的，需要双旋转来解决。在分析这四种情况前，我们先看看AVL的结点该如何设计的，其声明如下：

~~~java
/**
 * Created by zejian on 2016/12/25.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 * 平衡二叉搜索树(AVL树)节点
 */
public class AVLNode<T extends Comparable> {

    public AVLNode<T> left;//左结点
    public AVLNode<T> right;//右结点
    public T data;
    public int height;//当前结点的高度

    public AVLNode(T data) {
        this(null,null,data);
    }
    public AVLNode(AVLNode<T> left, AVLNode<T> right, T data) {
        this(left,right,data,0);
    }
    public AVLNode(AVLNode<T> left, AVLNode<T> right, T data, int height) {
        this.left=left;
        this.right=right;
        this.data=data;
        this.height = height;
    }
}
~~~

　　可以看出，为了满足平衡二叉树的特性，需要在原来的二叉搜索树(BST)的结点中添加一个height的字段表示高度，方便我们计算。这里强调一下，高度和深度是一组相反的概念。高度是指当前结点到叶子结点的最长路径，如所有叶子结点的高度都为0；而深度则是指从根结点到当前结点的最大路径，如根结点的深度为0。这里约定空结点（空子树）的高度为-1，叶子结点的高度为0，非叶子节点的高度则根据其子树的高度而计算获取。如下图： 

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4dbbub0c3j20so0dl76q.jpg)



## 四、平衡二叉树的旋转算法与实现

### 1.左左单旋转(LL)：情景①分析

　　从下图可以看出，结点X并不能满足AVL树的性质，因为它的左子树比右子树深2层。这种情况就是典型的LL情景，此时需要通过右向旋转来修复失衡的树。

　　如下图1，X经过右旋转后变成图2，W变为根结点，X变为W的右子树；同时W的右子树变为X的左子树，树又重新回到平衡，各个结点的子树高度差都已在正常范围。一般情况下，我们把X结点称为失衡点。修复一棵被破坏的AVL树时，找到失衡点是很重要的，并把通过一次旋转即可修复平衡的操作叫做单旋转。

　　从图3和图4可知，在原始AVL树插入7结点后，结点9变为失衡点，树不再满足AVL性质，因此需要对9结点进行左左单旋转（即向右旋转）后，得到图4。我们发现此时并没有操作树的根结点(6)。实际上这是因为正常情况下，不必从树的根结点进行旋转，而是从插入结点处开始，向上遍历树，并更新和修复在这个路径上的每个结点的平衡及其平衡信息(高度)即可。

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4dgglt7h9j20rd0m4jws.jpg)

其代码实现如下，比较简单：

~~~java
//左左单旋转(LL旋转) W变为X的根结点, X变为W的右子树
private AVLNode<T> singleRotateLeft(AVLNode<T> x){
    AVLNode<T> w=x.left;//把w结点旋转为根结点
    x.left=w.right;	//同时w的右子树变为x的左子树
    w.right=x;		//x变为w的右子树
    //重新计算x/w的高度
    x.height=Math.max(height(x.left),height(x.right))+1;
    w.height=Math.max(height(w.left),x.height)+1;
    return w;//返回新的根结点
}
~~~

### 2.右右单旋转(RR)：情景④分析

　　接着再来看看右右单旋转(RR)的情景。如下图，可以发现与左左单旋转的情况恰好是一种镜像关系，同样结点X并不能满足AVL树的性质。在这样的情景下，需要对X结点进行左旋转来修复树的平衡，

　　如图1经左旋转后变了图2，此时X变为了根结点，W变为X的左孩子，X的左子树变为W的右子树，而树又重新恢复了平衡。

　　如图3和图4的实例情景，原始的AVL树在12处插入结点18后，结点10就变成了失衡点。因为10的左子树和右子树的高度相差2，显然不符合AVL树性质，需要对结点10进行右右单旋转修复(向左旋转)。然后得到图4，此时树重新回到了平衡，这便是右右单旋转(RR)的修复情景。

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4dhxz4o7cj20sq0oawkk.jpg)

~~~java
//右右单旋转(RR旋转) x变为w的根结点, w变为x的左子树
private AVLNode<T> singleRotateRight(AVLNode<T> w){
    AVLNode<T> x=w.right;
    w.right=x.left;
    x.left=w;
    //重新计算x/w的高度
    x.height=Math.max(height(x.left),w.height)+1;
    w.height=Math.max(height(w.left),height(w.right))+1;
    //返回新的根结点
    return x;
}
~~~

### 3.左右双旋转(LR)：情景②分析

　　前面两种情景都已分析完，它们都是基于单旋转的算法。但这种算法存在一个问题，那就是对情景②③无法生效，根本问题在于子树Y太深了。如下图所示：

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4dl1r0hkej20qj0dv41g.jpg)

　　显然经过一次单旋转的修复后无论是X或者W作为根结点都无法符合AVL树的性质。此时就需要用双旋转算法来实现了。由于子树Y是在插入某个结点后导致X结点的左右子树失去平衡，那么就说明子树Y肯定是非空的。因此为了易于理解，我们可以把子树Y看作一个根结点和两棵子树，如下图所示：

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4dl3kstl0j20ez0bi75b.jpg)

　　为了重新平衡，通过上述的分析显然不能把X作为根结点，而X与W间的旋转也解决不了问题。那唯一的旋转就是把Y作为新根。这样的话，X、W就不得不成为Y的孩子结点，其中W作为Y的左孩子结点，而X成为Y的右孩子结点。

　　这里我们以下图为例来分析。为了达到以上结果，需要W、Y进行单旋转（图1）。这里我们可把WY组成的子树看成前面的右右旋转情景，然后进行左向旋转，得到图2：W变为Y的左子树同时Y的左子树B变成W的右子树，其他不变到此第一次旋转完成。（右右单旋转，RR）

　　然后进行第二次旋转，以X结点向右进行旋转(同样可看作左左情景)，由图2得到图3：X变成Y的右孩子结点并且Y的右子树C变成X的左子树，第二次旋转完成，树也重新恢复到平衡。（左左单旋转，LL）

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4dl6hyem8j20sv0jwai4.jpg)

　　在左右双旋转实例图123中，在原AVL树种插入结点7后，结点8变成了失衡点，此时需要把6结点变为根结点才能重新恢复平衡。因此先进行左向旋转再进行右向旋转，最后树恢复平衡。算法代码实现如下：

~~~java
//左右旋转(LR旋转) x(根) w y 结点 把y变成根结点
private AVLNode<T> doubleRotateWithLeft(AVLNode<T> x){
    //w先进行RR旋转
    x.left=singleRotateRight(x.left);
    //再进行x的LL旋转
    return singleRotateLeft(x);
}
~~~

### 4.右左双旋转(RL)：情景③分析

　　对于右左双旋转(RL)情景和左右双旋转(LR)情景是一对镜像，旋转的原理上一样的。这里就不废话了，给出下图协助理解即可(已很清晰了)：

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4dlf7i2vaj20sj0jhaic.jpg)

~~~java
//右左旋转(RL旋转)
private AVLNode<T> doubleRotateWithRight(AVLNode<T> w){
    //先进行LL旋转
    w.right=singleRotateLeft(w.right);
    //再进行RR旋转
    return singleRotateRight(w);
}
~~~

## 五、平衡二叉树插入操作的实现

　　实际上，有了上述四种情况后，编写插入操作的编码细节并不会太困难。这里我们给出主要思路和代码实现，与BST（二叉查找树）的插入实现原理一样，使用递归算法，根据值大小查找到插入位置，然后进行插入操作。插入完成后，我们需要进行平衡判断。评估子树是否需要进行平衡修复，需要则利用上述的四种情景套入代码即可。最后要记得重新计算插入结点路径上的高度。代码实现如下：

~~~java
//插入方法
@Override
public void insert(T data) {
    if (data==null)
        throw new RuntimeException("data can\'t not be null ");
    this.root=insert(data,root);
}

private AVLNode<T> insert(T data , AVLNode<T> p){
    //说明已没有孩子结点,可以创建新结点插入了.
    if(p==null){
        p=new AVLNode<T>(data);
    }
    int result=data.compareTo(p.data);
    if(result<0){//向左子树寻找插入位置
        p.left=insert(data,p.left);

        //插入后计算子树的高度,等于2则需要重新恢复平衡,由于是左边插入,左子树的高度肯定大于等于右子树的高度
        if(height(p.left)-height(p.right)==2){
            //判断data是插入点的左孩子还是右孩子
            if(data.compareTo(p.left.data)<0){
                p=singleRotateLeft(p);//LL旋转
            }else {
                p=doubleRotateWithLeft(p); //进行左右旋转
            }
        }
    }
    if (result>0){//向右子树寻找插入位置
        p.right=insert(data,p.right);

        if(height(p.right)-height(p.left)==2){
            if (data.compareTo(p.right.data)<0){
                p=doubleRotateWithRight(p);//进行右左旋转
            }else {
                p=singleRotateRight(p);//RR旋转
            }
        }
    }
    //重新计算各个结点的高度
    p.height = Math.max( height( p.left ), height( p.right ) ) + 1;
    return p;//返回根结点
}
~~~

## 六、平衡二叉树删除操作的实现

　　关于平衡二叉树的删除，我们这里给出一种递归的实现方案。和二叉查找树中删除方法的实现类似，但是在移除结点后需要进行平衡检测，以便判断是否需要进行平衡修复。主要明白的是，这种实现方式在删除时效率并不高，不过我们并不打算过多讨论它，更复杂的删除操作过程将放在红黑树中进行讨论。下面给出实现代码：

~~~java
//删除方法
@Override
public void remove(T data) {
    if (data==null)
        throw new RuntimeException("data can\'t not be null ");
    this.root=remove(data,root);
}

//删除操作
private AVLNode<T> remove(T data,AVLNode<T> p){
    if(p ==null)
        return null;

    int result=data.compareTo(p.data);
    //从左子树查找需要删除的元素
    if(result<0){
        p.left=remove(data,p.left);
        //检测是否平衡
        if(height(p.right)-height(p.left)==2){
            AVLNode<T> currentNode=p.right;
            //判断需要那种旋转
            if(height(currentNode.left)>height(currentNode.right)){
                //RL
                p=doubleRotateWithRight(p);
            }else{
                //RR
                p=singleRotateRight(p);
            }
        }

    }
    //从右子树查找需要删除的元素
    else if(result>0){
        p.right=remove(data,p.right);
        //检测是否平衡
        if(height(p.left)-height(p.right)==2){
            AVLNode<T> currentNode=p.left;
            //判断需要那种旋转
            if(height(currentNode.right)>height(currentNode.left)){
                //LR
                p=doubleRotateWithLeft(p);
            }else{
                //LL
                p=singleRotateLeft(p);
            }
        }
    }
    //已找到需要删除的元素,并且要删除的结点拥有两个子节点
    else if(p.right!=null&&p.left!=null){

        //寻找替换结点
        p.data=findMin(p.right).data;

        //移除用于替换的结点
        p.right = remove( p.data, p.right );
    }else{
        //只有一个孩子结点或者只是叶子结点的情况
        p=(p.left!=null)? p.left:p.right;
    }
    //更新高度值
    if(p!=null)
        p.height = Math.max( height( p.left ), height( p.right ) ) + 1;

    return p;
}
~~~

## 七、平衡二叉树的最少结点数和最多结点数问题

　　关于最少结点数和最多结点数的问题，为了方便理解和简单化问题，这里我们假设AVL树的高度是h，N(h)表示高度为h的AVL树的结点数。对于求解高度为h的AVL树的最少结点数，则应该尽可能用最少结点数来填充该树，现在假设左子树填充到的高度为h-1，根据AVL树的特性，右子树的高度只能填充到h-2，因此高度为h的AVL树的最小结点数为：

> N(h) = N(h-1) + N(h-2) + 1

其中：

- N(h-1) 代表高度为h-1的左子树的最小结点数
- N(h-2) 代表高度为h-2的右子树的最小结点数
- 1 代表当前结点(根)。

　　求解上述递归公式可得（计算过程涉及线性代数的知识点，这里就不详细分析求解过程，毕竟我们主要求时间复杂度相关问题）其中n是AVL树的节点数：

> N(h)=O(1.618<sup>h</sup>)=>h=1.44logn≈O(logn)

　　通过上述的递推公式，我们也可以发现最少结点数的求解公式恰好符合斐波那契数列的规律（F(n)=F(n-1)+F(n-2)），因此求解最少结点数也就变得容易多了。

　　接着，我们采用同样的方法计算最大结点数。为了得到最大结点数，则左右子树的高必须相等，即都填充到h-1。由于结点都充满了，那么该树不仅是AVL树而且还是一个完全二叉树了，则会有如下公式：

> N(h) = N(h-1) + N(h-1) + 1 = 2N(h-1) + 1

最终求解该递归公式得：

> N(h)=O(2<sup>h</sup>)=>h=logn≈O(logn)

　　因此在两种情况下，AVL树的性质可以确保带有n个结点的AVL树的高度为O(logn)。这也意味着AVL树的操作在时间复杂度上近乎于O(logn)，也就不可能出现BST(二叉查找树)的最糟糕情况O(n)。 

[作者github源码下载（含文章列表）](https://github.com/shinezejian/javaStructures)

来源：[java数据结构与算法之平衡二叉树(AVL树)的设计与实现](https://blog.csdn.net/javazejian/article/details/53892797)

<center><font style="font-weight:bold">（完）</font></center>