# GIS 6 Detecting Spatial Patterns

## 6.1 准备工作

1. 导入地图数据，挑选出研究区域

2. 导入研究数据

3. 将研究数据绘制在地图上

   ```R
   tmap_mode('plot') # 将tmap切换为画图模式
   ```

4. 数据清洗：去除重复的点和在边界之外的点

   ```R
   library(tidyverse)
   library(sf)
   BluePlaques <- distinct(BluePlaques) # 去重
   blue_plaques_sub <- BluePlaques[borough_map,] #去除边界之外的点
   ```

5. 挑选Harrow为研究区域，创建一个窗口来显示Harrow的边界

   ```R
   window <- as.owin(Harrow)
   plot(window)
   ```

## 6.2 点模式分析

在分析之前，由于spatstat 包不能直接处理SpatialPolygonDataFrames格式的变量，我们需要使用SpatialPolygonsDataFrames 或者 sf 对象。对于点模式分析，我们需要先创建点模式对象(point pattern object, ppp)

```r
blue_plaques_Harrow <- blue_plaques_Harrow %>%
  as(.,'Spatial')

blue_plaques.ppp <- ppp(x=blue_plaques_Harrow@coords[,1],
                        y=blue_plaques_Harrow@coords[,2],
                        window=window)
```

核密度估计(Kernel Density Estimation)

为了完成核密度估计，需要用ppp对象的density()功能计算密度

```r
#核密度估计和可视化
blue_plaques.ppp %>%
  density(.,sigma=500) %>% #sigma控制核的大小
  plot()
```

样方分析(quadrat analysis)

```r
# 先绘制地图
blue_plaques.ppp %>%
  plot(.,pch=16,cex=0.5,
       main='Blue Plaques Harrow')

# 样方分析
blue_plaques.ppp %>%
  quadratcount(.,nx=6,ny=6) %>% # 确定样方个数
    plot(., add=T,col='red') #plot的时候绘制红色的格网
```

对样方内数据的频率进行统计

```r
q_count <- blue_plaques.ppp %>%
  quadratcount(.,nx=6,ny=6) %>%
  as.data.frame() %>%
  dplyr::count(var1=Freq) %>%
  dplyr::rename(Freqquadratcount=n)
```

q_count代表各个样方点出现的频次（可以理解为对样方中点的个数做直方图）

经过点模式分析后，我们需要知道哪些区域是CSR，哪些区域不是。为了完成这一步，我们需要对观测值和预期值做卡方检验(Chi-square test)。如果卡方值<0.05，就拒绝“此区域是CSR”的假设；如果卡方>0.05，则接受假设，说明区域无明显聚类特征。

假设检验和可视化代码：

```r
q_count_table <- q_count %>%
  mutate(Pr = (lambda^var1*exp(-lambda))/factorial(var1)) %>%
  mutate(expected = (round(Pr * sums$Freqquadratcount, 0)))

plot(c(1,5),c(0,14),type='n',
              xlab='number of blue plaques',
              ylab='Frequency of Occurances')

points(q_count_table$Freqquadratcount,
       col='red',
       type='o',lwd=3)
points(q_count_table$expected,
       col='blue',
       type='o',lwd=3)

teststats <- quadrat.test(blue_plaques.ppp, nx=6,ny=6)

plot(blue_plaques.ppp,pch=16,cex=0.5,main='Blue Plaques in Harrow')
plot(teststats, add=T,col='red')
```

## 6.3 Repley's K

在`spatstat`包中可以用kest()做Repley's K 检验

```r
K <- blue_plaques.ppp %>%
  Kest(.,correction='border') %>%
  plot()
```

在红色线之上的，认为是拥有聚类特征，在红色线之下，认为有分散特征。

## 6.4 DBSCAN

样方和Repley’s K都用于我们知道在区域中有聚类存在的情况，DBSCAN可以帮助我们发现聚类

R语言实现DBSCAN需要两个参量：$$\epsilon$$ :用来寻找聚类的半径；Minpts：构成聚类的最小点数

`fpc::dbscan()`可以实现该功能

```r
#first extract the points from the spatial points data frame
BluePlaquesSubPoints <- BluePlaquesSub %>%
  coordinates(.)%>%
  as.data.frame()

#now run the dbscan analysis
db <- BluePlaquesSubPoints %>%
  fpc::dbscan(.,eps = 700, MinPts = 4)

#now plot the results
plot(db, BluePlaquesSubPoints, main = "DBSCAN Output", frame = F)
plot(BoroughMap$geometry, add=T)
```

## 6.5 空间自相关

在绘制空间自相关时候，首先需要计算邻接矩阵，在这个部分使用最邻近法计算

首先得到地图中心点坐标

```r
#First calculate the centroids of all Wards in London

coordsW <- points_sf_joined%>%
  st_centroid()%>%
  st_geometry()
  
plot(coordsW,axes=TRUE)
```

绘制邻接矩阵与网络

```r
LWard_nb <- points_sf_joined %>%
  poly2nb(., queen=T) %>%

summary(LWard_nb)
plot(LWard_nb, st_geometry(coordsW), col="red")
```

计算moran's I、Geacy's C、Getis Ord General G

```r
Lward.lw <- LWard_nb %>%
  nb2listw(., style="C")
  
I_LWard_Global_Density <- points_sf_joined %>%
  pull(density) %>%
  as.vector()%>%
  moran.test(., Lward.lw)

C_LWard_Global_Density <- 
  points_sf_joined %>%
  pull(density) %>%
  as.vector()%>%
  geary.test(., Lward.lw)

G_LWard_Global_Density <- 
  points_sf_joined %>%
  pull(density) %>%
  as.vector()%>%
  globalG.test(., Lward.lw)
```

可视化部分（Moran's I为例）：

```r
tm_shape(points_sf_joined) +
    tm_polygons("plaque_count_Iz",
        style="fixed",
        breaks=breaks1,
        palette=MoranColours,
        midpoint=NA,
        title="Local Moran's I, Blue Plaques in London")
```

## appendix week 6 quiz



1. **What is spatial sub setting and how do you do it ？**

+ a.    Select data within another data set – e.g. points within a polygon… BluePlaquesSub <- data you want to subset[data you want to subset by, columns, operator to subset ]r

+ b.    Similar to a raster mask

2. **What is attribute sub setting (select by attribute)**

+ a.    Filtering based on criteria 

3. **How is spatial sub setting different to spatial joining ？**

+ a.    Joining will keep everything in the left dataset

+ b.    It might aggregate the data (e.g. you can’t keep the points, it will join the data within them to a polygon) as the left dataset is the ‘main’ dataset

4. **When using `spatstat` package what type of spatial object do we need to use**

+ a.    point pattern (ppp) object.

5. **What test determines if there is an association between the observed and expected frequencies from quadrat analysis / poisson distribution**

+ a.    Chi-square and p<0.05.

6. **What advantages does Ripley’s K have over quadrat analysis**

+ a.    Considers circles around points as opposed to geographic areas that don’t align with quadrants

7. **What does DBSCAN tell us that Ripley’s K and quadrat analysis can’t **

+ a.    Tell is if we have clusters present, DBSCAN shows us where

8. **How does spatial autocorrelation differ from point pattern analysis**

+ a.    Spatial autocorrelation looks at spatially continuous observations and their similarity of values, point pattern is just clustering of single, binary points.

9. **What is a spatial weight matrix ?**

+ a.    A representation of the relationships between the objects (shapes geometries) in your data

10. **What does Moran’s I show us**

+ a.    Shows how similar surrounding objects (based on the weight matrix) are to the current one, with a value of 1 = clustered, 0 = no pattern, -1 = dispersed
