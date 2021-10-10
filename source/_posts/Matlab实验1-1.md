---
title: Matlab实验1-1
date: 2021-03-3 13:27:52
tags: Matlab
---

## Matlab实验1-1



1. 编写Script命令文件，画出振荡曲线{% katex %}y =e ^ {- \frac{b}{3}}sin3t{% endkatex %}及其包络线{% katex %}y_0=e^{- \frac{t}{3}} {% endkatex %}，其中t的取值范围是{% katex %}[0,\pi]{% endkatex %}.

```Matlab
clc;
clear;
t=0:pi/50:4*pi;
y=exp(-t/3).*sin(3*t);
y0=exp(-t/3);
plot(t,y,'r',t,y0,':b',t,-y0,':b');
```
<!-- more -->
<center><img src="https://s3.ax1x.com/2021/03/13/6wN3FK.jpg" width="50%"></center>

2. 编写函数并做调用测试，其中{% katex %}f(x)=\begin{cases}
lnx + 1 &\text{, } x>1 \\
\sqrt {2x} &\text{, } 0 < x \leqslant 1 \\
|x| &\text{, } x \leqslant 0 \\
\end{cases}{% endkatex %}


```Matlab
function y = MyFun(x)
if x > 1
    y = ln(x) + 1;
elseif x > 0 && x <= 1
    y = sqrt(2 * x);
else
    y = abs(x);
end
```

3. 计算表达式{% katex %}e^{10} + 20^2 \log_{2} {5} \div tan20{% endkatex %}的值

```Matlab
format long;
exp(10) + (20^2 * log2(5)) / tan(20)
```

>运算结果

```Matlab
>> MyCalculate  

ans =

     2.244162199550501e+04
```

4. 编写函数{% katex %} myf(x)= tan{(-x^2)}arccos{(x)}{% endkatex %}，并测试{% katex %}myf(0.25){% endkatex %}，{% katex %}myf(0.78){% endkatex %}的值.

```Matlab
function y=myf(x)
    y=tan(-(x^2))*acos(x);
end
```
>运算结果

```Matlab
>> myf(0.25)    

ans =

  -0.082489690911370

>> myf(0.78)    

ans =

  -0.470951904834096
```

5. 测试矩阵的一些生成和操作函数，如diag, magic, zeros, ones, eye, tril, triu, rand, 要求把测试的过程和结果放入实验报告.

```略```

6. 测试数组的生成函数，如from:step:to,linspace，要求把测试的过程和结果放入实验报告.

```Matlab
>> A=2:2:100
A =
  Columns 1 through 21
     2     4     6     8    10    12    14    16    18    20    22    24    26    28    30    32    34    36    38    40    42
  Columns 22 through 42
    44    46    48    50    52    54    56    58    60    62    64    66    68    70    72    74    76    78    80    82    84
  Columns 43 through 50
    86    88    90    92    94    96    98   100
>> B=linspace(5,100,20)
B =
     5    10    15    20    25    30    35    40    45    50    55    60    65    70    75    80    85    90    95   100
```

7. 测试矩阵或数字的点运算，要求把测试的过程和结果放入实验报告.
   
```Matlab
A = randi(5, 3, 4) %randi(max,row,col)
B = magic(4)
C = eye(3, 4)

D = A * B
E = A .* C
```
>运算结果
```Matlab
>> TestCalculate

A =

     4     2     4     1
     4     4     1     1
     4     1     2     5


B =

    16     2     3    13
     5    11    10     8
     9     7     6    12
     4    14    15     1


C =

     1     0     0     0
     0     1     0     0
     0     0     1     0


D =

   114    72    71   117
    97    73    73    97
   107   103   109    89


E =

     4     0     0     0
     0     4     0     0
     0     0     2     0
```

---


### <center>Next⬇️</center>

<center><a href="https://lhxone.top/2021/03/04/Matlab实验1-2/">Matlab练习1-2 🍺</a></center>