# 二叉树的遍历
`序`指的是`根节点在遍历中的顺序`：先序指先遍历根节点。  
前中后序遍历的`路径是一模一样的`。
* 先序遍历
  1. 先访问`根节点`
  2. 先序遍历`左子树`
  3. 先序遍历`右子树`
  
  ```C
  void PreOrderTraversal(BinTree BT)
  {
    if(BT)
    {
      printf("%d",BT->Data);
      PreOrderTraversal(BT->Left);
      PreOrderTraversal(BT->Right);
    }
  }
  ```
* 中序遍历
  1. 先中序遍历`左子树`
  2. 访问`根节点`
  3. 中序遍历`右子树`
  ```C
  void InOrderTraversal(BinTree BT)
  {
    if(BT)
    {
      InOrderTraversal(BT->Left);
      printf("%d",BT->Data);
      InOrderTraversal(BT->Right);
    }
  }
  ```
  非递归遍历算法：使用`堆栈`
    1. 遇到节点就压栈，并遍历其左子树
    2. 当左子树遍历结束后，从栈顶弹出这个节点并访问
    3.然后按其有指针再去中序遍历该节点的右子树
  ```C
  void InOrderTraversal(BinTree BT)
  {
    BinTree T = BT
      Stack S = CteatStack(MxSize);
    while (T || !IsEmpty(S))
    {
      while (T)
      {
        Push(S, T);
        T = T->Left;
      }
      if (!IsEmpty(S))
      {
        T = Pop(S);
        printf("%d",T->Data);
        T = T->Right;
      }
    }
  }
  ```  
  
* 后序遍历
  1. 先后序遍历`左子树`
  2. 后序遍历`右子树`
  3. 访问`根节点`
  ```C
  void PostOrderTraversal(BinTree BT)
  {
    if(BT)
    {
      MidOrderTraversal(BT->Left);
      MidOrderTraversal(BT->Right);
      printf("%d",BT->Data);
    }
  }
  ```
* 层序遍历
二叉树遍历的核心问题：二维机构的`线性化`
  * 从节点访问其左、右儿子节点
  * 访问做儿子后，右儿子节点怎么办
    * 需要一个存储结构保存暂时不访问的节点
    * 存储结构：堆栈、队列
队列实现：遍历从根节点开始，首先将根节点入队，然后开始执行循环：节点出队，访问该节点、其左右儿子入队
  ```C
  void LevelOrderTraversal(BinTree BT)
  {
    Queue Q;
    BinTree T;
    Q=CreatQueue(MaxSize);
    Enqueue(Q,BT);
    while(!IsEmpty(Q))
    {
      T=Dequeue(Q);
      printf("%d",T->Data);
      if(T->Left)
      {
        Enqueue(Q,T->Left);
      }
      if(T->Right)
      {
        Enqueue(Q,T->Right);
      }
    }
  }
  ```
# 二叉搜索树
查找问题：
1. `静态查找`与`动态查找`（带插入删除）
2. 针对动态查找，数据如何组织？
二叉搜索树：一棵二叉树，满足以下性质：
1. 非空左子树的所有键值小于其根节点的键值。
2. 非空右子树的所有键值小于其根节点的键值。
3. 左、右子树都是二叉搜索树。
操作：
1. 查找：从BST中查找元素X，返回其在节点的地址
2. 找最小（大）值：从BST中查找并返回最小元素所在的节点地址
3. 插入
4. 删除
  * 查找操作：若BST非空，则根节点关键字和X进行比较：
  1. 若X小于根节点的键值，只需在左子树中继续搜索。
  2. X大于根节点的键值，在右子树中进行继续搜索
  3. 若两者相等，搜索完成，返回指向节点的指针
  
  ``` C
  Position Find(ElementType X，BinTree BST)
  {
    if(!BST)
    {
      return NULL;
    }
    if(X>BST->Data)
    {
      return Find(X,BST->Right);  //尾递归
    }
    else if (X<BST->Data)
    {
      return Find(X,BST->Left);   //尾递归
    }
    else
    {
      return BST;
    }
  }
  ```
>尾递归会影响执行的效率，一般都可使用循环的方式去除。
  ``` C
  Position Find(ElementType X，BinTree BST)
  {
    while(BST)
    {
      if(X>BST->Data)
      {
        BST=BST->Right;
      }
      else if (X<BST->Data)
      {
        BST=BST->Left;
      }
      else
      {
        return BST;
      }
    }
    return NULL;
  }
  ```
>查找的效率取决于树的`高度`
  * 找最大值、最小值：最大值位于树的最右侧，最小值位于树的最左侧
  
  * 二叉搜索树的插入
  找到元素应该插入的位置，可以采用与Find类似的方法
  ``` C
  BinTree Insert(ElementType X，BinTree BST)
  {
    if(!BST)//迭代结束条件
    {//BST是空树，即找到了带插入位置
      BST=malloc(sizeof(struct TreeNode));  //构造新节点
      BST->Data=X;
      BST->Left=NULL;
      BST->Right=NULL;
    }
    else//迭代内容
    {
      if(X>BST->Data)
      {
        BST->Right=Insert(X,BST->Right);
      }
      else if(X<BST->Data)
      {
        BST->Left=Insert(X,BST->Left);
      }
    }
    return BST;
  }
  ```
  * 二叉搜索树的删除
  三种情况：
  1. 待删除的是叶节点：直接删除并修改其父节点指针为NULL
  2. 要删除的节点只有一个子节点：将其父节点的指针指向该节点的子节点
  3. 要删除的节点左右节点都是子树：用另一节点替代被删除的节点：`右子树的最小元素`或`左子树的最大元素`。
  ``` C
  BinTree Delete(ElementType X，BinTree BST)
  {
    if(!BST)
    {
      printf("未找到")；
    }
    if（X>BST->Data）
    {
      BST->Right=Delete(X,BST->Right);
    }
    else if(X<BST->Data)
    {
      BST->Left=Delete(X,BST->Right);
    }
    else
    {
      if(BST->Left&&BST->Right)
      {
        Tmp=FindMin(BST->Right);
        BST->Data=Tmp->Data;
        BST->Right=Delete(Tmp->Data,BST->Right);
        free(Tmp);
      }
      else
      {
        Tmp=BST;
        if(!BST->Left)
        {
          BST=BST->Right;
        }
        else if(!BST->Right)
        {
          BST=BST->Left;
        }
        free(Tmp);
      }
    }
    return BST;
  }
  ```
# 平衡二叉树
搜索树节点不同的`插入次序`，将导致不同`深度`和`平均查找长度ASL`
`平衡因子BF`：对于节点来说，BT(T)=hL-hR,`hL`、`hR`分别为T的左子树和右子树的高度
平衡二叉树（AVL）:空树或任一节点的左、右子树的高度差的绝对值不超过1，即`|BT(T)|<=1`
  * 平衡二叉树的调整
    * 右单旋RR：
  ![RR单旋](https://github.com/fake960324/DataStructure-Algorithm/blob/master/Pics/RR%E6%97%8B%E8%BD%AC.png)
    “发现者”：指平衡条件被破坏的最下面的节点。
    “破坏者”：造成平衡条件破坏的节点。
    RR：破坏者在发现者的`右子树的右子树`（Right-Right）上。
    * 左单旋LL：
    ![LL单旋](https://github.com/fake960324/DataStructure-Algorithm/blob/master/Pics/LL%E6%97%8B%E8%BD%AC.png)
    * LR旋转：
    LR：破坏者在发现者的`左子树的右子树`上
    ![LL单旋](https://github.com/fake960324/DataStructure-Algorithm/blob/master/Pics/LR%E6%97%8B%E8%BD%AC.png)
    * RL旋转
    RL：破坏者在发现者的`右子树的左子树`上
    ![LL单旋](https://github.com/fake960324/DataStructure-Algorithm/blob/master/Pics/RL%E6%97%8B%E8%BD%AC.png)
