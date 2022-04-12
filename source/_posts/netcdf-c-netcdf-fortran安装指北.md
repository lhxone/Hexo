---
title: netcdf-c/netcdf-fortran安装指北
date: 2022-04-11 15:37:27
tags: 
- 实习日记
- 安装指北
---

以下安装需要依赖HDF5以及mpi环境：[HDF5安装方法](HDF5安装指北.md)

# netcdf-c


## 版本问题

在安装NetCDF-C时,使用4.7.0版本的安装包报错,在GitHub上查到[issue](https://github.com/Unidata/netcdf-c/issues/1390),更换最新版本解决

```log
checking whether byte range support is enabled... no
configure: error: curl required for byte range support. Install curl or build without --enable-byterange.
```

## 获取源码

源码地址为```https://github.com/Unidata/netcdf-c/archive/refs/tags/v4.8.1.tar.gz```,使用wget下载并解压源码

```shell
wget https://github.com/Unidata/netcdf-c/archive/refs/tags/v4.8.1.tar.gz
tar -zxvf netcdf-c-4.8.1.tar.gz
cd netcdf-c-4.8.1
```

## 配置安装

执行以下命令进行配置、编译及安装

```shell
./configure --prefix=/path/to/NETCDF LDFLAGS="-L$HDF5/lib" CPPFLAGS="-I$HDF5/include" CC=mpicc --disable-dap
make -j
make install
```

## 编辑环境变量

```shell
vim ~/.bashrc
export PATH="/path/to/NETCDF/bin:$PATH"
export LD_LIBRARY_PATH="/path/to/NETCDF/lib:$LD_LIBRARY_PATH "
export NETCDF="/path/to/NETCDF"
```



# netcdf-fortran

##  获取源码

源码地址为```https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v4.5.4.tar.gz```,使用wget下载并解压

```shell
wget https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v4.5.4.tar.gz
tar -xzvf netcdf-fortran-4.5.4.tar.gz
cd netcdf-fortran-4.5.4.tar.gz
```

## 配置安装

执行以下命令进行配置

```shell
./configure --prefix=/path/to/NETCDF CPPFLAGS="-I$HDF5/include -I$NETCDF/include" LDFLAGS="-L$HDF5/lib -L$NETCDF/lib" CC=mpicc FC=mpif90 F77=mpif90
make -j
make install
```

