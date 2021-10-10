---
title: Java—abstract类与继承
date: 2021-03-31 11:06:01
tags: Java
---

## 抽象类


- ```abstract```类不能使用```new```建立对象，只能作为父类
- 抽象方法中没有方法体，包含抽象方法的类一定要为抽象类
- ```abstract```方法必须在子类中重写

<!-- more -->

```Java
abstract class shape {
    protected String name;
    double s, v;

    public abstract String getName();
}

class ball extends shape {
    private double r;

    public String getName() {
        return ball.super.name;
    }

    ball(double r, String name) {
        this.r = r;
        this.name = name;
        s = 4 * Math.PI * Math.pow(r, 2);
        v = 4 * Math.PI * Math.pow(r, 3) / 3;
    }

    public double getVolume() {
        return v;
    }
}

public class Main {
    public static void main(String[] args) {
        ball c = new ball(2, "MyBall");
        System.out.println(c.getName() + "\t" + c.getVolume());
    }
}
```

## 多态

```Java
public class Test {
    public static void main(String[] args) {
      show(new Cat());  // 以 Cat 对象调用 show 方法
      show(new Dog());  // 以 Dog 对象调用 show 方法
                
      Animal a = new Cat();  // 向上转型  
      a.eat();               // 调用的是 Cat 的 eat
      Cat c = (Cat)a;        // 向下转型  
      c.work();        // 调用的是 Cat 的 work
  }  
            
    public static void show(Animal a)  {
      a.eat();  
        // 类型判断
        if (a instanceof Cat)  {  // 猫做的事情 
            Cat c = (Cat)a;  
            c.work();  
        } else if (a instanceof Dog) { // 狗做的事情 
            Dog c = (Dog)a;  
            c.work();  
        }  
    }  
}
 
abstract class Animal {  
    abstract void eat();  
}  
  
class Cat extends Animal {  
    public void eat() {  
        System.out.println("吃鱼");  
    }  
    public void work() {  
        System.out.println("抓老鼠");  
    }  
}  
  
class Dog extends Animal {  
    public void eat() {  
        System.out.println("吃骨头");  
    }  
    public void work() {  
        System.out.println("看家");  
    }  
}
```