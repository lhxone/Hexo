---
title: Matlab实验2-2
date: 2021-04-05 09:55:07
tags: Matlab
---

## Matlab实验2-1

>Matlab图形操作实验

---
## 第5讲例题：5-1，5-3，5-4,5-5,5-6，5-7，5-8,5-9，5-10,5-15


<!-- more -->
### 5-1：三维螺旋线 

```Matlab
t = 0:0.1:10 * pi;
x = t .* sin(t);
y = t .* cos(t);
z = t;
plot3(x, y, z, 'r * -.');
```

<center><img src="https://z3.ax1x.com/2021/04/05/cMtNJs.png" width="50%"></center>

### 5-3：meshgrid网格生成函数 

```Matlab
x = [1, 2, 3];
y = [4, 7, 9, 10];
[X, Y] = meshgrid(x, y)
```

>运行结果

```Matlab
X =

     1     2     3
     1     2     3
     1     2     3
     1     2     3


Y =

     4     4     4
     7     7     7
     9     9     9
    10    10    10
```


### 5-4：“墨西哥帽子”

```Matlab
x = -8:0.5:8;
y = -8:0.5:8;
[X, Y] = meshgrid(x, y);
r = sqrt(X.^2 + Y.^2) + eps;    %会出现r=0的情况，因此加一个eps,eps表示浮点数的最小单位
Z = sin(r) ./ r;
mesh(X, Y, Z)
```

<center><img src="https://z3.ax1x.com/2021/04/05/cMttij.png" width="50%"></center>

### 5-5：绘制等高线 meshc 

```Matlab
[X, Y] = meshgrid(-8:0.5:8);
r = sqrt(X.^2 + Y.^2) + eps;
Z = sin(r) ./ r;
meshc(X, Y, Z)
```

<center><img src="https://z3.ax1x.com/2021/04/05/cMtJoQ.png" width="50%"></center>

### 5-6：绘制边界面屏蔽 meshz 

```Matlab
[X, Y] = meshgrid(-8:0.5:8);
r = sqrt(X.^2 + Y.^2) + eps;
Z = sin(r) ./ r;
meshz(X, Y, Z)
```

<center><img src="https://z3.ax1x.com/2021/04/05/cMtGdg.png" width="50%"></center>

### 5-7：画图{% katex %}z=x^2+y^2,(-3 \le x,y \le 3){% endkatex %},{% katex %}z=x^2-3y^2,(-10 \le x,y \le 10){% endkatex %}.

```Matlab
[X1, Y1] = meshgrid(-3:0.1:3);
Z1 = X1.^2 + Y1.^2;
subplot(2, 1, 1);
mesh(X1, Y1, Z1), title('z = x^2 + y^2');
[X2, Y2] = meshgrid(-10:0.1:10);
Z2 = X2.^2 - 3 * Y2.^2;
subplot(2, 1, 2)
mesh(X2, Y2, Z2), title('z = x^2 - 3y^2马鞍面')

```

<center><img src="https://z3.ax1x.com/2021/04/05/cMtazq.png" width="50%"></center>



### 5-8：ezmesh、ezsurf 函数

```Matlab
ezmesh('-cos(sqrt(x^2+y^2))', [-4, 4], [-4, 4])
figure
ezsurf('(6+2*cos(u))*cos(v)', '(6+2*cos(u))*sin(v)', '2 * sin(u)', [0, 2 * pi, 0, 2 * pi])
hold on
ezsurf('2*sin(u).*cos(v)', '2*sin(u).*sin(v)', '2*cos(u)', [0, pi, 0, 2 * pi])
axis equal
hold off
```
<center><img src="https://z3.ax1x.com/2021/04/05/cMNifs.png" width="50%"></center>
<center><img src="https://z3.ax1x.com/2021/04/05/cMNkpn.png" width="50%"></center>


### 5-9：画图{%katex%}x^2+y^2+z^2=1{%endkatex%}和{%katex%}x^2+y^2+z^2=4{%endkatex%}的图像.

```Matlab
v = [-2 2 -2 2 -2 2];
subplot(1, 2, 1), sphere(30), axis(v);
title('半径为1的球面');
subplot(1, 2, 2), [x, y, z] = sphere(30);
surf(2 * x, 2 * y, 2 * z);
title('半径为2的球面');
```

<center><img src="https://z3.ax1x.com/2021/04/05/cMNAlq.png" width="75%"></center>

### 5-10：柱面作图

```Matlab
subplot(1, 3, 1), cylinder;
subplot(1, 3, 2), cylinder([3, 2]);
subplot(1, 3, 3), t = -pi:pi / 10:pi;
[x, y, z] = cylinder(1 + sin(t));
surf(x, y, z)
```

<center><img src="https://z3.ax1x.com/2021/04/05/cMNCkQ.png" width="75%"></center>

### 5-15：hidden透视控制

```Matlab
[x, y, z] = sphere(50);
x1 = 2 * x;
y1 = 2 * y;
z1 = 2 * z;
surf(x, y, z);
shading interp;
hold on
mesh(x1, y1, z1), colormap(hot)
hidden off;
axis equal;
```

<center><img src="https://z3.ax1x.com/2021/04/05/cMNPYj.png" width="50%"></center>

---


### <center>Next⬇️</center>

<center><a href="http://lhxone.top/2021/04/06/Matlab实验2-3/">Matlab练习2-3 🍺</a></center>