# CASA0005 Lecture 6 Detecting Spatial Patterns

## 1. point pattern analysis 点模式分析

空间中的点分布模式大概有三种

- **聚类(cluster)**：点与点之间分布较紧凑
- **分散(disperse)**：点与点之间分散排布
- **随机(random)**：当对象既不以聚集模式存在也不以分散模式存在时发生。这也就是我们所说的“假设”或“规范”模式。
- **规律(regular)**: 点与点之间排布距离相等
- **完全集中(perfectly concentrated)**: 所有点完全集中于一个位置

complete spatial randomness (CSR) 完全空间随机分布

一般认为空间中点的分布

 ## 2. Poisson distribution 泊松分布

定义：描述在固定时间内某事情发生次数的概率

泊松分布适用于：

1. 时间是离散的并且是证书技术
2. 各个事件相互独立
3. 事件发生的均值是已知的

泊松分布在点模式分析中常常作为预期模型之一来对观测数据拟合

## 3. Quad rate Analysis 样方分析

把地区分成均匀的格网，然后对格网内事件数量计数，再将事件数按照期望的函数（一般是泊松分布）拟合，可以通过卡方分布来判断观测数据与预期分布是否一致

样方分析的缺点：

+ 格网的size很大程度上影响分析结果
+ 格网的形状可能不统一（因为研究区域不一定是标准的图形）

## 4. Ripley's K

Ripley’s K可以用来表明质心一定范围内是否具有显著的聚类或离散

简单地说，Ripley's K是在半径任意区域内点的数目和总$$K(r)$$对于泊松分布的期望值是 $$\pi r^{2}$$ 。Ripley's K与泊松分布作比较，在泊松分布之上则是聚类，反之是离散。 

Ripley's K的缺陷：

+ 会受地形影响（事件不会发生在河谷/山区）
+ 当点数太多时会计算困难
+ 研究区域的范围会影响计算：圆周会覆盖其他区域

改进Ripley's K可以处理边缘效应

## 5. DBSCAN (Density-Based Spatial Clustering of Applications with Noise)

DBSCAN的原理如下：

为了进行 DBSCAN 聚类，所有的点被分为**核心点**、**边界点**及**局外点**：当在一个半径为$$\epsilon$$ (epsilon)的圆内，点的个数大于或等于MinPts时，将此点认为是核心点（core），在核心点范围内而不是核心点则是边界点(border)，在核心点范围外的点是离群点（noise）

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWNiLnpoaW1nLmNvbS92Mi0yZDY0NDJjYmY5ZjY5M2U4ZTIxZjUzN2EzNDliMzI4Zl9yLmpwZw?x-oss-process=image/format,png#pic_center)

当两个点a,b可以通过同一聚类的单位圆相连接，则称a,b为密度相连，反之则为非密度相连。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS92Mi0xOTAwMTUxMGJiMzc2YTUzZmZhMDFhM2RiMmNhNGZkYl9yLmpwZw?x-oss-process=image/format,png#pic_center)

算法实现：

1. 取任意一点看他是否为核心点，若是核心点，则寻找他可达点是否为核心点，直至全部寻找完毕，形成第一个聚类C1
2. 对于剩下的点重复1步骤，直至所有点寻找完毕。

DBSCAN的优点

1. DBSCAN 不需要预先声明聚类数量。
2. DBSCAN 可以找出任何形状的聚类，甚至能找出一个聚类，它包围但不连接另一个聚类，并且可以避免极小聚类的出现。
3. DBSCAN 能分辨噪音（局外点）。
4. DBSCAN 只需两个参数，且对数据库内的点的次序几乎不敏感。

但是，如何设置eps和MinPts是一个问题。

## 6.  Spatial Autorrelation 空间自相关

**空间权重矩阵**：常用的空间权重矩阵是基于连通性或者基于距离的

基于连通性的：i和j相邻，则邻接矩阵记为1，不相邻则记为0
$$
w_{ij}=1 \ if \ regions\ i\ and\ j\ are\ contiguous,\ w_{ij}=0\ otherwise
$$
基于距离的：考虑两区域中心点的直线距离（一般呈反比）
$$
w_{ij}=d_{ij}^{-{\beta}}
$$

$$
w_{ij}=e^{-{\beta}d_{ij}}
$$

## 7. indices of social association 社会相关性指数

社会相关性指数主要讨论：**某个社会特征是否具有空间自相关性？**

### 7.1 Moran's I 

 Moran's I 是一种全局判定的空间相关性指数，Moran's I范围是 [-1,1] ,靠近1代表聚类，靠近-1则代表分散

Moran's I 计算方法

![img](E:\casa\casa0005\week6\文档\images\IMG_2100(20211125-164936).PNG)

Greay's C 与Moran's I 相似，但Greay's C不考虑空间权重，只考虑是否为邻域，所以可靠性不如Moran's I 。

Getis-Ord's Gi*是局部的空间自相关检测度量，也叫热点分析。
