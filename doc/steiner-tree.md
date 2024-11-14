斯坦纳树问题是组合优化问题，与最小生成树相似，是最短网络的一种。最小生成树是在给定的点集和边中寻求最短网络使所有点连通。而最小斯坦纳树允许在给定点外增加额外的点，使生成的最短网络开销最小。

## 问题引入

19 世纪初叶，柏林大学几何方面的著名学者斯坦纳，研究了一个非常简单却很有启示性的问题：将三个村庄用总长为极小的道路连接起来。从数学上说，就是在平面内给定三个点 $A$、$B$、$C$ 找出平面内第四个点 $P$，使得和数 $a+b+c$ 为最短，这里 $a$、$b$、$c$ 分别表示从 $P$ 到 $A$、$B$、$C$ 的距离。

问题的答案是：如果三角形 $\textit{ABC}$ 的每个内角都小于 $120^{\circ}$，那么 $P$ 就是使边 $\textit{AB}$、$\textit{BC}$、$\textit{AC}$ 对该点所张的角都是 $120^{\circ}$ 的点。如果三角形 $\textit{ABC}$ 的有一个角，例如 $C$ 角，大于或等于 $120^{\circ}$，那么点 $P$ 与顶点 $C$ 重合。

### 问题推广

1.  在斯坦纳问题中，给定了三个固定点 $A,B,C$。很自然地可以把这个问题推广到给定 $n$ 个点 $A_1,A_2,\dots,A_n$ 的情形；我们要求出平面内的点 $P$，使距离和 $a_1+a_2+\dots+a_n$ 为极小，其中 $a_i$ 是距离 $PA_i$。

2.  考虑到点的其他相关因素，加入了权重的表示。$n$ 个点的其他相关因素可以换算成一个权重表示，求出平面内的点 $P$，使距离与权重的乘积的总和 $a_1\cdot w_1+a_2\cdot w_2+\dots+a_n\cdot w_n$ 为极小，其中 $w_i$ 是每个点的权重。

3.  库朗（R.Courant）和罗宾斯（H.Robbins）提出第一个定义的推广是肤浅的。为了求得斯坦纳问题真正有价值的推广，必须放弃寻找一个单独的点 $P$，而代之以具有最短总长的＂道路网＂。数学上表述成：给定 $n$ 个点 $A_1,A_2,\cdots,A_n$，试求连接此 $n$ 个点，总长最短的直线段连接系统，并且任意两点都可由系统中的直线段组成的折线连接起来。他们将此新问题称为 **斯坦纳树问题**。在给定 $n$ 个点的情形，最多将有 $n-2$ 个复接点（斯坦纳点）。过每一斯坦纳点，至多有三条边通过。若为三条边，则它们两两交成 $120^{\circ}$ 角；若为两条边，则此斯坦纳点必为某一已给定的点，且此两条边交成的角必大于或等于 $120^{\circ}$。

连接三个以上的点的最短网络

![steiner-tree1](../docs/graph/images/steiner-tree-1.svg)

在第一种情形，解是由五条线段组成的，其中有两个斯坦纳点（红色 $s_1,s_2$），在那里有三条线段相交且相互间的交角为 $120^{\circ}$。第二种情形的解含有三个斯坦纳点。第三种情形，一个或几个斯坦纳点可能退化，或被一个或几个给定的点所代替。

我们将斯坦纳树的问题模型以图论形式呈现。

![steiner-tree2](../docs/graph/images/steiner-tree-2.svg)

对于形式一，如果令关键点为 $\{1,2,3,4\}$，可以发现若直接将这四个关键点相连的最小边权和是 12，显然这不是最优的。如果考虑使用 5 号节点那么最小边权和就会是 9，得到一个更优的答案。

对于形式二，如果令关键点为 $\{1,2,3,4\}$，可以发现这四个关键点中的一些点甚至没有直接相连的边，必须考虑使用复接点（斯坦纳点）。这时将 5 号考虑进去可以得到最小边权和 9。

并且我们可以发现在两张图中 1 号和 4 号的斯坦纳点是退化的，被 1 号或 4 号代替了。

## 例题

首先以一道模板题来带大家熟悉最小斯坦纳树问题。见 [【模板】最小斯坦纳树](https://www.luogu.com.cn/problem/P6192)。

题意已经很明确了，给定连通图 $G$ 中的 $n$ 个点与 $k$ 个关键点，连接 $k$ 个关键点，使得生成树的所有边的权值和最小。

结合上面的知识我们可以知道直接连接这 $k$ 个关键点生成的权值和不一定是最小的，或者这 $k$ 个关键点不会直接（相邻）连接。所以应当使用剩下的 $n-k$ 个点。

我们使用状态压缩动态规划来求解。用 $f(i,S)$ 表示以 $i$ 为根的一棵树，包含集合 $S$ 中所有点的最小边权值和。

考虑状态转移：

-   首先对连通的子集进行转移，$f(i,S)\leftarrow \min(f(i,S),f(i,T)+f(i,S-T))$。

-   在当前的子集连通状态下进行边的松弛操作，$f(i,S)\leftarrow \min(f(i,S),f(j,S)+w(j,i))$。在下面的代码中用一个 `tree[tot]` 来记录两个相连节点 $i,j$ 的相关信息。

??? note "参考实现"
    ```cpp
    --8<-- "docs/graph/code/steiner-tree/steiner-tree_1.cpp"
    ```

另外一道经典例题 [\[WC2008\] 游览计划](https://www.luogu.com.cn/problem/P4294)。

这道题是求点权和最小的斯坦纳树，用 $f(i,S)$ 表示以 $i$ 为根的一棵树，包含集合 $S$ 中所有点的最小点权值和。$a_i$ 表示点权。

考虑状态转移：

-   $f(i,S)\leftarrow \min(f(i,S),f(i,T)+f(i,S-T)-a_i)$。由于此处合并时同一个点 $a_i$，会被加两次，所以减去。

-   $f(i,S)\leftarrow \min(f(i,S),f(j,S)+w(j,i))$。

可以发现状态转移与上面的模板题是类似的，麻烦的是对答案的输出，在 DP 的过程中还要记录路径。

用 `pre[i][s]` 记录转移到 $i$ 为根，连通状态集合为 $s$ 时的点与集合的信息。在 DP 结束后从 `pre[root][S]` 出发，寻找与集合里的点相连的那些点并逐步分解集合 $S$，用 ans 数组来记录被使用的那些点，当集合分解完毕时搜索也就结束了。

??? note "参考实现"
    ```cpp
    --8<-- "docs/graph/code/steiner-tree/steiner-tree_2.cpp"
    ```

## 习题

-   [【模板】最小斯坦纳树](https://www.luogu.com.cn/problem/P6192)
-   [\[WC2008\] 游览计划](https://www.luogu.com.cn/problem/P4294)
-   [\[JLOI2015\] 管道连接](https://loj.ac/problem/2110)
-   [\[APIO2013\] 机器人](https://www.luogu.com.cn/problem/P3638)