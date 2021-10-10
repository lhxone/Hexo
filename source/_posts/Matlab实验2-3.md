---
title: Matlab实验2-3
date: 2021-04-06 13:47:09
tags: Matlab
---


## 专题实验（分段函数的图像）

1. 请画出以下分段函数在[-10,40]上的图像，每个分段用不同的颜色、线型来描述.


{% katex %}
f(x)=
\left \{
    \begin{aligned}
    & x,&  & x<1, \\
    & 2x-1,& & 1 \le x \le 10, \\
    & 3x-11,& & 10 < x \le 30,\\
    & sinx+lnx,& & x > 30.
\end{aligned}
\right.
{% endkatex %}

```Matlab
x = -10:0.1:40;
x1 = x(1:110);
x2 = x(111:201);
x3 = x(202:401);
x4 = x(402:501);

for index = 1:numel(x1)
    y1(index) = x1(index);
end

for index = 1:numel(x2)
    y2(index) = 2 * x2(index) - 1;
end

for index = 1:numel(x3)
    y3(index) = 3 * x3(index) - 11;
end

for index = 1:numel(x4)
    y4(index) = sin(x4(index)) + log(x4(index));
end

plot(x1, y1, 'r*', x2, y2, 'bd', x3, y3, 'gH', x4, y4, 'y^')

```
<!-- more -->
>运行结果

<center><img src="https://z3.ax1x.com/2021/04/01/cEbH1K.png" width="50%"></center>


1. 选做题：针对1中的函数f编写函数文件，使得可以直接对矩阵或者数组进行运算，进而快速实现画图.

```TestFunc.m```

```Matlab
function y = TestFunc(x)
    if x<1
        y=x;
    elseif x<=10
        y=2*x-1;
    elseif x<=30
        y=3*x-11;
    else
        y=sin(x)+log(x);
    end
end
```

---

## 专题实验（二维极坐标画图）

从第2次实验附件中至少挑选2个图像进行绘图。

1. Archimedes螺线{%katex%}r = a\theta{%endkatex%}

```Matlab
theta = 0:pi / 10:4 * pi;
a = 3;
r = a * theta;
polar(theta, r)
```

>运行结果

<center><img src="https://z3.ax1x.com/2021/04/01/cEbclT.png" width="50%"></center>

2. 双曲螺线{%katex%}r\theta = a{%endkatex%}

```Matlab
theta = 0:pi / 10:4 * pi;
a = 3;
r = a ./ theta;
polar(theta, r)
```

>运行结果

<center><img src="https://z3.ax1x.com/2021/04/01/cELk26.png" width="50%"></center>

---
<center>E N D</center>