---
title: Java矩阵操作
date: 2021-03-18 13:15:39
tags: Java
---


# Java 矩阵转置


## 矩阵转置


### 函数返回一个二维数组，而不改变原有数组
```Java
public class Main {
    public static int[][] ReserveMat(int[][] matrix) {
        int m=matrix.length, n=matrix[0].length;
        int[][] newMatrix=new int[m][n];
        for (int i = 0; i < m ; i++) {
            for (int j = 0; j < n; j++) {
                newMatrix[i][j]=matrix[m-i-1][n-j-1];
            }
        }
        return newMatrix;
    }

    public static void print(int[][] m) {
        for (int i = 0; i < m.length; i++) {
            for (int j = 0; j < m[i].length; j++) {
                System.out.print(m[i][j] + "\t");
            }
            System.out.println("");
        }
    }

    public static void main(String[] args) {
        int[][] matrix = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
        System.out.println("原矩阵：");
        print(matrix);
        int[][] newMatrix= ReserveMat(matrix);
        System.out.println("转换后的矩阵：");
        print(newMatrix);
        System.out.println("原来的的矩阵：");
        print(matrix);
    }
}
```
<!-- more -->
## 矩阵相加

```Java
public class Main {
    public static int[][] PlusMat(int[][] matrix1, int[][] matrix2) {
        int m1 = matrix1.length, n1 = matrix1[0].length;
        int m2 = matrix2.length, n2 = matrix2[0].length;
        int[][] newMatrix = new int[m1][n1];
        if ((m1 != m2) || (n1 != n2)) {
            System.out.println("两矩阵行列不一致，无法相加");
            return null;
        } else {
            for (int i = 0; i < m1; i++) {
                for (int j = 0; j < n1; j++) {
                    newMatrix[i][j] = matrix1[i][j] + matrix2[i][j];
                }
            }
        }
        return newMatrix;
    }

    public static void print(int[][] m) {
        for (int i = 0; i < m.length; i++) {
            for (int j = 0; j < m[i].length; j++) {
                System.out.print(m[i][j] + "\t");
            }
            System.out.println("");
        }
    }

    public static void main(String[] args) {
        int[][] matrix1 = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
        int[][] matrix2 = {{2, 3, 4}, {5, 6, 7}, {8, 9, 10}};
        int[][] newMatrix;
        System.out.println("矩阵相加\nMatrix1:");
        print(matrix1);
        System.out.println("Matrix2");
        print(matrix2);
        System.out.println("相加后的结果");
        newMatrix = PlusMat(matrix1, matrix2);
        if (newMatrix != null) {
            print(newMatrix);
        }
    }
}
```