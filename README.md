# 江西省管内长途客运汽车资源的优化配置

## 背景

据2021全球数字经济大会统计，我国数字经济规模已经连续多年位居世界第二。特别是新冠肺炎疫情暴发以来，数字技术、数字经济在支持抗击新冠肺炎疫情、恢复生产生活方面发挥了重要作用。

如何高质量利用数据资源，发展数字经济意义重大。习近平总书记多次提出要发展数字经济，推进数字产业化和产业数字化，推动数字经济和实体经济深度融合，打造具有国际竞争力的数字产业集群。

江西省也将大力实施数字经济写入2022年全省深化发展和改革双“一号工程”。

数字经济和实体经济深度融合的重要途径是通过数字技术解决实际问题。

>关键词：`matlab` `Gauss` 消元 `Dijkstra`算法

## 问题分析

### 对于问题一

- 第一步，我们首先计算出了各个城市之间的具体人口流动情况，在这里我们使用 `matlab` 运用 `Gauss` 消元法进行计算。

- 第二步，以第一步的计算数据为基础，运用 `Execl` 对数据进行量化分析，再根据合理假设模拟实际单程载客量。

- 第三步，计算出载客量与实际需运输人数的差值，再根据该差值计算出还需购入的合适的客车类型以及客车数。

### 表1 江西省各地级市之间的公路距离表

| 公路距离(公里) | 南昌| 赣州|宜春|吉安|上饶|抚州|九江|景德镇|萍乡|新余|鹰潭|
| :---        |    :----:   |  :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 南昌|  -  |  429 | 210 | 237 | 276 | 105 | 150 | 282 | 270 | 189 | 147 |
| 赣州 |  -  |  - | 314 | 186 | 512 | 345 | 524 | 537 | 322 | 284 | 402 |
| 宜春 |   -  |  - | -| 150 | 392 | 214 | 336 | 392 | 64 | 72 | 297 |
|吉安 |    -  | - | -| - | 387 | 194 | 347 | 410 | 210 | 122 | 276 |
| 上饶 |    -  |  - | - | - |- | 196 | 332 | 200 | 452| 337 | 113 |
|抚州  |    -  |  -| - | - | - | - | 241 | 220 | 280 | 165 | 102 |
| 九江  |   -  |  - | - | - | - | - | - | 140 | 389 | 280 | 276 |
| 景德镇  |  -  |  - | - | - | - | -| - | - | 453 | 337 | 156 |
| 萍乡  |    -  |  - | - | -| - | - |- | - | - | 120 | 360 |
| 新余  |   -  |  - | - | - | - | - | - | - | - |- | 245 |
| 鹰潭 |   -  |  - | - | -| -| - | - | - | - | - | - |

### 表2 江西省各地市汽车运营公司拥有的客车数量表

| 地区 | 南昌| 赣州|宜春|吉安|上饶|抚州|九江|景德镇|萍乡|新余|鹰潭|
| :---        |    :----:   |  :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 小型客车|  14  |  85 | 0 | 42 | 16 | 16 | 55 | 19 | 24 | 0 | 7 |
| 中型客车 |129|  243 | 30 | 66 | 221 | 100 | 299 | 15 | 70 | 11 | 26 |
| 大型客车 |133|  126 | 74| 28 | 127 | 39 | 88 | 12 | 11 | 4 | 19 |
|特大型客车 |0 | 0 | 0| 0 | 0 | 0 | 0 | 1 | 1 | 0 | 0 |
| 总计 | 276 |  454 | 104 | 136 |364 | 155 | 442 | 47 | 106| 15 | 52 |

### 表3 江西省各地市的常驻人口数

| 地区 | 南昌| 赣州|宜春|吉安|上饶|抚州|九江|景德镇|萍乡|新余|鹰潭|
| :---        |    :----:   |  :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 常住人口(万人)|643.75 |898 |497.11 |442.51| 643.67| 357.94| 456.07| 162.06| 180.59 |120.21| 115.5|
| 城镇化率（%）|78.64|56.35 |57.38| 53.41| 55.31| 57.96| 62.15| 65.94| 68.77| 74.14| 65.43|

## 已知条件

1. 根据交通管理部门要求，小型客车最大载客量为9人，中型客车的最大载客量为19人，大型客车最大载客量为45人，特大型客车最大载客量为55人。

2. 江西省各地市之间的日人口流动为1.5%，目的地与当地人口比例近似线性相关，在不考虑火车等其它交通工具。

3. 已知江西省各地市的常驻人口数量、江西省各地级市之间的公路距离和江西省各地市的常驻人口 [^1] 。

4. 常住人口城镇化率=$\frac{在城镇居住半年以上的常住人口}{总人口}$×100%[^2]

## 假设条件

1. 假设每辆客车处于运营状态时均为满载

2. 假设每种客车的运行速度均为 `90km/h` ，且车辆中途不停车、不用加油维护等

3. 客车到达目的地后立即返回出发地

4. 假设各城市的常住人口基本不发生变化

## 准备工作

### 符号声明

|    符号   | 符号解释 |
| ----------- | ----------- |
| $a_{i}$     | $i$ 城市原有的小客车数    |
| $b_{i}$   | $i$ 城市原有的中客车数        |
| $c_{i}$   | $i$ 城市原有的大客车数      |
| $d_{i}$   | $i$ 城市原有的特大客车数     |
| $Ur_{i}$  | $i$ 城市的城镇化率    |
|$Rp_{i}$   | $i$ 城市的常住人口|

## 附录

```
//最段路代码-Dijkstra算法
const int INF = 1000000000;
/*Dijkstra算法解决的是单源最短路径问题，即给定图G(V,E)和起点s(起点又称为源点),
求从起点s到达其它顶点的最短距离，并将最短距离存储在矩阵d中*/
void Dijkstra(int n, int s, vector<vector<int>> G, vector<bool>& vis, vector<int>& d)
{
       /*
       param
       n：           顶点个数
       s：           源点
       G：           图的邻接矩阵
       vis：         标记顶点是否已被访问
       d：           存储源点s到达其它顶点的最短距离
       */
       fill(d.begin(), d.end(), INF);                         //初始化最短距离矩阵，所有为INF
       d[s] = 0;                                              //起点s到达自身的距离为0
       for (int i = 0; i < n; ++i)
       {
              int u = -1;                                     //找到d[u]最小的u
              int MIN = INF;                                  //记录最小的d[u]
              for (int j = 0; j < n; ++j)                     //开始寻找最小的d[u]
              {
                     if (vis[j] == false && d[j] < MIN)
                     {
                           u = j;
                           MIN = d[j];
                     }
              }
              //找不到小于INF的d[u]，说明剩下的顶点和起点s不连通
              if (u == -1)
                     return;
              vis[u] = true;                                  //标记u已被访问
              for (int v = 0; v < n; ++v)
              {
                     //遍历全部顶点，若是v未被访问&&u可以到达v&&以u为中介点可使d[v]更优
                     if (vis[v] == false && d[u] + G[u][v] < d[v])
                           d[v] = d[u] + G[u][v];             //更新d[v]
              }
       }
}
```
[^3]

[^1]:https://baike.baidu.com/item/%E5%B8%B8%E4%BD%8F%E4%BA%BA%E5%8F%A3/1238278

[^2]:https://www.ndrc.gov.cn/fggz/fzzlgh/gjfzgh/202112/t20211225_1309650.html

[^3]:https://www.csdn.net/tags/OtDagg0sNjEtYmxvZwO0O0OO0O0O.html
