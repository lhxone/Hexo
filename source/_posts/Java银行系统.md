---
title: Java银行系统
date: 2021-04-10 12:25:15
tags: Java
---


## Java银行系统

### 实现的功能

- 继承抽象类```people```，创建```customer```类
- 使用构造函数对信息初始化
- 重载customer的toString方法
- 实现存款、取款、注册新卡和删除卡片等操作

<!-- more -->

### 代码实例


```Java
abstract class people {
    protected String name;
    protected String gender;
    protected String uid;
    protected int age;

    people(String name, String gender, int age, String uid) {
        this.name = name;
        this.gender = gender;
        this.age = age;
        this.uid = uid;
    }
}

class customer extends people {
    protected String[] CardID = new String[3];
    protected String[] password = new String[3];
    protected double[] remain = new double[3];
    protected int CardNum = 0;

    customer(String name, String gender, int age, String uid) {
        super(name, gender, age, uid);
    }

    //TODO:USER Operation
    @Override
    public String toString() {
        return "客户名字为：" + super.name + "，身份证为：" + super.uid + "，卡片数量为：" + this.CardNum;
    }

    void showUSERinfo() {
        System.out.printf("客户名字为：%s，性别为：%s，身份证为：%s，卡片数量为：%d\n", super.name, super.gender, super.uid, this.CardNum);
        if (CardNum != 0) {
            System.out.println("--编号--CardID--Password------------Remain");
            for (int i = 0; i < CardNum; i++) {
                System.out.println("\t" + i + "\t" + CardID[i] + "\t" + password[i] + "\t\t" + remain[i]);
            }
        }
    }


    //TODO:Card Operation
    void NewCard(String ID, String password, int remain) {
        if (this.CardNum < 3) {
            this.CardID[CardNum] = ID;
            this.password[CardNum] = password;
            this.remain[CardNum] = remain;
            System.out.printf("卡片创建成功！\n卡号为：%s，密码为%s。\n", this.CardID[CardNum], this.password[CardNum]);
            CardNum++;
        } else {
            System.out.println("卡片数量已满，无法申请新卡片！");
        }
    }

    void DelCard(String ID, String password) {
        if (CardNum == 0) {
            System.out.println("没有可用的卡！");
        } else {
            for (int i = 0; i < CardNum; i++) {
                if (CardID[i] == ID) {
                    if (this.password[i] == password) {
                        for (int j = i; j < CardNum - 1; j++) {
                            CardID[j] = CardID[j + 1];
                            this.password[j] = this.password[j + 1];
                            remain[j] = remain[j + 1];
                        }
                        CardNum--;
                        System.out.println("卡片删除成功！");
                    } else {
                        System.out.println("密码不正确！");
                    }
                    return;
                }
            }
        }
    }

    void getMoney(String ID, String password, double moneyNum) {
        for (int i = 0; i < CardNum; i++) {
            if (CardID[i] == ID) {
                if (this.password[i] == password) {
                    if (moneyNum <= remain[i]) {
                        remain[i] -= moneyNum;
                        System.out.println("取款成功！已在" + CardID[i] + "取出" + moneyNum + "，账户还剩：" + remain[i]);
                    }
                } else {
                    System.out.println("密码不正确！");
                }
                return;
            }
        }
        System.out.println("未找到此卡片，请检查输入的卡号！");
    }

    void saveMoney(String ID, int moneyNum) {
        if (moneyNum <= 0) {
            System.out.println("无效操作！");
            return;
        }
        for (int i = 0; i < CardNum; i++) {
            if (CardID[i] == ID) {
                remain[i] += moneyNum;
                System.out.println("存款成功！已在" + CardID[i] + "存入" + moneyNum + "，账户还剩：" + remain[i]);
                return;
            }
        }
        System.out.println("未找到此卡片，请检查输入的卡号！");
    }

}

public class Main {

    public static void main(String[] args) {
        customer cust;
        cust = new customer("张三", "男", 23, "12134");
        cust.NewCard("0001", "thisisapassword", 1000);
        cust.NewCard("0002", "anotherpassword", 1200);
        cust.NewCard("0003", "isthirdpassword", 4200);
        cust.NewCard("0004", "isatestpassword", 3600);
//        System.out.println(cust.toString());
        cust.showUSERinfo();
        cust.getMoney("0001", "thisisapassword", 300);
        cust.showUSERinfo();
        cust.DelCard("0002", "anotherpassword");
        cust.showUSERinfo();
        cust.saveMoney("0001", 200);
        cust.showUSERinfo();
    }
}
```