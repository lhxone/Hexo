---
title: Matlab实验1-2
date: 2021-03-4 14:32:58
tags: Matlab
---

## Matlab实验1-2

8. 测试矩阵的``` : ```操作，要求把测试的过程和结果放入实验报告.

```Matlab
A = randi(5, 3, 4)

A(5)
A(2, 3)%row=2,col=3
A(3, :)%row=3
A(:, 1) = [ ]%delet first col
```
>运算结果
```Matlab
>> TestColon    

A =

     4     1     4     3
     2     3     4     3
     5     2     1     4


ans =

     3


ans =

     4


ans =

     5     2     1     4


A =

     1     4     3
     3     4     3
     2     1     4
```
<!-- more -->
9. 测试字符串操作.

```Matlab
str = 'a+b,c+d,'
n = findstr(str, ',')%n1是第一个","(4),n2是第二个", "(8)
str1 = str(1:n(1))
str2 = str(n(1) + 1:n(2))
str3 = strrep(str1, ',', '*2')%str replace
a = 5
b = 2
c = 3
d = 4
eval(str1)%计算str1的值
eval(str3)%计算str3的值
str2 = upper(str2)
```
>运算结果
```Matlab
>> TestStr  

str =

    'a+b,c+d,'


n =

     4     8


str1 =

    'a+b,'


str2 =

    'c+d,'


str3 =

    'a+b*2'


a =

     5


b =

     2


c =

     3


d =

     4


ans =

     7


ans =

     9


str2 =

    'C+D,'
```

10.  已知{% katex %}f(n)=1+\frac{1}{3}+\frac{1}{5}+···+\frac{1}{2n-1}{% endkatex %}分别用```for```循环和```while```循环编写两个函数，并计算{% katex %}f(50){% endkatex %},{% katex %}f(100){% endkatex %},{% katex %}f(200){% endkatex %}的值.

```CalFunc.m```
```Matlab
function y=F(x)
    y=1/(2*x);
end
```

```Cal.m```
```Matlab
sum = 0;

for index = 1:50
    sum = sum + CalFunc(index);
end

disp('f(50)=')
sum
sum=0;

for index = 1:100
    sum = sum + CalFunc(index);
end

disp('f(100)=')
sum
sum=0;

for index = 1:200
    sum = sum + CalFunc(index);
end

disp('f(200)=')
sum
sum=0;
```

>运算结果

```Matlab
>> Cal
f(50)=

sum =

   2.249602669164712

f(100)=

sum =

   2.593688758819810

f(200)=

sum =

   2.939015474060723
```

11. 查阅测试excel文件的读取和写入函数xlsread,xlswrite，要求把测试的过程和结果放入实验报告.

```Matlab
num=xlsread("Book1.xls")
num(:,1)=[ ]
xlswrite("Book1.xls",num,'A5')
```
运行结果
```Matlab
>> Testxls

num =

     3     4     6     3     2
     5     9     7     5     3
     4     0     6     4     3


num =

     4     6     3     2
     9     7     5     3
     0     6     4     3

Warning: Could not start Excel server for export.
XLSWRITE will attempt to write file in CSV format. 
> In xlswrite (line 181)
  In Testxls (line 3) 
```

<table><tr><td bgcolor=yellow>出现了无法写入的问题，num被写入到了新建的Book1.csv，而非原有的Book1.xls。此问题仅存在于macOS，原因为macOS上的Office目前不支持COM接口，目前无解。</td></tr></table>

12.  自己编写矩阵左右翻转、上下翻转函数，不得使用Matlab已经编写好的左右翻转和上下翻转函数。给一个具体的矩阵并做测试翻转.

```Matlab
A = magic(4)
MatrixReserveFunc1(A)
MatrixReserveFunc2(A)
```

```MatrixReserveFunc1.m```
```Matlab
function MatrixReserveFunc1(A)
    [m, n] = size(A);

    for row = 1:m / 2
        temp = A(row, :);
        A(row, :) = A(m - row + 1, :);
        A(m - row + 1, :) = temp;
    end

    A
end
```

```MatrixReserveFunc2.m```
```Matlab
function MatrixReserveFunc2(A)
    [m, n] = size(A);

    for col = 1:n / 2
        temp = A(:, col);
        A(:, col) = A(:, n - col + 1);
        A(:, n - col + 1) = temp;
    end

    A
end
```
运行结果
```Matlab
>> MatrixReserve

A =

    16     2     3    13
     5    11    10     8
     9     7     6    12
     4    14    15     1


A =

     4    14    15     1
     9     7     6    12
     5    11    10     8
    16     2     3    13


A =

    13     3     2    16
     8    10    11     5
    12     6     7     9
     1    15    14     4
```

13. 编写函数，计算{% katex %}1!+2!+···+20!{% endkatex %}和{% katex %}1!+2!+···+30!{% endkatex %}

```TestFactorFunc.m```
```Matlab
function y = TestFactorFunc(n)
    fac = 1;
    s = 0;
    for index1 = 1:n

        for index2 = 1:index1
            fac = fac * index2;
        end

        s = s + fac;
        fac = 1;
    end

    y = s;
end
```

```TestFactor.m```
```Matlab
disp('Factor(20)=')
sum=TestFactorFunc(20)

disp('Factor(30)=')
sum=TestFactorFunc(30)
```
运行结果
```Matlab
>> TestFactor
Factor(20)=

sum =

   2.5613e+18

Factor(30)=

sum =

   2.7441e+32
```

14. 编写斐波那契（Fibonacci）数列的函数，列出数列前40个数.提示：可以递归调用，也可以把数列的数放入数组中存储.

```TestFib.m```
```Matlab
A=[ ]
for index = 1:40
    A=[A,TestFibFunc(index)];
end
A
```

```TestFibFunc.m```
```Matlab
function y = Fib(n)
    A(1) = 1;
    A(2) = 1;

    for i = 3:n
        A(i) = A(i - 1) + A(i - 2);
    end

    y = A(n);
    return
end
```
运行结果
```Matlab
>> TestFib

A =

     [ ]


A =

  Columns 1 through 9

           1           1           2           3           5           8          13          21          34

  Columns 10 through 18

          55          89         144         233         377         610         987        1597        2584

  Columns 19 through 27

        4181        6765       10946       17711       28657       46368       75025      121393      196418

  Columns 28 through 36

      317811      514229      832040     1346269     2178309     3524578     5702887     9227465    14930352

  Columns 37 through 40

    24157817    39088169    63245986   102334155
```

15. 编写课件中的猜数游戏，并做测试. 首先由计算机随机产生一个{% katex %}[1,100]{% endkatex %}之间的一个整数，然后由用户猜测所产生的这个数。根据用户猜测的情况给出不同的提示，如果猜测的数大于产生的数，则显示```High```，小于则显示```Low```，等于则显示```You won!```，同时退出游戏。用户最多有 10 次机会.

```Matlab
fprintf("游戏开始！\n请输入你猜的数\n")
num=randi(100)
t=10;
while t>0
    t=t-1;
    x=input('');
    if num==x
        fprintf("You won!\n")
        break
    else
        if x>num
            fprintf("High!")
        else
            fprintf("Low!")
        end
        fprintf("还剩%d次机会，请再次输入：",t);
    end
end
if t==0
    fprintf("机会为0，游戏结束!\n");
end
```
---

### <center>Next⬇️</center>

<center><a href="http://lhxone.top/2021/03/07/Matlab实验1-3/">Matlab练习1-3 🍺</a></center>