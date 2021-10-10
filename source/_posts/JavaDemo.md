---
title: JavaDemo
date: 2021-03-10 20:35:19
tags: 
- Java
- demo
---

### Java Demo


1. 一个Java的Demo，用以显示一个表格

```Java
import java.awt.*;
import javax.swing.*;
public class TableDemoMVC extends JFrame{
    TableDemoMVC(){
        init();
    }
    protected void init(){
        Container ct;
        final String[] columnNames={"姓名","职位","电话","月薪","婚否"};

        final Object[][] data={
                {"王东","总经理","0191123823",new Integer(5000),new Boolean(false)},
                {"李宏","秘书","0191123824",new Integer(3500),new Boolean(true)},
                {"李瑞","开发","0191123825",new Integer(4500),new Boolean(false)},
                {"赵新","保卫","0191123826",new Integer(2000),new Boolean(true)},
                {"陈理","销售","0191123827",new Integer(4000),new Boolean(false)},
        };

        JTable table=new JTable(data,columnNames);
        this.setSize(new Dimension(500,230));
        JScrollPane jp=new JScrollPane(table);
        ct=getContentPane();
        ct.add(jp);
    }
    public static void main(String[] args) throws ClassNotFoundException,
            InstantiationException,
            IllegalAccessException,
            UnsupportedLookAndFeelException{
        try {

            UIManager.setLookAndFeel("javax.swing.plaf.metal.MetalLookAndFeel");
        }
        catch (Exception e) {
            System.out.println("Look and Feel not set");
        }
        TableDemoMVC frame=new TableDemoMVC();
        frame.setVisible(true);
    }
}
```

<!-- more -->

2. 一个Java的Demo，用以显示一带有背景的窗口

```Java
import java.awt.*;
import java.awt.event.*;
import java.util.Scanner;
import javax.swing.JFrame;

public class Main extends JFrame {
    public Main() {
        setSize(1000, 650);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
    }
    public void paint(Graphics g) {
        g.setColor(Color.white);
        Image img = Toolkit.getDefaultToolkit().getImage("test.jpg");   //用你的图片加以替换
        g.drawImage(img, 0, 0, this.getWidth(), this.getHeight(), Color.LIGHT_GRAY, this);
        g.fillRoundRect(120, 120, 300, 100, 40, 40);
        g.setColor(Color.CYAN);
        g.drawRoundRect(100, 100, 300, 100, 40, 40);
        g.setFont(new Font("Helvetica", Font.PLAIN, 30));
        g.setColor(Color.CYAN);
        g.drawString("Hello world!", 150, 150);
    }

    public static void main(String[] args) {
        Frame f = new Main();
        Button b = new Button("一个按钮");
        Label l = new Label("标签");
        b.setBounds(100, 250, 100, 30);
        b.setFont(new Font("仿宋", Font.PLAIN, 10));
        l.setBounds(100, 200, 60, 30);
        f.add(l);
        f.add(b);
        f.setSize(1000, 650);
        f.setResizable(false);
        f.setLayout(null);
        f.setVisible(true);
        b.addActionListener(new ActionListener() {
            int count = 0;

            public void actionPerformed(ActionEvent e) {
                Toolkit.getDefaultToolkit().beep();
                int cnt = ((count++) % 2);
                switch (cnt) {
                    case 0:
                        l.setBackground(Color.white);
                        l.setForeground(Color.BLACK);
                        break;
                    case 1:
                        l.setBackground(Color.BLACK);
                        l.setForeground(Color.white);
                        break;
                }
            }
        });
    }
}
```