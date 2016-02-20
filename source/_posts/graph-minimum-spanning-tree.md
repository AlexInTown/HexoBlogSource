title: 基础图论-Prim, Kruskal算法证明及实现
date: 2016-02-19 15:35:00
tags: 
- 最小生成树
- Prim
- Kruskal
categories:
- algorithm
----
给定带权无向连通图G(V, E)，最小生成树指所有边权重之和最小的生成树。Prim算法和Kruskal算法分别采用不同的贪心策略，Prim算法适用于稠密图，Kruskal算法适用于稀疏图。

## Prim 算法
Prim 算法维护一个子图G'，对应的点集为V'，边集E'，表示生成树过程中已经包括的顶点和边；通过贪心地添加在V-V'中代价最小的顶点，同时更新剩下顶点的代价，最终生成目标树。

### Prim算法的证明
1. 初始情况下，G'仅有一个顶点，所以G'为一棵最小生成树，并且G'是最终的最小生成树T的子图。
2. 当前G'是T的子图，V-V'中代价最小的顶点为u，其代价为u到V'中最近的顶点v边权。
假设此次添加的不是点u，而在最终的T中，连通G'和 T-G'的边为(u',v')。那么将(u,v)添加到T中（形成环），并删去(u',v')能够得到更小的权重总和，这与T为最小生成树矛盾。

所以(u,v)必出现在T中，G'添加了顶点u和边(u,v)后，依然是T的子图。
综上所述，通过|V|-1次添加顶点后，生成的子图为最小生成树。

### Prim算法的C++实现
从一个例子来看Prim算法的实现，[poj 1258 Ari-Net](http://poj.org/problem?id=1258)。
题意很好理解：铺设光纤网络，让整个网络是连通的，并且使得光纤的总开销最小。基本上是对最小生成树的直接运用。
``` c++
#include <iostream>
#include <memory.h>
#include <stdio.h>
using namespace std;
const int MAXN = 101;
int g[MAXN][MAXN];    // 邻接矩阵
int N;
int min_cost[MAXN];   // 顶点的加入代价
bool visited[MAXN];
int prim(){
    int i,j;
    memset(visited, 0, sizeof(bool) * N);
    for(i = 1; i < N; ++i){
        min_cost[i] = 100000000;
    }
    int res = 0;
    int min_cost_val, min_cost_v;
    min_cost[0] = 0;
    while(1){
        min_cost_v = -1;
        min_cost_val = 1000000;
        for(j = 0; j < N; ++j){      // 寻找在V-V'中加入代价最小的顶点
            if(!visited[j] && min_cost_val > min_cost[j]){
                min_cost_val = min_cost[j];
                min_cost_v = j;
            }
        }
        if(min_cost_v == -1) break;  // 都已经加入，结束
        res += min_cost_val;         // 更新总代价
        visited[min_cost_v] = 1;     // 更新加入的标记
        for(j = 0; j < N; ++j){      // 更新剩下顶点的加入代价
            if(!visited[j] && min_cost[j] > g[min_cost_v][j]){
                min_cost[j] = g[min_cost_v][j];
            }
        }
    }
    return res;
}
int main(){
    int i,j;
    while(scanf("%d", &N) != EOF){
        for(i = 0; i < N; ++i){
            for(j = 0;j < N; ++j){
                scanf("%d", &g[i][j]);
            }
        }
        printf("%d\n", prim());
    }
    return 0;
}
```

### Prim算法复杂度分析
上述算法实现中，每次从V-V'中寻找最小代价的点，更新剩下顶点，时间复杂度为O(|V|)。添加顶点次数|V|-1，总的时间复杂度为O(|V|^2)。

若采用邻接表，对于更新剩下顶点的时间总开销，减小为O(|E|)，但寻找最小代价的点没有改进。

若采用邻接表，二叉堆作为优先队列，剩余顶点代价更新次数O(|E|)次，每次开销O(log|V|)；取最小代价点的总开销为O(|V|log|V|); 故时间复杂度为O(|E|log|V|+|V|log|V|)=O(|E|log|V|)。

若采用邻接表，斐波拉契数堆，剩余顶点代价更新总开销为O(|E|)，因为斐波拉契堆插入开销为O(1)；取最小代价点单次为O(log|V|)，总开销O(|V|log|V|)；故时间复杂度为O(|E|+|V|log|V|)。



## Kruskal算法
Kruskal 算法，先对所有边按边权排序(从小到大)，维护一个子图G'，对于每个边e(u, v)，若能保证加入后子图中没有环，则将该边加入子图。
对于连通子图中的顶点，可以使用并查集来标识。如果边e的顶点u，v在同一个集合中，加入e后必然会形成环路，反之则可以加入e。

### Kruskal算法的证明
与Prim算法类似，采用数学归纳法证明：
1. 初始边集X为空，所有顶点都各成一个连通子图，并且每个子图都是最后的最小生成树T的子图。
2. 假设当前每个连通子图都是T的子图，考虑连接各个子图之间的边，设最小的为e(u, v)。那么e(u, v)将出现在T中：因为若加入的是e'，连接u',v'，而e不被包含在T中，加入e后可以将e'删除，并且保持生成树的性质且总代价比T更小。加入了e后，各连通子图依旧是T的子图。

综上，每次加入边能保证每个子图都是最小生成树T的子图，通过|V|-1次加入操作就能生成最小生成树。

### Kruskal算法的C++实现
我们看另一个例子，[poj 2377 Bad Cowtractors](http://poj.org/problem?id=2377)。
题意：铺设网络，使得网络连通并开销最大，输出网络开销。将边的处理顺序变成从大到小即可。

``` c++
#include <iostream>
#include <algorithm>
#include <memory.h>
#include <stdio.h>
using namespace std;
struct Edge{
    int u, v, w;
    bool operator< (const Edge &b) const{
        return w > b.w;  // 自定义比较函数
    }
};
int arr[1000];
Edge edges[20000];
// 并查集操作，如果a,b在不同集合，合并集合，返回集合id；否则返回-1。
int unifind(int a, int b){
    int fa = arr[a];
    int fb = arr[b];
    while(fa>=0){
        a = fa;
        fa = arr[a];
    }
    while(fb>=0){
        b = fb;
        fb = arr[b];
    }
    if(a == b) return -1;   // 同一个集合
    int res;
    if(fa < fb){            // 按秩合并
        arr[a] += fb;
        arr[b] = a;
        res = a;
    }else{
        arr[b] += fa;
        arr[a] = b;
        res = b;
    }
    return res;
}
int N, M;
int kruskal(){
    sort(edges, edges+M);   // 排序
    int i = 0;
    int res = 0;
    for(i = 0; i < M; ++i){ // 从小到大处理加入边
        if(unifind(edges[i].u, edges[i].v) >= 0){  
            res += edges[i].w;
        }
    }
    int cnt = 0;
    for(i = 0; i < N; ++i){
        if(arr[i] < 0) cnt++;
    }
    if(cnt > 1) return -1;
    return res;
}
int main(){
    scanf("%d%d", &N, &M);
    int i;
    for(i = 0; i < M; ++i){
        Edge &e = edges[i];
        scanf("%d%d%d", &e.u, &e.v, &e.w);
        e.u--; e.v --;
    }
    memset(arr, -1, sizeof(int) *N);  // 初始化并查集
    printf("%d\n", kruskal());
    return 0;
}
```

### Kruskal算法复杂度分析
Kruskal算法实现起来直接简单，开销取决于排序操作O(|E|log|E|) = O(|E|log|V|)。图较为稀疏的时候，比Prim算法更有优势。

## Prim，Kruskal算法比较
对于稀疏图，首选Kruskal算法。因为基于邻接表和二叉堆，实际实现中，效率一般比不上直接用快排。而采用斐波拉契堆，理论上均摊效率虽高，但实现起来也较复杂。

对于稠密图，Prim算法没必要采用邻接表+堆，反而O(|V|^2)更为简单高效。

上述两种C++实现，分别对稠密图和稀疏图有较好的适用性。