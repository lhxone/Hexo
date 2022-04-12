---
title: HDF5安装指北
date: 2022-04-11 15:37:02
tags:
- 实习日记
- 安装指北
---


# HDF5安装

## 获取源码

源码地址为```https://s3.amazonaws.com/hdf-wordpress-1/wp-content/uploads/manual/HDF5/HDF5_1_10_5/source/hdf5-1.10.5.tar.gz```,使用wget下载并解压源码

```shell
wget https://s3.amazonaws.com/hdf-wordpress-1/wp-content/uploads/manual/HDF5/HDF5_1_10_5/source/hdf5-1.10.5.tar.gz
tar -xvf hdf5-1.10.5.tar.gz
cd hdf5-1.10.5
```
### 配置安装

执行以下命令进行配置、编译及安装

```shell
./configure --prefix=/path/to/install/HDF5 --enable-fortran --enable-parallel CC=mpicc FC=mpif90 CXX=mpicxx
make -j
make install
```
