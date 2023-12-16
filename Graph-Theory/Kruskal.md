## 写在前面

由于本人实力尚浅，接触算法没多久，写这篇blog仅仅是想要提升自己对算法的理解，如果各位读者发现什么错误，恳请指正，希望和大家一起进步。(●'◡'●)

## 题目

![skruskal题目](https://img-blog.csdnimg.cn/direct/f089ec1e8fb0493e863917fe7d69818e.png#pic_center)

## 思路

1. 用`p[]`数组记录每个顶点的祖宗（即每个顶点属于哪个集合），初始时，每个顶点的祖宗都是自己（即每个顶点自己是一个集合）。用`cnt`记录连通部分用多少条边。
2. 将每一条边按照边权的大小从小到大排序。
3. 按顺序取出每一条边，看是否与之前取出的边形成回路，也就是判断边的俩个顶点的祖宗是否相同（即两个顶点是否在同一个集合）。如果祖宗相同就会形成回路，那就不选这条边。如果如果祖宗不同，那就把其中一个顶点置为另一个顶点的祖宗，同时`cnt++`表示连通部分边数加一。
4. 遍历完所有的边，判断`cnt`和`n-1`的大小。如果`cnt<n-1`那就说明图不连通，否则图就连通。
   
   ## 代码

```cpp
#include<iostream>
#include<algorithm>

using namespace std;
const int N=1e5+10,M=2e5+10;
int p[N];       //用来实现并查集，记录每个顶点的祖宗顶点
int n,m;

struct edge     //定义边的结构体
{
    int a,b,w;
}e[M];

bool cmp(struct edge a,struct edge b)       //比较边权大小的函数
{
    return a.w<b.w;
}

int find(int u)     //寻找祖宗顶点，并且路径压缩
{
    if(u==p[u])
        return u;
    return p[u]=find(p[u]);
}


int Kruskal()
{
    sort(e,e+m,cmp);        //将边按照边权大小排序
    for(int i=1;i<=n;i++)       //对p[]数组初始化，一开始每个顶点的祖宗都是自己
        p[i]=i;
    int res=0;      //记录最后的答案，即最小生成树的所有边的边权
    int cnt=0;      //记录连通部分有多少边，方便最后判断图是否连通
    for(int i=0;i<m;i++)        //遍历每一条边
    {
        int a=e[i].a,b=e[i].b,w=e[i].w;
        a=find(a),b=find(b);        //找到边的两个顶点的祖宗（看俩个顶点是否属于一个集合）
        if(a!=b)        //如果祖宗不同，也就是不在一个集合
        {
            res+=w;
            cnt++;      //连通部分的边数加一
            p[a]=b;     //把a的祖宗置为b
        }
    }
    if(cnt<n-1)     //如果最后连通部分的边数小于n-1，就说明图不连通
        return 0x3f3f3f3f;
    else 
        return res;
}

int main()
{
    cin>>n>>m;
    for(int i=0;i<m;i++)
    {
        int a,b,c;
        cin>>a>>b>>c;
        e[i]={a,b,c};
    }

    int res=Kruskal();
    if(res==0x3f3f3f3f)
        cout<<"impossible"<<endl;
    else 
        cout<<res<<endl;
    return 0;
}
```

## 说明

- 这道题中用的到`并查集`会有很多应用，同时`find()`函数也非常优美，短短几行代码就可以找到祖宗，同时还能进行路径压缩。~~后面我看有时间我会专门出一篇blog讲解并查集，这还不赶紧关注博主一波。~~

```cpp
int find(int u)     //寻找祖宗顶点，并且路径压缩
{
    if(u==p[u])
        return u;
    return p[u]=find(p[u]);
}
```

- 学有余力的童鞋可以对比一下`kruskal`和`prim`算法（不了解`prim`?——>[传送门](https://github.com/yyy-warrior/Algorithm/blob/main/Graph-Theory/prim.md)），两个算法求解的是同一个问题。
  
  > 感谢各位童鞋看到这里，后面我会持续更新数据结构与算法，也希望大家点点赞，我们一起进步(❁´◡`❁)
