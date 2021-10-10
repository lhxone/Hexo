---
title: Matlab实验1-3
date: 2021-03-07 21:30:24
tags: Matlab
---

## Matlab计算圆周率

#### 欧拉级数
   > 收敛速度较慢
   > 形式较为雅观
<!-- more -->

```Euler.m```
```Matlab
format long;
t = 9e7;
d = 1;

while d >= 1e-10
    if d<=1e-9
        t = t + 100;
    else
        t = t + 100000;
    end
    mypi = sqrt(6 * EulerSum(t))
    d = abs(pi - mypi)
end

t    
```

```EulerSum.m```
```Matlab
function y = EulerFunc(n)
    sum = 1;

    for index = 2:n
        sum = sum + 1 / (index^2);
    end

    y = sum;
    return
end

```
另一种计算方式：

```Euler2.m```
```Matlab
format long;
t = 0;
d = 1;
s=0;

while d >= 1e-10
    t = t + 1;
    s=s+1/(t^2);
    mypi = sqrt(6 * s)
    d = abs(pi - mypi);
end

t
```
   
#### 拉马努金级数
   > 收敛速度极快
   > 只需计算一两步即可到达1e-8的精度
```Matlab
format long;
n = 0;
d = 1;
s = 0;
sqrt_int = (2 * sqrt(2)) / 9801;

while d >= 1e-8
    s = s + (factorial(4 * n) * (1103 + 26390 * n)) / ((factorial(n)^4) * ((396)^(4 * n)));
    mypi = (sqrt_int * s)^(- 1)
    d = abs(pi - mypi)
    n = n + 1;
end

n
```

#### 莱布尼茨公式



{% katex %}\frac{\pi}4=1-\frac13+\frac15-\frac17+\cdot\cdot\cdot{% endkatex %}



```Matlab
format long;
d = 1;
t = 0;
s = 0;
mypi = 0;

while d >= 1e-2
    s = s + ((-1)^t*(1/(2*t+1)));
    mypi = 4 * s
    d = abs(pi - mypi);
    t = t + 1;
end

t
```
---
<center>E N D</center>