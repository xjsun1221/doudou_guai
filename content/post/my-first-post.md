---
title: "双分类变量的箱线图"
date: 2020-04-08T20:57:54+08:00
draft: false
categories: [test]
tags: ["test"]
authors: [xijie]
---

### 背景基础
箱线图的解释：
![](https://upload-images.jianshu.io/upload_images/9475888-aa7fbc515459caa1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

添加p值的需求
![](https://upload-images.jianshu.io/upload_images/9475888-e9f56292abafb88e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这样的箱线图，添加p值的需求是很多见的，核心就是stat_compare_means函数，可以很轻易搜到代码：
```
library(ggpubr)my_comparisons <- list( c("0.5", "1"), c("1", "2"), c("0.5", "2") )
ggboxplot(ToothGrowth, x = "dose", y = "len",
          color = "dose", palette = "jco")+ 
  stat_compare_means(comparisons = my_comparisons)+ 
  stat_compare_means(label.y = 50)  
```

### 加难度
如题，有的箱线图不止一个分组，下面的例子中，横坐标按照cyl列映射，填充颜色按照am列映射，即可得到展示双分类的箱线图。

![作图数据](https://upload-images.jianshu.io/upload_images/9475888-5f69830842851792.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
library(ggplot2)
library(dplyr)
x = mtcars %>%
  mutate_at(vars(am, cyl), as.factor)
p <- ggplot(x,aes(am, disp, fill=cyl))+
  geom_boxplot()+theme_classic()
p1 = p + stat_compare_means(aes(group = am),label = "p.format")
p2 = p + stat_compare_means(aes(group = cyl), label = "p.format")
library(patchwork)
p1+p2
```

![](https://upload-images.jianshu.io/upload_images/9475888-26e26d3ac55549bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
此图添加p值，**可根据am分组，也可根据cyl分组**，计算出的p值是不同的。
本想继续探索如何给分组内部添加比较连线，却发现stat_compare_means的comparisons参数无法支持这样的操作，一个画图爱好者抱着执念搜索了好久，没有找到直接添加的操作，只能是通过**分面**了。

```
p <- ggplot(x,aes(cyl, disp, fill=cyl))+
  facet_wrap(~am)+
  geom_boxplot()+theme_classic()+
  stat_compare_means(comparisons = list(c("4","6"),c("6","8")));p
```

![](https://upload-images.jianshu.io/upload_images/9475888-d2e3febcd64532d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)