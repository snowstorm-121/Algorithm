## 写在前面：

由于本人实力尚浅，接触算法没多久，写这篇blog仅仅是想要提升自己对算法的理解，如果各位读者发现什么错误，恳请指正，希望和大家一起进步。

## 题目

![bellman_ford题目](https://img-blog.csdnimg.cn/direct/4cbc25670f344ff2b039025016e1552b.png#pic_center)

## 思路

1. 外层循环，用来控制边数，循环多少次就是最多经过经过多少条边
2. 内层循环，遍历所有的边，依次来更新图中顶点到源点的距离
   
   ## 代码

```
#include<iostream>
#include<cstring>

using namespace std;
const int N=10010,M=510;
int n,m,k;
int d[M],backup[M];     //d[]数组来表示顶点到源点的距离，backup数组是在每次松弛前对d数组的备份

struct Edge     //用来存储图
{
    int a,b,w;
}e[N];

void Bellman_ford()
{
    memset(d,0x3f,sizeof d);        //初始化
    d[1]=0;
    while(k--)      //边数限制是k，所以就进行k次松弛
    {
        memcpy(backup,d,sizeof d);      //对上一次松弛的d数组的备份，防止串联
        for(int j=0;j<m;j++)        //遍历图中的所有的边
        {
            int a=e[j].a,b=e[j].b,w=e[j].w;
            d[b]=min(d[b],backup[a]+w);     //一定要用backup
        }
    }
}


int main()
{
    cin>>n>>m>>k;
    for(int i=0;i<m;i++)
    {
        int a,b,c;
        cin>>a>>b>>c;
        e[i]={a,b,c};
    }

    Bellman_ford();
    if(d[n]>0x3f3f3f3f/2)       //就算终点和源点不连通，终点到源点的距离也可能会被更新，所以和INf/2比较
        cout<<"impossible"<<endl;
    else 
        cout<<d[n]<<endl;
    return 0;
}
```

## 说明

- 使用`backup`数组的意义：如果边数限制为1，那么在第一次循环更新二号顶点，把二号顶点距离更新为1后，然后更新三号顶点，那么三号顶点的距离就会被更新为4（1+3=4），但是我们知道三号顶点应该被更新为7，因为边数限制为1，而如果三号距离为4就要经过两条边，这与题目不符，而使用`backup`数组就可以避免这个问题（实在不懂的可以找着我这个图手动模拟一下）![backup的意义](https://img-blog.csdnimg.cn/direct/1806f93d70f74c8ca5031b392e642ac2.png#pic_center)
- 为什么最后判断是与`INF/2`比较大小，因为即使终点和源点不连通，但是只要有点和终点相连且边权为负，那么终点的距离也会不断被减小。如下图![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/043aa3488d1e4699ac179bc089f3288b.jpeg#pic_center)
