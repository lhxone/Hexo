---
title: 二叉树(Binary Tree)
date: 2020-10-23 11:20:10
tags: 
- 数据结构
- C++
---

### 10.23 二叉树BinaryTree

#### 上机任务


<center><img src="https://s3.ax1x.com/2021/03/13/6wNztK.md.png" width="50%"></center>

```cpp
#include <iostream>
#include <queue>

using namespace std;

typedef struct node{
    char data;
    node *parent,*left,*right;
}Treenode,*BiTree;

typedef struct BinaryTree{
    Treenode *root;
    int deepth;
    int num;
};

void CreatTree(BiTree &t){
    char c;
    cin>>c;
    if (c == '*'){
        t = NULL;
    } else{
        t = new (node);
        t->data = c;
        CreatTree(t->left);
        CreatTree(t->right);
    }
    return;
}


void DFS(node* node)
{
    if (node==NULL){
        return;;
    }
    cout<<node->data<<"->";
    if (node->left!=NULL)
        DFS(node->left);
    if (node->right!=NULL)
        DFS(node->right);
}

void prettyPrintTree(node* node, string prefix = "", bool isLeft = true) {
    if (node == NULL) {
        cout << "Empty tree";
        return;
    }
    if(node->right) {
        prettyPrintTree(node->right, prefix + (isLeft ? "│   " : "    "), false);
    }
    cout << prefix + (isLeft ? "└── " : "┌── ") + node->data + "\n";
    if (node->left) {
        prettyPrintTree(node->left, prefix + (isLeft ? "    " : "│   "), true);
    }
}

void Mirror(Treenode *node){
    if (node == NULL){
        return;
    }
    swap(node->left,node->right);
    Mirror(node->left);
    Mirror(node->right);
}

int TreeDepth(Treenode *node){
    if(node==NULL)
        return 0;
    int left=TreeDepth(node->left)+1;
    int right=TreeDepth(node->right)+1;
    return left>right?left:right;
}

int leaf(Treenode *node,int &sum){
    if (node==NULL){
        return 0;
    }
    if (node->left == NULL&&node->right == NULL){
        sum++;
    }
    if (node->left!=NULL)
        leaf(node->left,sum);
    if (node->right!=NULL)
        leaf(node->right,sum);
    return sum;
}

int main() {
    int sum=0;      //叶子数量
    BinaryTree t;
    CreatTree(t.root);
    DFS(t.root);
    cout<<endl;
    prettyPrintTree(t.root);
    leaf(t.root,sum);
    cout<<"叶子个数为："<<sum<<endl;
    cout<<"深度为："<<TreeDepth(t.root)<<endl;
    Mirror(t.root);
    DFS(t.root);
    cout<<endl;
    prettyPrintTree(t.root);
    return 0;
}
//ABD*F***CE***
```
<!-- more -->
| 完成的✅        | 没完成的❌        |
| -------------- | ---------------- |
| 建立BinaryTree | 中序遍历         |
| 前序遍历       | 后序遍历         |
| 前序输出       | （逆）波兰表达式 |
| 镜像反转       |                  |

<img src="https://s3.ax1x.com/2021/03/13/6wUBu9.md.png">

*****