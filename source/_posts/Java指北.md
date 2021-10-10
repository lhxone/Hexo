---
title: Java指北
date: 2021-03-11 16:53:26
tags: Java
---

### Java指北

1. Java的工作方式
   * 安装JDK
   * 确认```java```与```javac```添加到环境变量中
   * 新建```name.java```，并使用文本编辑器进行编辑(推荐使用NotePad++或者是SublimeText)
   * 在命令行中使用```javac```对name.java进行编译，并使用```java```命令运行生成的name.class(注意路径是否正确)
   <center><table><tr><td bgcolor=yellow>编译前记得先保存</td></tr></table></center>
<!-- more -->
2. 注意的事情
   * 文件名与主类名相同，比如```demo.java```对应的主类应为```public class java{}```
   * 在主类中，应该有```main```方法作为程序开始的入口，若没有```main```方法，可以通过编译但无法运行
   * ```main```方法中必须有```String```类型的字符串数组,数组名无所谓，但习惯上使用```String[] args```
   * ```String```与```System```等关键字大小写敏感
   * ```String[] array;```与```String array[];```都是可以的
   * ```boolean```与```int```本身不支持强制转换，因此结果为非```boolean```的表达式放入```if```或者是```while```中会报错
   * Java中的运算符```%```为取余，即结果与左操作符具有相同的符号，这一点与```C++```相同，举个栗子
   ```Java
   System.out.println(-4%-3);
   System.out.println(4%-3);
   System.out.println(-4%3);
   ```
   >运行结果
   ```text
   -1
   1
   -1
   ```
   取模的运算结果在不同的环境下具有不同的意义，举一个```Matlab```的栗子
   ```M
   >> mod(-4,-3)

    ans =

        -1

   >> mod(-4,3) 

    ans =

         2

   >> mod(4,-3)

    ans =

        -2

   ```

3. 一些小技巧
   * Java提供了一种主要用于数组的增强型```for```循环
   ```java
    public class demo {
        public static void main(String args[]){
           int [] numbers = {10, 20, 30, 40, 50};
     
           for(int x : numbers ){
              System.out.print( x );
              System.out.print(",");
           }
           System.out.print("\n");
           String [] names ={"James", "Larry", "Tom", "Lacy"};
           for( String name : names ) {
              System.out.print( name );
              System.out.print(",");
            }
        }
    }
   ``` 
   >运行结果为
   ```text
    10,20,30,40,50,
    James,Larry,Tom,Lacy,
   ```


---
<center>持续更新中🍺</center>