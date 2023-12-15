## 写在前面

由于本人实力尚浅，接触算法没多久，写这篇blog仅仅是想要提升自己对算法的理解，如果各位读者发现什么错误，恳请斧正，希望和大家一起进步。(●'◡'●)

## 题目

![spfa题目](https://img-blog.csdnimg.cn/direct/1bc68f5d4a754360a5a3625c2878d1bc.png#pic_center)

## 思路

1. 根据上一篇关于bellman_ford分析的blog（不了解？—>[传送门](https://blog.csdn.net/yourgrandfather_/article/details/134935786))我们知道bellman_ford的解法太暴力了，每次都遍历所有的边，聪明的你肯定想到了，其实bellman_ford算法中很多次松弛是没有意义的，因为只有当一个点的前驱结点距离更新时，那么该结点才有可能更新（只是有可能，不一定会更新）。
2. 想到这一点，我们就可以用一个队列`queue`来存储每次的更新的顶点。先将源点压入队列。
3. 只要队列不空，我们就取出队头，遍历它的所有边，依次更新与它相连的点距离源点的距离。如果该顶点距离源点的距离被更新，将它入队。
   
   ## 代码

```cpp
//一些数组的含义说明
int e[N],ne[N],h[N],w[N];        //建立邻接表
int d[N];        //存储每一个顶点距离源点的距离，初始化为INF
int st[N];        //记录每一个顶点是否在队列中，1表示在队列中，0表示不在队列中
```

```cpp
#include<iostream>
#include<cstring>
#include<queue>

using namespace std;
const int N=100010;
int n,m;
int e[N],ne[N],h[N],w[N],d[N],st[N],idx;

void add(int a,int b,int c)
{
    e[idx]=b,ne[idx]=h[a],w[idx]=c,h[a]=idx++;
}

void spfa()
{
    queue<int> q;       
    q.push(1);      //将源点压入队列
    d[1]=0;         //同时将源点距离自己本身的距离置为0
    st[1]=1;        //由于源点已经入队，所以源点的状态置为1
    while(!q.empty())       //只要队列不空，就一直循环下去
    {
        int t=q.front();
        q.pop();
        st[t]=0;        //由于已经将该顶点取出沪，所以又将该点的状态置为0，方便后续再次入队
        for(int i=h[t];i!=-1;i=ne[i])       //遍历与该顶点相连的所有顶点
        {
            int j=e[i];
            if(d[j]>d[t]+w[i])      
            {
                d[j]=d[t]+w[i];
                if(!st[j])
                {
                    st[j]=1;
                    q.push(j);      //如果距离被更新且该点不在队列中，就将该点压入队列
                }
            }
        }
    }
}

int main()
{
    cin>>n>>m;
    memset(h,-1,sizeof h);      //邻接表的初始化
    memset(d,0x3f,sizeof d);        //距离数组的初始化
    while(m--)
    {
        int a,b,c;
        cin>>a>>b>>c;
        add(a,b,c);
    }

    spfa();
    if(d[n]==0x3f3f3f3f)
        cout<<"impossible"<<endl;
    else
        cout<<d[n]<<endl;
    return 0;
}
```

## 说明

1. `st[]`数组并不是必须的，没有它也可以，只不过运行时间会慢一点。因为`st[]`的意义就是防止将已经在队列中的点再次入队，提升代码运行效率。
2. `SPFA`算法不能用来求解含有负权回路的图。因为如果有负权回路，那么负权回路中的顶点距离源点的距离会不断被更新，因此这些顶点也会不断地入队再出队，形成一个死循环。（因此我们也可以借助`SPFA`来判断一个图中是否含有负权回路，我一会在后面会写到的）
   
   ## 拓展
   
   #### 利用`SPFA`来判断途中是否含有负权回路，先看下面的题。
   
   ![spfa判断负权回路](https://img-blog.csdnimg.cn/direct/9366cbc7e01e4caeb33e6b602c387862.png#pic_center)
   
   #### 思路
3. 大致思路和前面`SPFA`思路差不多，就是引入了`cnt[]`数组，`cnt[]`数组是用来记录各个顶点到达源点的最短距离要经过的顶点的数量（如果该顶点`i`和源点联通的话`cnt[i]`就不为0，如果不连通就一直为0）关于`cnt[]`理解起来可能有些困难，一会配合着看代码可能要好理解一些😊
   
   #### 代码

```cpp
#include<iostream>
#include<cstring>
#include<queue>

using namespace std;
const int N=100010;
int n,m;
int e[N],ne[N],h[N],w[N],d[N],st[N],cnt[N],idx;

void add(int a,int b,int c)
{
    e[idx]=b,ne[idx]=h[a],w[idx]=c,h[a]=idx++;
}

bool spfa()
{
    queue<int> q;
    for(int i=1;i<=n;i++)       //一开始将所有顶点压入队列
    {
        q.push(i);
        st[i]=1;
    }
    d[1]=0;
    while(!q.empty())       //队列不为空
    {
        int t=q.front();
        q.pop();
        st[t]=0;
        for(int i=h[t];i!=-1;i=ne[i])       //和之前spfa做法一模一样
        {
            int j=e[i];
            if(d[j]>d[t]+w[i])
            {
                d[j]=d[t]+w[i];
                cnt[j]=cnt[t]+1;        //这个表示源点到顶点j经过cnt[t]+1个顶点
                if(cnt[j]>=n)       //如果经过的顶点大于所有顶点数，那么就说明含有负权回路
                    return false;
                if(!st[j])
                {
                    st[j]=1;
                    q.push(j);
                }
            }
        }
    }
    return true;        //如果最后可以跳出循环，就说明没有负权回路
}

int main()
{
    cin>>n>>m;
    memset(h,-1,sizeof h);
    memset(d,0x3f,sizeof d);
    while(m--)
    {
        int a,b,c;
        cin>>a>>b>>c;
        add(a,b,c);
    }

    if(spfa())
        cout<<"No"<<endl;
    else 
        cout<<"Yes"<<endl;
    return 0;
}
```

#### 说明

- 一开始一定要保证将所有顶点压入队列，因为图可能是不连通的。如果不这样的话，存在与源点不联通的负权回路就不会被探测出来，具体实例可以看下面这张图。
  ![spfa判断负权回路的反例](https://img-blog.csdnimg.cn/direct/66c922f62d6b4bbb873797deeb6a3c32.png#pic_center)
- 还有一种更优化的方法判断负权回路，由于我目前的实力有限，还没有看懂思路，想了解的童鞋可以看这位大佬[KonaeAkira](https://konaeakira.github.io/)。
  
  > 感谢各位童鞋看到这里，后面我会持续更新数据结构与算法，也希望大家点点赞，我们一起进步(❁´◡`❁)
