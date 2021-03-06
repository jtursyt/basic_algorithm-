## 图

### 相关概念



### 图的存储

#### 直接存边

使用数组存储各边的起点、终点和权值。

一般用于kruskal算法中对边权进行排序。

#### 邻接矩阵

使用一个二维数组来存边，m\[u][v]存储u到v的边的权值。

适用于没有重边的情况，适用于稠密图（边的数量接近点数量的平方），查询速度快。

#### 邻接表

使用一个支持动态增加元素的数据结构构成的数组，m[u]存储点u所有出边的相关信息。

适用于边数较少的稀疏图（边数约等于点数）。

使用链表实现的邻接表，称为链式前向星。

## 最短路

### 单源最短路



## 欧拉回路

### 定义

如果图G中的一个路径包括每个边恰好一次，则该路径称为**欧拉路径**。如果一个回路是欧拉路径，则称为欧拉回路。具有欧拉回路的图称为欧拉图，具有欧拉路径但不具有欧拉回路的图称为半欧拉图。

一个无向图存在欧拉回路，当且仅当该图所有**顶点度数都为偶数**，且该图是连通图。一个有向图存在欧拉回路，当且仅当所有**顶点的入度等于出度**，且该图是连通图。

一个图是半欧拉图的充分必要条件是其连通且仅有两个奇度点。

### Hierholzer算法

从某节点u开始（半欧拉图应为起点），任意的经过还未经过的边，直到无路可走，此时回到了该节点u（半欧拉图为到达终点v），得到了一条从u-->u的回路。接下来找到回路上具有出边的节点w，从w开始找到一条w-->w的回路嵌入原回路中，直到所有的边都被处理。通过递归操作，实际上是逆序确定回路中的边。

### 题目

#### 322. 重新安排行程

#### 753. 破解保险箱

* 转化为求欧拉回路。当前节点为a1a2...an-1，那么其连接的下一个节点为输入一个字符后的结果，如a2...an-1x。当前节点加上其出边的编号0~n即构成了一组密码，由于这个图的每个节点都有k条入边和出边，因此它一定存在一个欧拉回路，即可以从任意一个节点开始，一次性不重复地**走完所有的边**且回到该节点。

```python
class Solution:
    def crackSafe(self, n: int, k: int) -> str:
        def dfs(node):
            for x in range(k):
                nxt = node*10+x
                if nxt not in visited:
                    visited.add(nxt)
                    dfs(nxt%lims)
                    res.append(str(x))
        
        lims = 10**(n-1)
        visited = set()
        res = []
        dfs(0)
        return ''.join(res)+'0'*(n-1)
```

#### 2097. 合法重新排列数对

#### 
