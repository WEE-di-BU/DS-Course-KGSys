author: sshwy, zhouyuyang2002, StudyingFather, Ir1d, ouuan, Enter-tainer

## 引入

笛卡尔树是一种二叉树，每一个节点由一个键值二元组 $(k,w)$ 构成。要求 $k$ 满足二叉搜索树的性质，而 $w$ 满足堆的性质。如果笛卡尔树的 $k,w$ 键值确定，且 $k$ 互不相同，$w$ 也互不相同，那么这棵笛卡尔树的结构是唯一的。如下图：

![eg](../docs/ds/images/cartesian-tree1.png)

（图源自维基百科）

上面这棵笛卡尔树相当于把数组元素值当作键值 $w$，而把数组下标当作键值 $k$。可以发现，这棵树的键值 $k$ 满足二叉搜索树的性质，而键值 $w$ 满足小根堆的性质。同时根据二叉搜索树的性质，可以发现这种特殊的笛卡尔树满足一棵子树内的下标是一个连续区间。

竞赛中使用笛卡尔树时，常用数组下标作为二元组的键值 $k$。

## 用栈构建笛卡尔树

### 过程

我们考虑将元素按下标顺序依次插入到当前的笛卡尔树中。那么每次我们插入的元素必然在这棵树的右链（右链：即从根节点一直往右子树走，经过的节点形成的链）的末端。于是我们执行这样一个过程，从下往上比较右链节点与当前节点 $u$ 的 $w$，如果找到了一个右链上的节点 $x$ 满足 $w_x<w_u$，就把 $u$ 接到 $x$ 的右儿子上，而 $x$ 原本的右子树就变成 $u$ 的左子树。

图中红框部分就是我们始终维护的右链：

![build](../docs/ds/images/cartesian-tree2.png)

显然每个数最多进出右链一次（或者说每个点在右链中存在的是一段连续的时间）。这个过程可以用栈维护，栈中维护当前笛卡尔树的右链上的节点。一个点不在右链上了就把它弹掉。这样每个点最多进出一次，复杂度 $O(n)$。

???+ note "笛卡尔树与 Treap"
    实际上，Treap 是笛卡尔树的一种，只不过 Treap 中 $w$ 的值完全随机。Treap 有线性的构建算法，如果提前将键值 $k$ 排好序，是可以使用上述单调栈算法完成构建过程的，只不过很少会这么用。

### C++ 实现

```cpp
// stk 维护笛卡尔树中节点对应到序列中的下标
for (int i = 1; i <= n; i++) {
  int k = top;  // top 表示操作前的栈顶，k 表示当前栈顶
  while (k > 0 && w[stk[k]] > w[i]) k--;  // 维护右链上的节点
  if (k) rs[stk[k]] = i;  // 栈顶元素.右儿子 := 当前元素
  if (k < top) ls[i] = stk[k + 1];  // 当前元素.左儿子 := 上一个被弹出的元素
  stk[++k] = i;                     // 当前元素入栈
  top = k;
}
```

## 例题

???+ note "[HDU 1506. Largest Rectangle in a Histogram](https://acm.hdu.edu.cn/showproblem.php?pid=1506)"
    $n$ 个位置，每个位置上的高度是 $h_i$，求最大子矩形。如下图：
    
    ![eg](./images/cartesian-tree3.png)
    
    阴影部分就是图中的最大子矩阵。

??? note "解题思路"
    具体地，我们把下标作为键值 $k$，$h_i$ 作为键值 $w$ 满足小根堆性质，构建一棵 $(i,h_i)$ 的笛卡尔树。
    
    这样我们枚举每个节点 $u$，把 $w_u$（即节点 $u$ 的高度 $h$）作为最大子矩阵的高度。由于我们建立的笛卡尔树满足小根堆性质，因此 $u$ 的子树内的节点的高度都大于等于 $u$。而我们又知道 $u$ 子树内的下标是一段连续的区间。于是我们只需要知道子树的大小，然后就可以算这个区间的最大子矩阵的面积了。用每一个点计算出来的值更新答案即可。显然这个可以一次 DFS 完成，因此复杂度是 $O(n)$ 的。

??? note "参考实现"
    ```cpp
    --8<-- "docs/ds/code/cartesian-tree/cartesian-tree_1.cpp"
    ```

## 参考资料

[笛卡尔树 - 维基百科](https://zh.wikipedia.org/wiki/%E7%AC%9B%E5%8D%A1%E5%B0%94%E6%A0%91)
