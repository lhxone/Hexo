---
title: Java三角形类
date: 2021-04-01 17:11:43
tags: Java
---


## Java三角形类

### 已实现的功能：
- 计算各角度值
- 计算周长、面积
- 判断是否可以构成三角形

<!-- more -->

```Java
class Triangle {
    double[] len;
    double[] degree;
    private double area, cir;

    Triangle(double num1, double num2, double num3) {
        if (num1 + num2 <= num3 || num1 + num3 <= num2 || num2 + num3 <= num1) {
            System.out.println("无法构成三角形");
            len = null;
            return;
        }

        len = new double[3];
        degree = new double[3];
        len[0] = num1;
        len[1] = num2;
        len[2] = num3;
        cir = len[0] + len[1] + len[2];
        double p = cir / 2;
        area = Math.sqrt(p * (p - len[0]) * (p - len[1]) * (p - len[2]));   //求面积
        double a = Math.pow(len[0], 2), b = Math.pow(len[1], 2), c = Math.pow(len[2], 2);
        degree[0] = Math.toDegrees(Math.acos((a + b - c) / (2 * len[0] * len[1])));
        degree[1] = Math.toDegrees(Math.acos((a + c - b) / (2 * len[0] * len[2])));
        degree[2] = Math.toDegrees(Math.acos((b + c - a) / (2 * len[1] * len[2])));
    }

    double getArea() {
        return area;
    }

    double getCir() {
        return cir;
    }

}

public class Main {

    public static void main(String[] args) {
        Triangle t = new Triangle(3, 4, 5);
        System.out.println("三角形的面积为：" + t.getArea() + "," + "周长为：" + t.getCir());
        System.out.println("三角度数为：" + t.degree[0] + "," + t.degree[1] + "," + t.degree[2]);
    }
}
```