​

# 写在前面：

由于本人实力尚浅，接触算法没多久，写这篇blog仅仅是想要提升自己对算法的理解，如果各位读者发现什么错误，恳请指正，希望和大家一起进步。

# 题目：

![](https://img-blog.csdnimg.cn/direct/5eddcd61731c4018adf8d4f67af943c0.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

```cpp
const int N=510;    

int f[N][N];    //用邻接矩阵来存储图，最开始要初始化为INF(0x3f3f3f3f)
int dist[N];    //用该数组来存储图上顶点到源点的距离,最开始也要初始化为INF
int st[N];      //st是state的缩写，用来表示该顶点是否已经被确定
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

# 思路：

1. 在未被确定的顶点中选出距离源点最近的顶点v，同时将顶点v状态置为已经确定最短路。
2. 遍历顶点v的所有边，更新与v相连的点到源点的距离。
3. 循环上面两步，一直迭代n次（因为每迭代一次就会确定一个点，一共有n个点，所以是n次）

# 代码：

```cpp
#include<iostream>
#include<algorithm>
#include<cstring>

using namespace std;
const int N=510;
int n,m,f[N][N],dist[N],st[N];

void dijkstra()
{
    dist[1]=0;      //将源点到源点的距离置为0
    for(int i=0;i<n;i++)        //由于每次确定一个顶点的最短距离，所以迭代n次
    {
        int t=0;        //t表示当前访问的顶点
        for(int j=1;j<=n;j++)       //经过t与每一个未被确定的顶点比较，选出距离源点最近的顶点
            if(!st[j] and (dist[j]<dist[t] or !t))
                t=j;
        for(int j=1;j<=n;j++)       //遍历顶点t所有边，更新其他点到源点的距离
            dist[j]=min(dist[j],dist[t]+f[t][j]);
        st[t]=1;        //将t状态置为已经确定
    }
}

int main()
{
    cin>>n>>m;
    memset(f,0x3f,sizeof f);        //初始化f，dist数组
    memset(dist,0x3f,sizeof dist);
    while(m--)      //读入每一条边，建图
    {
        int a,b,c;
        cin>>a>>b>>c;
        f[a][b]=min(f[a][b],c);
    }

    dijkstra();
    if(dist[n]==0x3f3f3f3f)     //如果n到源点的距离为INF，那就说明这个图不是连通图
        cout<<-1<<endl;
    else
        cout<<dist[n]<<endl;
    return 0;
}
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

# 总结：

-  Dijkstra适合用来求解单源汇非负权图的最短路问题，算法的思路其实非常清晰，代码也比较好写。
- 算法的时间复杂度是O(n^2)
- Dijkstra的证明过程在这里我就不写了~~（因为我自己到现在也没怎么搞明白）~~，各位读者可以自行Google。 

# 拓展：

还有一种堆优化的Dijkstra算法，其实就是用小根堆（优先队列）来维护代更新的顶点的顺序，保证每次迭代中，不需要在去遍历所有顶点来找到最近的顶点。时间复杂度是O(mlogn),m是边数，n是顶点数。

```cpp
#include<iostream>
#include<queue>
#include<cstring>

using namespace std;
typedef pair<int,int> PII;
const int N=150010;
int n,m;
int h[N],e[N],ne[N],w[N],st[N],d[N],idx;

void add(int a,int b,int c)     //邻接表添加一条边
{
    e[idx]=b,ne[idx]=h[a],w[idx]=c,h[a]=idx++;
}

void dijkstra()
{
    memset(d,0x3f,sizeof d);
    d[1]=0;
    priority_queue<PII,vector<PII>,greater<PII>> heap;      //构建小根堆
    heap.push({0,1});       //将源点压入堆
    while(!heap.empty())        //只要堆不为空，就一直迭代
    {
        auto t=heap.top();      //取出堆顶
        heap.pop();
        int dist=t.first,pos=t.second;      //dist表示当前顶点距离远点的距离，pos表示当前顶点的编号
        if(st[pos])         //如果该顶点已经被确定，就跳过本次循环
            continue;
        for(int i=h[pos];i!=-1;i=ne[i])     //遍历所有与pos相连的边
        {
            int j=e[i];
            if(d[j]>dist+w[i])      //如果可以更新，就更新并且将该顶点压入堆中
            {
                d[j]=min(d[j],dist+w[i]);
                heap.push({d[j],j});
            }
        }
        st[pos]=1;
    }
}

int main()
{
    cin>>n>>m;
    memset(h,-1,sizeof h);
    while(m--)
    {
        int a,b,c;
        cin>>a>>b>>c;
        add(a,b,c);
    }

    dijkstra();
    if(d[n]==0x3f3f3f3f)
        cout<<-1<<endl;
    else
        cout<<d[n]<<endl;
    return 0;
}
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

​
