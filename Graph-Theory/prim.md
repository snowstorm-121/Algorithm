## 写在前面

由于本人实力尚浅，接触算法没多久，写这篇blog仅仅是想要提升自己对算法的理解，如果各位读者发现什么错误，恳请指正，希望和大家一起进步。(●'◡'●)

## 题目

![prim题目](https://img-blog.csdnimg.cn/direct/22e2a0798d1c469694625d21b792990d.png#pic_center)
prim 算法干的事情是：给定一个无向图，在图中选择若干条边把图的所有节点连起来。要求边长之和最小。在图论中，叫做求最小生成树。

## 思路

1. 用`st[]`数组表示顶点是否已经连通，0表示没有连通，1表示已经连通，初始时`st[]`各个顶点的值均为0。`dist[]`数组表示顶点距离连通部分的距离，初始时`dist[]`各个顶点为`INF`。用`sum`记录最后的结果，即最小生成树的边权之和。
2. 从源点（即1号点）开始扩充连通部分，很明显`d[1]=0`,遍历源点的边，更新与源点的相连的顶点距离连通部分的距离，同时将`st[1=1`表示源点已经在连通部分，`sum`加上`d[1]`。
3. 遍历`dist[]`数组，找到距离连通部分最近的顶点`v`。将`st[v]=1`表示`v`已经加入连通部分，遍历`v`的所有边，更新与`v`相连的顶点距离连通部分的距离，最后`sum`加上`d[v]`。而如果最后找出的顶点距离连通部分距离仍然是`INF`,那就说明这个图表示连通图，也就不存在最小生成树，直接跳出循环。
4. 循环步骤3 `n-1`次(如果是连通图的话）就可以找到最小生成树，`sum`即为最小生成树的所有边权之和。
   
   ## 代码

```cpp
#include<iostream>
#include<cstring>

using namespace std;
const int N=510,INF=0x3f3f3f3f;
int n,m;
int g[N][N],d[N],st[N];


void prim()
{
    int sum=0;
    memset(d,0x3f,sizeof d);        //初始化
    d[1]=0;
    for(int i=0;i<n;i++)        //循环n次
    {
        int t=0;
        for(int j=1;j<=n;j++)       //找出距离连通部分距离最小的顶点
            if(!st[j] and (d[j]<d[t] or !t))    
                t=j;
        st[t]=1;        //将该点加入连通部分
        sum+=d[t];      
        if(d[t]==INF)       //如果距离连通部分距离最小的顶点距离仍然是INF就说明图不连通，跳出循环。
        {
            cout<<"impossible"<<endl;
            return;
        }
        for(int j=1;j<=n;j++)       //遍历该点的所有的边，更新距离
            if(!st[j] and d[j]>g[t][j])
                d[j]=g[t][j];
    }
    cout<<sum<<endl;
}

int main()
{
    cin>>n>>m;
    memset(g,0x3f,sizeof g);
    while(m--)
    {
        int a,b,c;
        cin>>a>>b>>c;
        g[a][b]=g[b][a]=min(g[a][b],c);     //可能有重边，我们只存最小的边权
    }

    prim();
    return 0;
}
```

## 总结

- 其实现在回过头来看prim和Dijkstra（不明白Dijksta？——>[传送门](https://github.com/yyy-warrior/Algorithm/blob/main/Graph-Theory/Dijkstra.md)）这两个算法挺像的，思路可以说一模一样，都先选一个距离最短的点，只不过`Dijksra`是离源点，另`prim`离已知顶点集合，然后都是根据这个点来更新与它相连的点的距离。    
  
  ## 拓展
  
  聪明的你肯定发现既然`prim`和`Dijkstra`这么像，那么`prim`是不是也可以像`Dijkstra`一样有优先队列（小根堆）来优化一下。没错，确实可以这样。

```cpp
#include<iostream>
#include<cstring>
#include<algorithm>
#include<queue>

using namespace std;

typedef pair<int,int> PII;

const int N=510,INF=0x3f3f3f3f;
int g[N][N],d[N];
int n,m;
bool st[N];

int prim()
{   
    int ret=0,cnt=0;
    memset(d,0x3f,sizeof d);        //初始化
    priority_queue<PII,vector<PII>,greater<PII>> q;        //小根堆（优先队列）
    q.push({0,1});
    while(q.size())
    {
        auto t=q.top();        //取出距离连通部分最近的顶点
        q.pop();
        int dis=t.first, poi=t.second;        //dis表示距离，poi表示顶点编号
        if(st[poi]) continue;        //如果顶点已经在连通部分就跳过本次循环
        st[poi]=true;
        ret+=dis,cnt++;
        for(int j=1;j<=n;j++)        //遍历该点的所有的边，更新距离
        {
            if(d[j]>g[poi][j])
            {
                d[j]=g[poi][j];
                q.push({d[j],j});
            }
        }
    }
    if(cnt!=n) return INF;
    else return ret;
}



int main()
{
    cin>>n>>m;

    memset(g,0x3f,sizeof g);
    for(int i=1;i<=n;i++) g[i][i]=0;

    while(m--)
    {
        int a,b,c;
        scanf("%d%d%d",&a,&b,&c);
        g[a][b]=g[b][a]=min(g[a][b],c);
    }

    int ret=prim();
    if(ret==INF) puts("impossible");
    else printf("%d\n",ret);

    return 0;
}
```

~~经过我的实际测试好像运行时间上并没有变快多少，这算是没什么卵用的优化🤣~~ 

> 感谢各位童鞋看到这里，后面我会持续更新数据结构与算法，也希望大家点点赞，我们一起进步(❁´◡`❁)
