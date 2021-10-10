---
title: Matlab实验2-1
date: 2021-04-01 11:32:28
tags: Matlab
---

## Matlab实验2-1

>Matlab图形操作实验

---


## 第4讲例题：4-1，4-2，4-5，4-6，4-7

<!-- more -->
### 4-1：画{% katex %}y=sin(x+3){% endkatex %}和{% katex %}y=exp(sin(x)){% endkatex %}的图像. 

```Matlab
x = -2 * pi:0.1:2 * pi;
y1 = sin(x + 3);
y2 = exp(sin(x));
plot(x, y1, '-*', x, y2, ':o')  %'-'表示实线，'*'表示以*标注；'：'表示点线，'o'表示用圆圈标出数据点
```

<center><img src="https://z3.ax1x.com/2021/04/05/cM8hVJ.png" width="50%"></center>


### 4-2：用不同标度在同一坐标绘制曲线{% katex %}y_{1}=200e^{-0.05x}sinx{% endkatex %}和{% katex %}y_{2}=0.8e^{-0.5x}sin10x{% endkatex %}

```Matlab
x = 0:0.1:20;
y1 = 200 * exp(-0.05 * x) .* sin(x); %要用.*
y2 = 0.8 * exp(-0.5 * x) .* sin(10 * x);
plot(x, y1, x, y2); %plot只有一个坐标轴，plotyy则有两个坐标轴

legend('y_1=200e^{-0.05x}sinx', 'y_2=0.8e^{-0.5x}sin10x'); %LaTex
```

<center><img src="https://z3.ax1x.com/2021/04/05/cM84a9.png" width="50%"></center>

### 4-5：绘制{% katex %}r=sint*cost{% endkatex %}的极坐标图.

```Matlab
t = 0:pi / 50:2 * pi;
r = sin(t) .* cos(t);
polar(t, r, 'dc');      %d表示菱形，c表示青色
```

<center><img src="https://z3.ax1x.com/2021/04/05/cM855R.png" width="50%"></center>

### 4-6：subplot划分画图区域

```Matlab
%将一个绘图窗口分割成m*n个子区域，并按行从左至右依次编号.p表示第p个绘图子区域.
%subplot('Position',[left,bottom,width,height])
x = -2:0.2:2;
y1 = x + sin(x);
y2 = sin(x) ./ x;
y3 = (x.^2);
subplot(2, 2, 1), plot(x, y1, 'm.'); %'m'表示洋红，'.'表示用点标出数据点
subplot(2, 2, 2), plot(x, y2, 'rp'); %'r'表示红色，'p'表示五角星
subplot('position', [0.2, 0.05, 0.6, 0.45]), plot(x, y3)
```

<center><img src="https://z3.ax1x.com/2021/04/05/cM8Wb4.png" width="50%"></center>

### 4-7：legend 添加图例 

```Matlab
x = 0:0.2:2 * pi;
hold on
plot(x, cos(x), 'r+:');
plot(x, sin(x), 'bd-.');
plot(x, 2 * cos(x), 'kh-');
legend('cos(x)', 'sin(x)', '2cos(x)')
xlabel('x');
ylabel('y');
title('几种三角函数图像比较')
text(0.7, 1.7, '\leftarrow 2cos(x)');
text(2.7, 0.6, '\leftarrow sin(x)');
text(2.2, -0.5, '\leftarrow cos(x)')
hold off
gtext('真好!')
```

<center><img src="https://z3.ax1x.com/2021/04/05/cMJwhn.jpg" width="50%"></center>

---


### <center>Next⬇️</center>

<center><a href="http://lhxone.top/2021/04/05/Matlab实验2-2/">Matlab练习2-2 🍺</a></center>