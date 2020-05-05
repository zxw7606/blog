---
title: 克鲁斯卡尔算法(Kruskal)
date: 2019-11-03 16:40:04
tags:
    - 算法
---



## 算法目的

求出从某顶点到其余各顶点的最短路径(不是路径全部加起来最小)



{% asset_img 01.jpg  %}



矩阵格式

```csv
         0,        30,     32767,        10,        50,
     32767,         0,        60,     32767,        80,
     32767,     32767,         0,     32767,     32767,
     32767,     32767,     32767,         0,        30,
     32767,     32767,        40,     32767,         0,

```





## 算法步骤



以V0点到各点为例

1. 算出V0点到其他点的直接距离记录到dist数组中，比如V0->v3=10,dist[3]=10并且记录路径到path数组中，比如V0->V3 =10, 那么path[3] =0，V0->V2=无穷,path[2]=-1;
2. 走求出最小的路径,比如第一次V0有 10,30,50,MAX,走 10(V3) 并标记V3走过
3. 更新没有走过的点的dist,比如dist[4] = 50，但是有更短的路径40 ， 那么此时dist[4] = dist[3]+ 30 (40) ，path[4] = 3; 

一直循环直到所有点都走过，那么最后V4 的路径为 path[4] (3) ,  path[path[4]] (0) 



<!--more-->

## 代码示例

存放矩阵的类

```c++
#include <iostream>
#include <iomanip>
class AdjMatrix
{
public:
    int **eage; // 矩阵二维数组
    int numV; /*顶点数*/
    int numE; /*边数*/
  
  	/**
     * 构造函数 
     */
    AdjMatrix(int M)
    {
        // 初始化数据
        numV = M;
        eage = new int *[numV];
        for (int i = 0; i < numV; i++)
        {
            eage[i] = new int[numV];
            for (int j = 0; j < numV; j++)
            {
                eage[i][j] = SHRT_MAX;
                if (i == j)
                {
                    eage[i][j] = 0;
                }
            }
        }
    };
  
  	/**
     * 打印矩阵 输入边的权值 
     */
    void inputE(int e)
    {
        numE = e;
        int vi, vj, w;
        //输入边的权重
        for (int i = 0; i < numE; i++)
        {
            std::cout << "输入边的信息及权值(vi,vj,w)：" << std::endl;
            std::cin >> vi >> vj >> w;
            eage[vi][vj] = w;
            // eage[vj ][vi ] = w; /*对于无向图*/
        }
    };
  	/**
     * 打印矩阵 
     */
    void print() 
    {
        for (int i = 0; i < numV; i++)
        {
            for (int j = 0; j < numV; j++)
            {
                std::cout << std::setw(10) << eage[i][j] << ",";
            }
            std::cout << std::endl;
        }
    };
};
```







```c++
#include "AdjMatrix.cpp"
#include <iostream>
#include <iomanip> //不要忘记包含此头文件
using namespace std;

/**
* 打印路径
*/
void printPath(int v, int *path)
{

    if (path[v] != -1)
    {
        printPath(path[v], path);
        cout << "->";
    }
    cout << setw(5) << v;
}


/**
* 核心
*/
void dijkstra(AdjMatrix &G, int v)
{

    int *dist = new int[G.numV]{SHRT_MAX}; // 存放点的最短路径的长度
    int *path = new int[G.numV]{-1};       // 存放点的上一个点
    int *s = new int[G.numV]{0};

    // 初始化V到各点的距离
    for (int i = 0; i < G.numV; i++)
    {
        dist[i] = G.eage[v][i];
        if (i != v && dist[i] < SHRT_MAX)
        {
            path[i] = v;
        }
        else
        {
            path[i] = -1;
        }
    }
    s[v] = 1;

  
  
    // 寻找最短的路径
    for (int i = 0; i < G.numV; i++)
    {
        int u = v;
        int min = SHRT_MAX;
        if (dist[i] < min)
        {
            min = dist[i];
            u = i;
        }
        s[u] = 1;
			 // 更新没有走过的点
        for (int j = 0; j < G.numV; j++)
        {
           
            if (!s[j] && dist[u] + G.eage[u][j] < dist[j])
            {
                dist[j] = dist[u] + G.eage[u][j];
                path[j] = u;
            }
        }
    }
  
  
    // 输出结果
    for (int i = 0; i < G.numV; i++)
    {
        if (i != v)
        {
            printPath(i, path);
            cout << endl;
        }
    }
};

int main()
{

    AdjMatrix adjMatrix(5);
    adjMatrix.inputE(7);
    adjMatrix.print();
    dijkstra(adjMatrix, 0);
    return 0;
}
```



## 测试结果

```
   	0->    1				(到1点的最短路径)
    0->    1->    2 (到2点的最短路径)
    0->    3				(到3点的最短路径)
    0->    3->    4	(到4点的最短路径)
```

