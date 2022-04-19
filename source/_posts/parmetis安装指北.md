---
title: parmetis安装指北
date: 2022-04-11 15:36:38
tags: 
- 实习日记
- 安装指北
- parmetis
---

# PARMETIS/parmetis

下载地址：[PARMETIS](http://glaros.dtc.umn.edu/gkhome/metis/parmetis/download)

需要准备的依赖：

- cmake

### 获取源码

```shell
wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/parmetis/parmetis-4.0.3.tar.gz
tar xzvf parmetis-4.0.3.tar.gz
cd parmetis-4.0.3
```

### 进行一些修改

修改`metis/include/metis.h`的第33行

```cpp
#define IDXTYPEWIDTH 64
```

### 进入metis目录，编译安装metis

```shell
cd metis
make config shared=1 prefix=/es01/yeesuan/yeesuan003/software/METIS
make install
```

### 进入上层目录，进行编译安装

```shell
cd ../
make config shared=1 prefix=/es01/yeesuan/yeesuan003/software/METIS
make install
```

