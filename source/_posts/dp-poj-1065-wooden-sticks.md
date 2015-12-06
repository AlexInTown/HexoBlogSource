title: Wooden Sticks POJ 1065
date: 2015-11-05 21:53:12
tags: 
- LIS
- LDS
- greedy
- DP
categories:
- algorithm
- POJ
----
## 题目大意 
有一台机器要对n根木棍进行加工，每根木棍用(l, w)的元组来表示，l为木棍的长度，w为木棍的重量；机器有如下特性：
1. 每次设置的时间延迟为1分钟(开始时必须设置一次);
2. 两根木棍(l1, w1), (l2, w2)依次输送给机器，并且满足条件(l1<=l2，w1<=w2)时，对于(l2, w2)无需重新设置。
现给出n根木棍的(l, w)，求这组木棍通过该机器的最小设置时间。

### 输入
第一行为正整数T，表示测试数据个数 
每个测试数据第一行n，1 <= n <= 5000；
接下来第二行为2n个数，l1, w1, l2, w2 .... ln, wn

### 输出
对每个测试数据输出最小设置时间


## 初步分析 
要设置时间最小，等价于求满足特性2的棍子序列的最少个数。长度和重量有2个维度，可以先对序列进行排序，第一排序字段为长度，次要排序字段为重量。例：
> 原始序列: (4,9) (5,2) (2,1) (3,5) (1,4)
> 排序后为：(1,4) (2,1) (3,5) (4,9) (5,2)

这样排序能够保证长度l是非递减序列。此时，找出最小设置时间，转化为重量w的最小的非递减序列的个数。
> 重量序列：4, 1, 5, 9, 2

简单验证可得到，该例最小重量序列的个数为2，两个序列分别为(4, 5, 9) (1, 2)


## 贪心法 
对于上述重量序列，易想到贪心算法：
> Step 0. times = 0
> Step 1. 对于当前序列，求出最长递增子序列LIS
> Step 2. 从当前序列中剔除该LIS，times++
> Step 3. 直到序列为空

给定一组子序列，子序列被剔除的顺序与最终结果无关；并且每次剔除序列时，LIS能够保证不比其他方法差。
假设一次提取子序列没有使用最长非递减子序列，剩下的元素可能导致子序列的个数增加。因此贪心法能够得到正确的结果。

### 代码
``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <limits.h>

using namespace std;
int T, N;

vector<int> idx;
vector<int> vi; 
vector<int> parent;
struct Comp{
    bool operator()(const int &a, const int &b){
        return vi[a] < vi[b];
    }
};

int LIS(vector<int> & vi, vector<int> & parent, int &lastIdx){
    parent.resize(vi.size());
    idx.resize(vi.size());
    int res = 0;
    int i;
    for(i = 0; i < vi.size(); ++i){
        int j = upper_bound(idx.begin(), idx.begin()+ res, i,Comp())-idx.begin();
        if(j > 0) parent[i] = idx[j-1];
        else parent[i] = -1;
        idx[j] = i;
        if(j == res) res++;
    }
    if(res > 0) lastIdx = idx[res-1];
    return res;
}
void removeLIS(vector<int> &vi, vector<int> &parent, int lastIdx){
    while(lastIdx >=0){
        vi[lastIdx] = INT_MIN;
        lastIdx = parent[lastIdx];
    }
    int j = 0;
    for(int i = 0; i < vi.size(); ++i){
        if(vi[i] != INT_MIN)
            vi[j++] = vi[i];
    }
    vi.resize(j);
}

int main(){
    cin>>T;
    pair<int, int> pii;
    vector<pair<int, int> >  vpii;
    int i;
    for(int t = 0; t < T; ++t){
        cin>>N;
        vpii.clear();
        for(i = 0; i < N; ++i){
            cin>>pii.first>>pii.second;
            vpii.push_back(pii);
        }
        sort(vpii.begin(), vpii.end());
        vi.clear();
        for(i = 0; i < N; ++i){
            vi.push_back(vpii[i].second);
        }
        int lastIdx;
        int res = 0;
        while(vi.size()){
            LIS(vi, parent, lastIdx);
            removeLIS(vi, parent, lastIdx);
            res ++;
        }
        cout<<res<<endl;
    }
    return 0;
}
```

### 复杂度分析
排序时间复杂度为O(nlog(n))。
假设求k次LIS，每次平均O(n/k log(n/k))，上界O(nlog(n))。
每个元素最多被剔除一次，但需要拷贝后续元素，最差情况为递减序列，复杂度为O(n^2)。
最差时间复杂度为O(n^2)。

## 最长严格递减子序列
假设最小的非递减子序列个数为x，而最长严格递减子序列为L。会有L==x，下面来证明这一点。
### 证明
#### L < x 不成立
非递减子序列的个数最小为x，基于上述对贪心法的正确性分析，我们可以认为这些非递减子序列是由不断的LIS算法剔除得到的。
> 该序列组设为 s1, s2, .. ,si, .. sx
> 每个si末尾元素为 e1, e2, .. , ei, .. ex

不妨设e1 e2 e3, ... ex 的顺序，为排序后在重量序列中的顺序。明显有：
> e1 > e2 > .. >ei ... > ex

否则与LIS的定义矛盾。e1 到 ex 已经有x个元素符合严格递减子序列的定义，L >= x; L < x不成立。

#### L > x 不成立
由于递减序列L中的元素，不能出现在同一个非递减序列中。假设L大于x，那么必然有两个L中的元素出现在某个费递减序列中。矛盾，故L > x不成立。

因此L==x，直接求排序后重量序列中的严格递减序列的长度，即为x。

### 代码
``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <limits.h>

using namespace std;
int T, N;

vector<int> idx;
vector<int> vi; 
struct Comp{
    bool operator()(const int &a, const int &b){
        return vi[a] >= vi[b];
    }
};

int LIS(vector<int> & vi){
    idx.resize(vi.size());
    int res = 0;
    int i;
    for(i = 0; i < vi.size(); ++i){
        int j = upper_bound(idx.begin(), idx.begin()+ res, i,Comp())-idx.begin();
        idx[j] = i;
        if(j == res) res++;
    }
    return res;
}

int main(){
    cin>>T;
    pair<int, int> pii;
    vector<pair<int, int> >  vpii;
    int i;
    for(int t = 0; t < T; ++t){
        cin>>N;
        vpii.clear();
        for(i = 0; i < N; ++i){
            cin>>pii.first>>pii.second;
            vpii.push_back(pii);
        }
        sort(vpii.begin(), vpii.end());
        vi.clear();
        for(i = 0; i < N; ++i){
            vi.push_back(vpii[i].second);
        }
        int res = LIS(vi);
        cout<<res<<endl;
    }
    return 0;
}
```


### 复杂度分析
求LDS与LIS的时间复杂度一致，为O(nlog(n)).
