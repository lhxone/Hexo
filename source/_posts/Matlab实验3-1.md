---
title: Matlab实验3-1
date: 2021-04-22 17:39:00
tags: Matlab
---

## 矩阵特征多项式、特征值、特征向量，稀疏矩阵

1. 测试函数poly, poly2str, eig

```Matlab
clc;
clear;
format short g
A = [1, 2, 3; 4, 5, 6; 7, 8, 9];
p = poly(A)
poly2str(p, 'x')
d = eig(A)
[V, D] = eig(A)
A * V - V * D
```

2. 测试函数sparse, spdiags

```Matlab
A = [0, 0, 5, 0; 3, 0, 3, 0; 0, 0, 0, 1; 0, 4, 3, 0]
S1 = sparse(A)
full(S1)
```

## 专题实验（迭代法求解线性方程组）

1. 编写Jacobi迭代格式解线性方程组的函数；
2. 编写Gauss-Seidel迭代格式求解线性方程组的函数；
3. 建议用矩阵形式编写，也可以用原始格式。
4. 对下面四个例题进行实验

<table>
<tr>
    <td>(1){%katex%}
\left\{
\begin{aligned}
& 10x_1-x_2-2x_3=7.2\\
& -x_1+10x_2-2x_3=8.3\\
& -x_1-x_2+5x_3=4.2\\
\end{aligned}
\right.
{%endkatex%}</td>
    <td>(2){%katex%}
\left\{
\begin{aligned}
& 10x_1-x_2-2x_3=7.2\\
& -x_1+10x_2-2x_3=8.3\\
& -x_1-x_2+0.5x_3=4.2\\
\end{aligned}
\right.
{%endkatex%}</td>
</tr>
<tr>
    <td>(3){%katex%}
\left\{
\begin{aligned}
& x_1-9x_2-10x_3=1\\
& -9x_1+x_2+5x_3=0\\
& 8x_1+7x_2+x_3=4\\
\end{aligned}
\right.
{%endkatex%}</td>
    <td>(4){%katex%}
\left\{
\begin{aligned}
& 5x_1-x_2-3x_3=-1\\
& -x_1+2x_2+4x_3=0\\
& -3x_1+4x_2+15x_3=4\\
\end{aligned}
\right.
{%endkatex%}</td>
</tr>
</table>


### Jacobi

#### 源代码

```TestJacobi.m```
```Matlab
A1 = [10, -1, -2; -1, 10, -2; -1, -1, 5];
b1 = [7.2; 8.3; 4.2];
ans1 = myJacobi(A1, b1)

A2 = [10, -1, -2; -1, 10, -2; -1, -1, 0.5];
b2 = [7.2; 8.3; 4.2];
ans2 = myJacobi(A2, b2)

A3 = [1, -9, -10; -9, 1, 5; 8, 7, 1];
b3 = [1; 0; 4];
ans3 = myJacobi(A3, b3)

A4 = [5, -1, -3; -1, 2, 4; -3, 4, 15];
b4 = [-1; 0; 4];
ans4 = myJacobi(A4, b4)

```

```myJacobi.m```
```Matlab
function x = myJacobi(A, b, X0, mytol)

    % A为系数矩阵.
    % b为右端常向量.
    % X0为初始层，默认取0.
    % mytol表示允许误差, 要求默认值为1e-6.

    % 判断参数
    if nargin == 3
        mytol = 1.0e-6;
    elseif nargin == 2
        mytol = 1.0e-6;
        N = length(b); %解向量的维数
        X0 = zeros(N, 1); %迭代初始值
    elseif nargin < 2
        error('输入参数不足');
        return
    end

    % 判断系数矩阵是否是方阵
    [A_row, A_col] = size(A);
    [b_row, b_col] = size(b);

    if A_row ~= A_col
        error('系数矩阵不是方阵');
        x = zeros(A_row, 1);
        return
    end

    % 判断b是否为常向量
    if b_col ~= 1
        disp('b不是常向量');
        x = zeros(A_row, 1);
        return;
    end

    % 判断常向量是否匹配
    if A_row ~= b_row
        disp('系数矩阵与常向量不匹配');
        x = zeros(A_row, 1);
        return;
    end

    % 判断系数矩阵是否可逆
    if abs(det(A)) < mytol
        disp('系数矩阵不可逆');
        x = zeros(A_row, 1);
        return
    end

    D = diag(diag(A)); % 求A的对角矩阵
    L = -tril(A, -1); % 求A的下三角矩阵
    U = -triu(A, 1); % 求A的上三角矩阵
    B = D \ (L + U); % Jacobi迭代矩阵

    e = abs(eig(B)); % 求特征值，判断是否收敛

    if e > 1
        disp('不收敛')
        x = zeros(A_row, 1);
        return;
    end

    f = D \ b;
    x = B .* X0 + f;
    n = 1; % 迭代次数

    while norm(x - X0) >= mytol % 进行迭代
        X0 = x;
        x = B * X0 + f;
        n = n + 1;

    end

    x = x(:, 1);
end
```

#### 运行结果

```
>> TestJacobi

ans1 =

    1.1000
    1.2000
    1.3000


ans2 =

   24.5000
   24.6000
  106.6000

不收敛

ans3 =

     0
     0
     0


ans4 =

   -0.0984
   -1.1639
    0.5574
```


### Gauss-Seidel

#### 源代码

```TestGS.m```
```matlab
A1 = [10, -1, -2; -1, 10, -2; -1, -1, 5];
b1 = [7.2; 8.3; 4.2];
ans1 = myGS(A1, b1)

A2 = [10, -1, -2; -1, 10, -2; -1, -1, 0.5];
b2 = [7.2; 8.3; 4.2];
ans2 = myGS(A2, b2)

A3 = [1, -9, -10; -9, 1, 5; 8, 7, 1];
b3 = [1; 0; 4];
ans3 = myGS(A3, b3)

A4 = [5, -1, -3; -1, 2, 4; -3, 4, 15];
b4 = [-1; 0; 4];
ans4 = myGS(A4, b4)

```

```myGS.m```
```matlab
function x = myGS(A, b, X0, mytol)

    % A为系数矩阵.
    % b为右端常向量.
    % X0为初始层，默认取0.
    % mytol表示允许误差, 要求默认值为1e-6.
    % A为系数矩阵.
    % b为右端常向量.
    % X0为初始层，默认取0.
    % mytol表示允许误差, 要求默认值为1e-6.

    % 判断参数
    if nargin == 3
        mytol = 1.0e-6;
    elseif nargin == 2
        mytol = 1.0e-6;
        N = length(b); % 解向量的维数
        X0 = zeros(N, 1); % 迭代初始值
    elseif nargin < 2
        error('输入参数不足');
        return
    end

    % 判断系数矩阵是否是方阵
    [A_row, A_col] = size(A);
    [b_row, b_col] = size(b);

    if A_row ~= A_col
        error('系数矩阵不是方阵');
        x = zeros(A_row, 1);
        return
    end

    % 判断b是否为常向量
    if b_col ~= 1
        disp('b不是常向量');
        x = zeros(A_row, 1);
        return;
    end

    % 判断常向量是否匹配
    if A_row ~= b_row
        disp('系数矩阵与常向量不匹配');
        x = zeros(A_row, 1);
        return;
    end

    % 判断系数矩阵是否可逆
    if abs(det(A)) < mytol
        disp('系数矩阵不可逆');
        x = zeros(A_row, 1);
        return
    end

    %将矩阵分裂为A=D-L-U
    D = diag(diag(A));
    L = -tril(A, -1); %下三角
    U = -triu(A, 1); %上三角

    g = (D - L) \ b;
    B = (D - L) \ U;

    % TODO:判断收敛

    x = B * X0 + g;

    while norm(x - X0) >= mytol
        X0 = x;
        x = B * X0 + g;
    end

end
```

#### 运行结果

```
>> TestGS

ans1 =

    1.1000
    1.2000
    1.3000


ans2 =

   24.5000
   24.6000
  106.6000


ans3 =

  -Inf
  -Inf
   Inf


ans4 =

   -0.0984
   -1.1639
    0.5574
```