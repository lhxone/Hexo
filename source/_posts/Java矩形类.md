---
title: Java矩形类
date: 2021-04-22 17:23:06
tags: Java
---


## 项目结构

```
$ tree -C -L 3
.
└── top
    └── lhxone
        ├── Main.java
        └── myRect

```

## 代码

```Main.java```

```Java
package top.lhxone;

import top.lhxone.myRect.PlainRect;

public class Main {

    public static void main(String[] args) {
        PlainRect r=new PlainRect(10,10,10,20);
        System.out.println("面积为"+r.area());
        if (r.isInside(25.5,13)){
            System.out.println("在矩形内.");
        }else {
            System.out.println("不在矩形内.");
        }
    }
}
```

```Rect.java```

```Java
package top.lhxone.myRect;

public class Rect {
    protected double width, height;

    Rect(double width, double height) {
        this.width = width;
        this.height = height;
    }

    public Rect() {
        this.width = 1.0;
        this.height = 1.0;
    }

    public double area() {
        return this.height * this.width;
    }

    public double perimeter(){
        return 2*(this.width+this.height);
    }

}
```

```PlainRect.java```

```Java
package top.lhxone.myRect;

public class PlainRect extends Rect {
    double startX, startY;

    PlainRect() {
        this.startX = 1.0;
        this.startY = 1.0;
        super.height = 1.0;
        super.width = 1.0;
    }

    public PlainRect(double X, double Y, double width, double height) {
        this.startX = X;
        this.startY = Y;
        super.width = width;
        super.height = height;
    }

    public boolean isInside(double x, double y) {
        if (x < startX + width && x > startX && y < startY && y > startY - height) {
            return true;
        } else {
            return false;
        }
    }
}
```