---
layout: post
title: matplotlib画图笔记
category: 笔记
tags: Python
keywords: 笔记
---

* content
{:toc}

## 属性
### 颜色

![](/assets/img/notes/plot_color.png)
### marker属性
用来定义点的形状
符号|解释
--|--
.|点标记
,|pixel marker
o|粗点标记
v|朝下的三角箭头
^|朝上的三角箭头
<|向左的实心箭头
\>|向右的实心箭头 
1|tri_down marker
2|tri_up marker
3|tri_left marker
4|tri_right marker
s|方块
p|五边形
*|\*标识
h|hexagon1 marker
H|hexagon2 marker
+|+标识
x|x的标识
D|diamond marker
d|thin_diamond marker
\||竖线标识
_|横线标识



## 画散点图
```
plt.scatter(x, y, s, c, marker)
```
### 属性
```
x: x轴坐标
y：y轴坐标
s：点的大小/粗细 标量或array_like 默认是 rcParams['lines.markersize'] ** 2
c: 点的颜色 
marker: 标记的样式 默认是 'o'
```

## 画线条
```
plt.plot(x,y)
```

### 属性

**linestyle**  
符号|解释  
--|--
\-|实线
\--|横线的虚线
\-.|线和点结合的虚线
:|点组成的虚线



