## 写在前面

---

***如果没看过我前面关于[01背包问题（良心正解）](https://blog.csdn.net/yourgrandfather_/article/details/135103012)和[完全背包问题（良心正解）](https://blog.csdn.net/yourgrandfather_/article/details/135111459)[动态规划——多重背包问题(保姆级教学）](https://blog.csdn.net/yourgrandfather_/article/details/135125267)[动态规划——分组背包问题（不看后悔系列）](https://blog.csdn.net/yourgrandfather_/article/details/135134277)的宝宝可以先去看看，可以让你对动态规划的理解更透彻***

## DP核心思路

![核心](https://img-blog.csdnimg.cn/img_convert/b77ffbb614f434935ed323b4074a8a01.png#pic_center#pic_center)

---

## LIS（最长上升子序列）

### 题目

![LIS](https://img-blog.csdnimg.cn/direct/b82bf173a5004ba39fefc5a860a66ae5.png#pic_center)

### 思路

> **重要变量说明**
> `a[i]`：存的第`i`个数
> `f[i]`：表示以`a[i]`结尾的最长上升子序列

1. 对于每一个数字`a[i]`，我们都有`i-1`选择：
   - 和第一个数`a[1]`比较，如果`a[i]>a[1]`，那么`a[i]`可以加在以`a[1]`为结尾的最长上升子序列中
   - 和第二个数`a[2]`比较，如果`a[i]>a[2]`，那么`a[i]`可以加在以`a[2]`为结尾的最长上升子序列中
   - `......`
   - 和第`i-1`个数`a[i-1]`比较，如果`a[i]>a[i-1]`，那么`a[i]`可以加在以`a[i-1]`为结尾的最长上升子序列中
2. 因为我们是要求最长的上升子序列，所以我们每次都只保留最大的`f[i]`
3. 循环完，我们要求整个序列的最长上升子序列，所以我们要遍历`f[]`，找到最大值。
   
   ### 代码

```cpp
#include<iostream>

using namespace std;
const int N=1010;
int a[N],f[N];

int main()
{
    int n;
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        f[i]=1;     //这属于初始化，因为对于每一个数字，自己单独一定是一个上升子序列
        cin>>a[i]; 
    }
    for(int i=1;i<=n;i++)       //i表示枚举到以a[i]结尾的最长上升子序列
        for(int j=1;j<i;j++)        //j表示从数组中下标小于i的数枚举，看能不能加到a[i]加到以a[j]为结尾的上升子序列后面
            if(a[i]>a[j])       //如果满足要求，那就表明a[i]可以加到以j结尾的最长上升子序列
                f[i]=max(f[i],f[j]+1);      //对于每一种可能取最大值
    int res=0;
    for(int i=1;i<=n;i++)       //求最大值
        res=max(res,f[i]);
    cout<<res<<endl;
    return 0;
}
```

---

### 优化

**我们发现这种方法的时间复杂度是$O(n^2)$，这让人有点难以接受，于是我们想能不能优化一下算法**

#### 思路

1. 我们构造一个数组`f[i]`，表示长度为`i`的上升子序列的末尾的最小的元素（至于为什么会有这种的奇诡的定义，我在后面会解释的）
2. 我们遍历数数组，把`a[i]`与`f[cnt]`(`cnt`表示`f[]`数组最后一个元素的下标），就有两种情况：
   - `a[i]>f[cnt]`,我们就把`a[i]`加到`f[cnt]`后面
   - `a[i]<=f[cnt]`，我们就用二分查找去查找`f[]`中第一个大于等于`a[i]`的下标，然后用`a[i]`去替代这个元素
3. 遍历完整个数组，`f[]`的长度就是最长上升子序列（但注意`f[]`存可能不是最长上升子序列，只能说`f[]`的长度和整个数组的最长上升子序列长度一样）
   
   #### 代码

```cpp
#include<iostream>

using namespace std;
const int N=100010;
int f[N],a[N];

int bsearch(int l,int r,int x)
{
    while(l+1!=r)
    {
        int mid=(l+r)/2;        
        if(f[mid]>=x)
            r=mid;
        else
            l=mid;
    }
    return r;
}

int main()
{
    int n,cnt=1;
    scanf("%d",&n);
    for(int i=1;i<=n;i++)
        scanf("%d",&a[i]);
    f[cnt]=a[1];
    for(int i=2;i<=n;i++)
    {
        if(a[i]>f[cnt])
            f[++cnt]=a[i];
        else 
        {
            int k=bsearch(0,cnt+1,a[i]);
            f[k]=a[i];
        }
    }
    cout<<cnt<<endl;
}   
```

---

#### 优化说明

- `f[]`的长度就是最长上升子序列（但注意`f[]`存可能不是最长上升子序列，只能说`f[]`的长度和整个数组的最长上升子序列长度一样），这算是这个方法的缺陷了。
- 关于这个做法的原理是基于**贪心**的，我们要求的是最长的上升子序列，当然不愿意越算子序列长度反而越小了。因此，如果我们目前算出的结果还没以前的长，会暂时保留以前的结果，当然也不丢弃目前的结果，因为之后继续计算的话，目前的结果可能更优。为了实现上述目的，我们可以用新序列从左到右逐渐覆盖掉旧序列。当新序列长度`<`原序列长度时，原序列没有被完全覆盖，因此保证长度不减小；当新序列长度` ≥` 原序列长度时，原序列已经被完全覆盖，现在就是以新序列为基础进行计算了。(还是不理解的童鞋可以自己举例子理解试试）

---

## 写在最后

> 如果你觉得我写题解还不错的，请各位**王子公主**移步到我的其他题解看看
> **数据结构与算法部分（还在更新中）：**
> [***C++ STL总结 - 基于算法竞赛（强力推荐***）](https://blog.csdn.net/yourgrandfather_/article/details/135051716?spm=1001.2014.3001.5501)
> [动态规划——01背包问题](https://blog.csdn.net/yourgrandfather_/article/details/135103012?spm=1001.2014.3001.5501)
> [动态规划——完全背包问题](https://blog.csdn.net/yourgrandfather_/article/details/135111459)
> [动态规划——多重背包问题](https://blog.csdn.net/yourgrandfather_/article/details/135125267)
> [动态规划——分组背包问题](https://blog.csdn.net/yourgrandfather_/article/details/135134277)
> [最短路算法——Dijkstra（C++实现）](https://blog.csdn.net/yourgrandfather_/article/details/134869064?spm=1001.2014.3001.5501)
> [最短路算法———Bellman_Ford算法（C++实现）](https://blog.csdn.net/yourgrandfather_/article/details/134935786?spm=1001.2014.3001.5501)
> [最短路算法———SPFA算法（C++实现）](https://blog.csdn.net/yourgrandfather_/article/details/135004393?spm=1001.2014.3001.5501)
> [最小生成树算法———prim算法（C++实现）](https://blog.csdn.net/yourgrandfather_/article/details/135026901?spm=1001.2014.3001.5501)
>     [最小生成树算法———Kruskal算法（C++实现）](https://blog.csdn.net/yourgrandfather_/article/details/135039904?spm=1001.2014.3001.5501)
> [染色法判断二分图（C++实现）](https://blog.csdn.net/yourgrandfather_/article/details/135094296?spm=1001.2014.3001.5501)
> **Linux部分（还在更新中）：**
> [Linux学习之初识Linux](https://blog.csdn.net/yourgrandfather_/article/details/134953315?spm=1001.2014.3001.5501)
> [Linux学习之命令行基础操作](https://blog.csdn.net/yourgrandfather_/article/details/134956923?spm=1001.2014.3001.5501)

## ✨🎉总结

“种一颗树最好的是十年前,其次就是现在”
所以,
“让我们一起努力吧,去奔赴更高更远的山海”
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7bb6bf71a1814bd88fb9615df5cc258d.png#pic_center =400x250)
如果有错误❌,欢迎指正哟😋

🎉如果觉得收获满满,可以动动小手,点点赞👍,支持一下哟🎉
