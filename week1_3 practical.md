

# wk1

#### Load  .shp或 .gpkg

· 读取   .shp文件

```R
library(sf)

# change this to your file path!!!

shape <- st_read("C:/Users/nekopi/OneDrive/CASA/21-22 term 1/0005 GIS/week1 homework/statistical-gis-boundaries-london/ESRI/London_Borough_Excluding_MHW.shp")
```

· 读取 .gpkg

```R
library(sf)
library(here)
st_layers(here("prac3_data", "gadm36_AUS.gpkg"))

## Driver: GPKG 
## Available layers:
##     layer_name geometry_type features fields
## 1 gadm36_AUS_0 Multi Polygon        1      2
## 2 gadm36_AUS_1 Multi Polygon       11     10
## 3 gadm36_AUS_2 Multi Polygon      569     13


Ausoutline <- st_read(here("prac3_data", "gadm36_AUS.gpkg"), 
                      layer='gadm36_AUS_0') #0 means whole Australia
                      
## Reading layer `gadm36_AUS_0' from data source 
##   `C:\Users\Andy\OneDrive - University College London\Teaching\CASA0005\2020_2021\CASA0005repo\prac3_data\gadm36_AUS.gpkg' 
##   using driver `GPKG'
## Simple feature collection with 1 feature and 2 fields
## Geometry type: MULTIPOLYGON
## Dimension:     XY
## Bounding box:  xmin: 112.9211 ymin: -55.11694 xmax: 159.1092 ymax: -9.142176
## Geodetic CRS:  WGS 84
```



· 查看坐标系统

```R
Cnoutline <- st_read(here("Raw_Data", "country_outline.shp")) 
print(Cnoutline) #查看详细信息以及前十行

##Simple feature collection with 34 features and 2 fields
##Geometry type: MULTIPOLYGON
##Dimension:     XY
##Bounding box:  xmin: 73.50235 ymin: 3.837031 xmax: 135.0957 ymax: 53.56362
##Geodetic CRS:  WGS 84
##First 10 features:
            ##City   Code                       geometry
##1          安徽省 340000 MULTIPOLYGON (((116.4319 34...
##2  澳门特别行政区 820000 MULTIPOLYGON (((113.5664 22...
##3          北京市 110000 MULTIPOLYGON (((116.6318 41...
##4          福建省 350000 MULTIPOLYGON (((117.6936 23...
##5          甘肃省 620000 MULTIPOLYGON (((106.0756 35...
##6          广东省 440000 MULTIPOLYGON (((110.5646 20...
##7  广西壮族自治区 450000 MULTIPOLYGON (((109.2185 20...
##8          贵州省 520000 MULTIPOLYGON (((105.0978 24...
##9          海南省 460000 MULTIPOLYGON (((112.0483 3....
##10         河北省 130000 MULTIPOLYGON (((118.5791 38...

st_crs(Cnoutline)$proj4string 
#仅显示坐标系
#[1] "+proj=longlat +datum=WGS84 +no_defs"
```

· 获取 shapefile 数据（属性表）中保存的数据的摘要

```R
summary(shape)
```



· 查看  .shp的外观

```R
plot(shape)
```



· 仅查看outline of the shape

```R
library(sf)
shape %>% 
  st_geometry() %>%
  plot()
```

------

#### Load  .tif

##### · 加载raster brick

```R
library(raster)
jan<-raster(here("prac3_data", "wc2.0_5m_tavg_01.tif"))
# have a look at the raster layer jan
jan


## class      : RasterLayer 
## dimensions : 2160, 4320, 9331200  (nrow, ncol, ncell)
## resolution : 0.08333333, 0.08333333  (x, y)
## extent     : -180, 180, -90, 90  (xmin, xmax, ymin, ymax)
## crs        : +proj=longlat +datum=WGS84 +no_defs 
## source     : C:/Users/Andy/OneDrive - University College London/Teaching/CASA0005/2020_2021/CASA0005repo/prac3_data/wc2.0_5m_tavg_01.tif 
## names      : wc2.0_5m_tavg_01 
## values     : -46.697, 34.291  (min, max)

```



##### · 加载多个raster layers (raster bricks)

```
# look in our folder, find the files that end with .tif

library(fs)
dir_info("prac3_data/") 

## # A tibble: 16 x 18
##    path              type       size permissions modification_time   user  group
##    <fs::path>        <fct>  <fs::by> <fs::perms> <dttm>              <chr> <chr>
##  1 prac3_data/gadm3~ file     83.57M rw-         2020-04-08 12:04:37 <NA>  <NA> 
##  2 prac3_data/gadm3~ direc~        0 rw-         2020-06-05 17:57:56 <NA>  <NA> 
##  3 prac3_data/licen~ file        300 rw-         2020-04-08 12:04:37 <NA>  <NA> 
##  4 prac3_data/readm~ file        256 rw-         2020-04-08 12:04:37 <NA>  <NA> 
##  5 prac3_data/wc2.0~ file      8.78M rw-         2020-04-08 12:04:38 <NA>  <NA> 
##  6 prac3_data/wc2.0~ file      8.92M rw-         2020-04-08 12:04:38 <NA>  <NA> 
##  7 prac3_data/wc2.0~ file      9.03M rw-         2020-04-08 12:04:38 <NA>  <NA> 
##  8 prac3_data/wc2.0~ file      9.09M rw-         2020-04-08 12:04:38 <NA>  <NA> 
##  9 prac3_data/wc2.0~ file      9.12M rw-         2020-04-08 12:04:38 <NA>  <NA> 
## 10 prac3_data/wc2.0~ file      9.04M rw-         2020-04-08 12:04:38 <NA>  <NA> 
## 11 prac3_data/wc2.0~ file      8.98M rw-         2020-04-08 12:04:38 <NA>  <NA> 
## 12 prac3_data/wc2.0~ file      8.95M rw-         2020-04-08 12:04:38 <NA>  <NA> 
## 13 prac3_data/wc2.0~ file      9.02M rw-         2020-04-08 12:04:38 <NA>  <NA> 
## 14 prac3_data/wc2.0~ file      8.99M rw-         2020-04-08 12:04:38 <NA>  <NA> 
## 15 prac3_data/wc2.0~ file      8.86M rw-         2020-04-08 12:04:38 <NA>  <NA> 
## 16 prac3_data/wc2.0~ file      8.77M rw-         2020-04-08 12:04:38 <NA>  <NA> 
## # ... with 11 more variables: device_id <dbl>, hard_links <dbl>,
## #   special_device_id <dbl>, inode <dbl>, block_size <dbl>, blocks <dbl>,
## #   flags <int>, generation <dbl>, access_time <dttm>, change_time <dttm>,
## #   birth_time <dttm>
```

##### · 更快捷简洁的方式

```
library(tidyverse)
listfiles<-dir_info("prac3_data/") %>%
  filter(str_detect(path, ".tif")) %>%
  dplyr::select(path)%>%
  pull()

#have a look at the file names 
listfiles

## prac3_data/wc2.0_5m_tavg_01.tif prac3_data/wc2.0_5m_tavg_02.tif 
## prac3_data/wc2.0_5m_tavg_03.tif prac3_data/wc2.0_5m_tavg_04.tif 
## prac3_data/wc2.0_5m_tavg_05.tif prac3_data/wc2.0_5m_tavg_06.tif 
## prac3_data/wc2.0_5m_tavg_07.tif prac3_data/wc2.0_5m_tavg_08.tif 
## prac3_data/wc2.0_5m_tavg_09.tif prac3_data/wc2.0_5m_tavg_10.tif 
## prac3_data/wc2.0_5m_tavg_11.tif prac3_data/wc2.0_5m_tavg_12.tif
```

##### · load all data into a raster stack 

A raster stack is a collection of raster layers with the same spatial extent and resolution.

```
worldclimtemp <- listfiles %>%
  stack()
  
#have a look at the raster stack
worldclimtemp
```

```
## class      : RasterStack 
## dimensions : 2160, 4320, 9331200, 12  (nrow, ncol, ncell, nlayers)
## resolution : 0.08333333, 0.08333333  (x, y)
## extent     : -180, 180, -90, 90  (xmin, xmax, ymin, ymax)
## crs        : +proj=longlat +datum=WGS84 +no_defs 
## names      : wc2.0_5m_tavg_01, wc2.0_5m_tavg_02, wc2.0_5m_tavg_03, wc2.0_5m_tavg_04, wc2.0_5m_tavg_05, wc2.0_5m_tavg_06, wc2.0_5m_tavg_07, wc2.0_5m_tavg_08, wc2.0_5m_tavg_09, wc2.0_5m_tavg_10, wc2.0_5m_tavg_11, wc2.0_5m_tavg_12 
## min values :          -46.697,          -44.559,          -57.107,          -62.996,          -63.541,          -63.096,          -66.785,          -64.600,          -62.600,          -54.400,          -42.000,          -45.340 
## max values :           34.291,           33.174,           33.904,           34.629,           36.312,           38.400,           43.036,           41.073,           36.389,           33.869,           33.518,           33.667
```

```
# access the january layer
worldclimtemp[[1]]
```



##### · 重命名every layer in the stacks

 `rename()` from the `dplyr` package isn’t yet available for raster data.

```
month <- c("Jan", "Feb", "Mar", "Apr", "May", "Jun", 
           "Jul", "Aug", "Sep", "Oct", "Nov", "Dec")

names(worldclimtemp) <- month
```

```
#to get data for just January use our new layer name
worldclimtemp$Jan
```



------

#### Load   .csv

· load  .csv file

```
library(tidyverse)
#this needs to be your file path again
mycsv <-  read_csv("C:/Users/nekopi/OneDrive/CASA/21-22 term 1/0005 GIS/week1 homework/fly_tipping_borough_1.csv")  
```

```
LondonData<- read.csv("地址/文件名.格式",
					header = TRUE, sep = ",", encoding = "latin1")
					
#"header = TRUE": the first row of the file containsheader information.
#"sep = "," ": the values in the file are separated with ",", rather than ":" or ";". 
#encoding = "latin1": 使用“Latin 1”作为储存字符的格式
```

```
library(here)
LondonData<- read.csv(here::here("文件夹名"，"文件名.格式"),
						header = TRUE, sep = ",", 
						encoding = "latin1")
						
#使用“here”package直接读取文件，避免/或\的问题。
# 命令是 here::here()
```



· view the  .csv file

```
mycsv
```



------

#### Join  .csv and  .shp

· join the .csv to .shp

​		*在 .shp中的列"GSS_CODE"与 .csv中的"Row Labels"列一一对应*

```
shape <- shape%>%
  merge(.,
        mycsv,
        by.x="GSS_CODE", 
        by.y="row_lables")
        
 #chapter1的Join代码
```

![image-20211017175104138](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211017175104138.png)

![img](https://andrewmaclachlan.github.io/CASA0005repo/prac2_images/sql-joins.png)

![image-20211021111843118](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211021111843118.png)



```
joined_data <- shape %>% 
  clean_names() %>%
  
  # the . here just means use the data already loaded
  left_join(.,    
            county_to_state_difference,
            by = c("countylabe" = "county")) 
 			#一一匹配时，见下图示意
 			
# If the strings didn't match (e.g. lower and upper case) we can covert them with...
t <- shape %>% 
  mutate(COUNTY2 = tolower(COUNTY))
            
#chapter2homework代码
```

![Taken from Tidy explain by Garrick Aden-Buie](https://andrewmaclachlan.github.io/CASA0005repo/prac2_images/left-join.gif)

```
#EW is the data we read in straight from the web
BoroughDataMap <- EW %>%
  clean_names()%>%
  # the . here just means use the data already loaded
  filter(str_detect(lad15cd, "^E09"))%>%
  merge(.,
        LondonData, 
        by.x="lad15cd", 
        by.y="new_code",    #存在多个匹配项时，见下图
        no.dups = TRUE)%>%
  distinct(.,lad15cd, 
           .keep_all = TRUE)
  #We’ve added some more arguments to distinct() that mean we only have unique rows based on the code, but we keep all other variables .keep_all=TRUE. If you change to .keep_all=FALSE (which is the default) then all the other variables will be removed.
           
#chapter2prac代码
```

![摘自 Garrick Aden-Buie 的 Tidy 解释](https://andrewmaclachlan.github.io/CASA0005repo/prac2_images/left-join-extra.gif)

· 检查merge是否成功，显示前十行

```
shape%>%
  head(., n=10)

#chapter1的Join代码
```



------

#### Export data

将join好的data打包成geopackage（ .gpkg）

```
shape %>%
  st_write(.,"C:/Users/nekopi/OneDrive/CASA/21-22 term 1/0005 GIS/week1 homework/Rwk1.gpkg",
           "london_boroughs_fly_tipping",
           delete_layer=TRUE)
```

------

#### 编写自动脚本Script 

```
library(sf)
library(tmap) 
library(tmaptools)
library(RSQLite)
library(tidyverse)
#read in the shapefile

shape <- st_read(
  "C:/Users/nekopi/OneDrive/CASA/21-22 term 1/0005 GIS/week1 homework/statistical-gis-boundaries-london/ESRI/London_Borough_Excluding_MHW.shp")
# read in the csv
mycsv <- read_csv("C:/Users/nekopi/OneDrive/CASA/21-22 term 1/0005 GIS/week1 homework/fly_tipping_borough_1.csv")  
# merge csv and shapefile
shape <- shape%>%
  merge(.,
        mycsv,
        by.x="GSS_CODE", 
        by.y="Row Labels")
# set tmap to plot
tmap_mode("plot")
# have a look at the map
qtm(shape, fill = "2011_12")
# write to a .gpkg
shape %>%
  st_write(.,"C:/Users/nekopi/OneDrive/CASA/21-22 term 1/0005 GIS/week1 homework/Rwk1_01.gpkg",
           "london_boroughs_fly_tipping",
           delete_layer=TRUE)
# connect to the .gpkg
con <- dbConnect(SQLite(),dbname="C:/Users/nekopi/OneDrive/CASA/21-22 term 1/0005 GIS/week1 homework/Rwk1_01.gpkg")
# list what is in it
con %>%
  dbListTables()
# add the original .csv
con %>%
  dbWriteTable(.,
               "original_csv",
               mycsv,
               overwrite=TRUE)
# disconnect from it
con %>% 
  dbDisconnect()
```



OPTION+ENTER: 一行一行运行R命令





#### Q&A

![image-20211008125645962](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211008125645962.png)



![image-20211008125745631](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211008125745631.png)

或者，在Qgis里用tool- vector table-refactor fileds工具直接更改字段类型：

![image-20211008130008397](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211008130008397.png)

![image-20211008130104063](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211008130104063.png)



# wk2

### 数据处理

#### Data structures

![img](https://andrewmaclachlan.github.io/CASA0005repo/prac2_images/data_structures.png)

```
data.frame[row, column]
```

```
library(dplyr)

df <- df %>%
  dplyr::rename(column1 = Data1, column2=Data2)
  
  #使用dplyr重命名列。
```

```
df %>%
	dplyr::select(column1)
	
	#使用dplyr根据名称选择列
```

```
df$column1

#当某些spatial data（如raster data）不能使用dplyr时，想要实现↑效果的一种替代方式。

df[["column1"]]
#第三种引用的方法
```



#### janitor package：清理数据

```
library(janitor)

clean_names()

#默认功能为删除所有大写字母并用_代替空格

clean_names(., case="big_camel")
# ., means all data
# 该行命令指的是更改所有大写字母
```



#### dplyr package：将数据更改为合适的格式

包括：select(   ) ; filter(    ); summarise(    ); mutate(    )。

* 选择.csv文件中特定的列（select（）），根据一定条件筛选（filter（）），合计（summarize（）），根据已有的variables添加一个新的（mutate（））；简化输入（across（）），详见：https://willhipson.netlify.app/post/dplyr_across/dplyr_across/

  

* “<-" 符号意思为某个对象（objects）被分配了一个（或多个）值（value），类似于"="。

```
# 按特定的符号将混为一列的data分割为columns

```



```
county_only <- report %>%
#根据表report新建一个新的表：county_only

  clean_names() %>%
 #清理数据
 
 
select(county, test_subject, count_met_standard, 
         count_of_students_expected_to_test, grade_level)%>%
#选取括号中的列

  filter(county != "Multiple")%>%   #county列中筛选出所有不是“multiple”的（ != means don't select this, but select everything else）；
  filter(test_subject == "Science")%>%   #test_subject列中筛选出所有的“science”；
  filter(grade_level=="All Grades")%>%   #grade_levell列中筛选出所有的“ALL Grades”；
  group_by(county)%>%   #将多个values根据相同的county合并在一起
  
  summarise(total_county_met_standard=sum(count_met_standard, na.rm=T),
  total_county_to_test=sum(count_of_students_expected_to_test,na.rm=T))%>%
  
 #这里的值是每个学校的成绩，要获得整个县county的值，需要对count_met_standard列和count_of_students_expected_to_test列求和输出为total_xxxx。
 #na.rm=T means reomove all N/A columns.
 
  mutate(percent_met_per_county=(total_county_met_standard/total_county_to_test)*100)
  #用mutate功能添加一个叫percent_met_per_county的新变量。
  
  
  #以上为选择(select)需要的列，筛选（filter）列中特定的值，按相同的位置分类（group_by），合计(summarise)相同位置的总值，添加百分比的新变量（(mutate)）的全过程。
  #其中特殊的处理包括利用janitor功能清理name，filter(xx != oo)选择除了“oo”以外的值，去除所有空值（na.rm=T）.
  
```



```
#得到每个县的总值后需要计算所有县（即Washington_state）的总值

state_met<- sum(county_only$total_county_met_standard)   #全Washington_state成绩合格的人数=在county_only这个data_frame中选择名叫"total_county_met_standard"的列（column），并求和

state_test<- sum(county_only$total_county_to_test)   #全Washington_state参与考试的人数=在county_only这个data_frame中选择名叫”total_county_to_test“的列（column），并求和

state_that_met<- (state_met/state_test*100)    #全Washington_state的合格率
```



```
county_to_state_difference <- county_only %>%
  mutate(state_diff = percent_met_per_county-state_that_met)%>%
  mutate(across(state_diff, round, 1))
  
  #round用来将state_diff这一列的值四舍五入保留一位小数
  #(across(c(被选择出来的列1，被选择出来的列2…)，function1, function2，…))
  #例一：mutate(across(where(is.numeric), round, 3))
        #选择所有的数字列，四舍五入保留三位小数
  #例二：mutate(across(UKdiff, round, 0))%>%
        #选择UKdiff列，四舍五入保留整数
```



#### merge或join_data见wk1的join部分



#### Q

```
#what is the difference between mutate(across(where(is.numeric), round, 3)) and mutate(across(is.numeric, round, 3))

```



------

### Tidying data: messy data→tidy data

![This figure is taken directly from Grolemund and Wickham (2017) Chapter 12.Following three rules makes a dataset tidy: variables are in columns, observations are in rows, and values are in cells.](https://andrewmaclachlan.github.io/CASA0005repo/prac2_images/messy-tidy-ex.png)



一种在读取数据阶段就可以完成数据清理（tidying data）的方式：

```
flytipping1 <- read_csv("https://data.london.gov.uk/download/fly-tipping-incidents/536278ff-a391-4f20-bc79-9e705c9b3ec0/fly-tipping-borough.csv", 
                       col_types = cols(
                         code = col_character(),
                         area = col_character(),
                         year = col_character(),
                         total_incidents = col_number(),
                         total_action_taken = col_number(),
                         warning_letters = col_number(),
                         fixed_penalty_notices = col_number(),
                         statutory_notices = col_number(),
                         formal_cautions = col_number(),
                         injunctions = col_number(),
                         prosecutions = col_number()
                       ))
# view the data
view(flytipping1)
```

这样我们得到了带有小标题的变量列，每一行的首个都是col_type（在这个例子——flytipping中，这里是LondonBorough）

![image-20211017182729574](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211017182729574.png)

#### tidyverse package

· 使用pivot_longer( )功能实现↑功能

```
#convert the tibble into a tidy tibble
flytipping_long <- flytipping1 %>% 
  pivot_longer(
  cols = 4:11,
  names_to = "tipping_type",
  values_to = "count"
)

# view the data
view(flytipping_long)
```

<img src="C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211017183211054.png" alt="image-20211017183211054" style="zoom:67%;" />



· 使用pivot_wider( )功能将长表格变为宽表格

```
#pivot the tidy tibble into one that is suitable for mapping

flytipping_wide <- flytipping_long %>% 
  pivot_wider(
  id_cols = 1:2,
  names_from = c(year,tipping_type),
  names_sep = "_",
  values_from = count
)

view(flytipping_wide)
```

![image-20211017182830666](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211017182830666.png)



· 对特定某个变量（这里是year对total_incidents）生成表格

```
widefly <- flytipping2 %>% 
  pivot_wider(
  names_from = year, 
  values_from = total_incidents)
```

![image-20211017183731202](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211017183731202.png)

------

### 出图

tmap package

#### tmaptools package

· read_osm( ) 

   实现从osm中提取所需底图的功能

· st_box( )

   在底图周边建立边界框

```
bbox_county <- joined_data %>%
  st_bbox(.) %>% 
  tmaptools::read_osm(., type = "esri", zoom = NULL)
```



```
tm_shape(bbox_county)+
  tm_rgb()+
  tm_shape(joined_data) + 
  tm_polygons("state_diff", 
              style="pretty",
              palette="Blues",
              midpoint=NA,
              #title="Number of years",
              alpha = 0.5) + 
  tm_compass(position = c("left", "bottom"),type = "arrow") + 
  tm_scale_bar(position = c("left", "bottom")) +
  tm_layout(title = "County to state percent difference in meeting science standards", 
            legend.position = c("right", "bottom"))
            
            
#for more paletteoptions, run "palette_explorer" or "tmaptools::palette_explorer"

```

![image-20211017215046753](C:\Users\nekopi\AppData\Roaming\Typora\typora-user-images\image-20211017215046753.png)



# wk3

#### CRS（coordinate reference system） 介绍

Whilst we were able to identify the CRS of our layer using `print` another alternative is to find the `proj4` string. A `proj4` string is meant to be a compact way of identifying a coordinate reference system.

##### · proj4

The proj4-string basically tells the computer where on the earth to locate the coordinates that make up the geometries in your file and what distortions to apply (i.e. if to flatten it out completely etc.) It’s composed of a list of parameters seperated by a `+`. Here are projection `proj` uses latitude and longitude (so it’s a geographic not projected CRS). The `datum` is WGS84 that uses Earth’s centre mass as the coordinate origin (0,0).

The [Coordiante systems in R chapter by Gimond (2019)](https://mgimond.github.io/Spatial/coordinate-systems-in-r.html#understanding-the-proj4-coordinate-syntax) provides much more information on Proj4. However, i’d advise trying to use EPSG codes, which we come onto next.

1. Sometimes you can download data from the web and it doesn’t have a CRS. If any boundary data you download does not have a coordinate reference system attached to it (NA is displayed in the coord. ref section), this is not a huge problem — it can be added afterwards by **adding the proj4string to the file or just assigning an EPSG code**.

To find the proj4-strings for a whole range of different geographic projections, use the search facility at http://spatialreference.org/ or http://epsg.io/.



##### · ESPG代码

EPSG 代码是代表世界上所有坐标参考系统的短数字，并直接链接到 proj4 字符串。

WGS84 世界大地测量系统的 EPSG 代码（通常是大多数空间数据的默认 CRS）是 4326 — http://epsg.io/4326



##### · 下载的数据无coordinate reference system时的解决方法

```
#下载的数据无coordinate reference system时的解决方法
#st_set_crs(需要的EPSG代码)

Ausoutline <- st_read(here("prac3_data", "gadm36_AUS.gpkg"), 
                      layer='gadm36_AUS_0') %>% 
  st_set_crs(4326)
```

##### · reprojecting spatial data(更改空间数据坐标)

· 空间数据为 .sp格式时，直接转换（spatial point）

```
AusoutlinePROJECTED <- Ausoutline %>%
  st_transform(.,3112) 
  #3112是澳大利亚本地的EPSG代码

print(AusoutlinePROJECTED)
```

· 空间数据为 .sf格式时， .sf→ .sp，再进行转换（spatial feature）

```
#From sf to sp
AusoutlineSP <- Ausoutline %>%
  as(., "Spatial")

#From sp to sf
AusoutlineSF <- AusoutlineSP %>%
  st_as_sf()
```

##### · reprojecting raster data(更改栅格数据坐标)

```
# set the proj 4 to a new object
newproj<-"+proj=moll +lon_0=0 +x_0=0 +y_0=0 +ellps=WGS84 +datum=WGS84 +units=m +no_defs"
# get the jan raster and give it the new proj4
pr1 <- jan %>%
  projectRaster(., crs=newproj)
plot(pr1)

#返回WGS84
pr1 <- pr1 %>%
  projectRaster(., crs="+init=epsg:4326")
plot(pr1)
```



# wk6

##### Q&A	

1. ###### what is spatial sub setting?
	
	use dplyr
	select data without altering the original data.
	select by location
	
2. ###### what is attribute sub setting?
	
	use dplyr filter
	filter the specifitc data we want to analyse.
	select by attribute table

###### 3.how spatial sub setting different to spatial joining?

​	<- data you want to subset [data you want to subset by, columns. operator to subset]

left join, right join,inner join, full join

4. ###### when using 'spatstat' package what typy of spatial object do we need to use?
	
	ppp
	
5. ###### what test determines if there is an association between the observed and expected frequencies from quadrat analysis poisson distribution?
	
	chi- square
	p<0.05


6. ###### what advantages does Ripley's k have over quadrat analysis?
	
	less dependency on area selection
	
7. ###### what does dbscan tell us that  Ripley's k and quadrat can not?
	
	where the clusters are.
	
8. ###### how does spatial autocorrelation differ from point pattern analysis?
	
	spatial autocorrelation looks ar spatially continuous obcervationand their similarity of vualues, point pattern is just clustering of single, binary points.

9. ###### what is spatial weight matrix?
	
	a representation of the relations between the object(shape geometries) in your data.
	
10. ###### what is Moran's show us
	
	shows how similar surrounding objects(based on the weight matrix) are to the current one, with a value of 1 = clustered, 0= no pattern, -1= dispersed

