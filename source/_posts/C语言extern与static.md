---
title: C语言extern与static
date: 2021-04-13 18:04:38
tags: C++
---


## static

### 示例

```cpp
#include <iostream>

using namespace std;

void out() {
    static int n = 0;
    n++;
    cout << "n=" << n << endl;
}

int main() {
    out();      //第一次调用out()
    out();      //第二次调用out()
    out();      //第三次调用out()
}
```
<!-- more -->
### 运行结果

```
n=1
n=2
n=3

Process finished with exit code 0
```

### 解释

第一次out()被调用，申请一个空间用来存放n，执行一次初始化。第一次调用结束后，存储空间并未被释放，即生命周期延长了。第二/三次调用，不再申请新的存储空间，而是继续使用第一次的存储空间，但是不执行```n = 0;```的赋值，n的值仍然为原来的值。

## extern

> 注：定义要为变量分配内存空间；而声明不需要为变量分配内存空间。extern是声明，而非定义，即通知编译器，此处只是该变量（或函数，但是函数一般不使用extern）的声明，不会分配内存，而真正的定义在别处。

#### 定义时，extern可以省略。
#### 定义时，extern可以省略。
#### 定义时，extern可以省略。


### 示例

extern一般用于自定义的*.h文件中

```cpp
extern int a; // 声明一个全局变量 a
int a; // 定义一个全局变量 a
extern int a =0 ; // 定义一个全局变量 a 并给初值。
int a =0;    // 定义一个全局变量 a, 并给初值，
//第三和第四是一样的
```


```cpp
//demo.cpp
#include <iostream>
#include "demo.h"

using namespace std;

int main() {
    cout<<b;
}
```

```cpp
//demo.h
extern int a;
int b = 1;
extern int c = 2;
int d = 3;
```
输出a时，编译报错；输出b,c,d时，可以正常输出，但提示c被声名为extern,即此处为

```
[Warning] 'c' initialized and declared 'extern'
```

### 解释

声明：仅仅表示有一个叫张三的人，他是个医生，他在哪不知道，咱们找不到他

定义：在地球上给一个叫张三的医生分配了地理资源，让他在这一片地理区域内活动，这样就可以知道他在哪，咱们也可以找到他

假如我们有两个文件A和B，在A里，我们完成了对张三的定义，如果我们在B中想再请A中定义的张三为我们工作，那我们就加个extern。```extern 张三;```这样，程序就知道这个张三的定义在别的文件里，然后程序找到A文件，发现张三的定义，我们就可以找到张三，请他出山来为我们工作了
