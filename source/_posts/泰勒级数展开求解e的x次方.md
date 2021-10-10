---
title: 泰勒级数展开求解e的x次方
date: 2021-03-25 17:40:23
tags: Java
---


# 泰勒级数展开求解e的x次方
<!-- more -->
## 根据公式
{%katex%}e^x = \lim\limits_{n\to+\infty} 1 + x + \frac{x^2}{2!} + ··· +\frac{x^n}{x!}{%endkatex%}

```java
public class Main {
    public static double CalE(int x) {
        double ans = 1, s = x;
        for (int i = 2; i < 100; i++) {
            ans += s;
            s = s * x * 1.0 / i;
        }
        return ans;
    }

    public static void main(String[] args) {
        System.out.printf("%.8f", CalE(1));         //CalE(x)即为e的x次方
    }
}
```

### 加入判断条件

```java
import java.math.*;

public class Main {
    public static double CalE(int x) {
        double ans = 1, s = x;
        for (int i = 2; s > 1e-8; i++) {
            ans += s;
            s = s * x * 1.0 / i;
        }
        return ans;
    }

    public static void main(String[] args) {
        System.out.printf("%.8f", CalE(1));
    }
}
```