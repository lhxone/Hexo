---
title: C++学生成绩
date: 2021-04-13 23:23:43
tags:
---


### 代码

<!-- more -->

```c++
#include <iostream>
#include <math.h>

void func1(double stu[10][6]) {
    //计算各科平均分
    for (int i = 0; i < 10; ++i) {
        if (!i) {
            printf("编号\ts1\t\ts2\t\ts3\t\ts4\t\ts5\t\t平均成绩\n");
        }
        printf("%d\t", i + 1);
        for (int j = 0; j < 6; ++j) {
            printf("%.2lf\t", stu[i][j]);
        }
        printf("\n");
    }
}

void func2(double stu[10][6]) {
    //各科平均成绩
    double class_sum[5] = {0};
    for (int i = 0; i < 10; ++i) {
        for (int j = 0; j < 5; ++j) {
            class_sum[j] += stu[i][j];
        }
    }
    for (int i = 0; i < 5; ++i) {
        class_sum[i] /= 10;
    }
    printf("平均\t");
    for (int i = 0; i < 5; ++i) {
        printf("%.2lf\t", class_sum[i]);
    }
    printf("\n");
}

void func3(double stu[10][6]) {
    //最高分
    double max_num = -1;
    int max_row, max_col;
    for (int i = 0; i < 10; ++i) {
        for (int j = 0; j < 5; ++j) {
            if (stu[i][j] > max_num) {
                max_num = stu[i][j];
                max_row = i;
                max_col = j;
            }
        }
    }
    printf("最高分数为：%.2lf,科目为：s%d,学生编号为：%d\n", max_num, max_col + 1, max_row + 1);
}

void func4(double stu[10][6]) {
    //方差
    double Dx, Sigma1 = 0, Sigma2 = 0;
    for (int i = 0; i < 10; ++i) {
        Sigma1 += stu[i][5];
        Sigma2 += pow(stu[i][5], 2);
    }
    Dx = (Sigma2 / 10) - (pow(Sigma1 / 10, 2));
    printf("平均分方差为：%.2lf\n", Dx);
}

int main() {
    double stu[10][6] = {0};
    for (int i = 0; i < 10; ++i) {
        for (int j = 0; j < 5; ++j) {
            stu[i][j] = (random() % 40) + 50;
//            scanf("%lf", &stu[i][j]);
            stu[i][5] += stu[i][j];
        }
        stu[i][5] = stu[i][5] / 5;            //计算每个学生平均分
    }

    func1(stu);
    func2(stu);
    func3(stu);
    func4(stu);
}
```