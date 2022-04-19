---
title: FVCOM编译&测试
date: 2022-03-08 14:24:38
tags: 
- 实习日记
- 安装指北
- FVCOM
---

# 编译前准备

## 安装HDF5

源码地址为```https://s3.amazonaws.com/hdf-wordpress-1/wp-content/uploads/manual/HDF5/HDF5_1_10_5/source/hdf5-1.10.5.tar.gz```,使用wget下载并解压源码

```shell
wget https://s3.amazonaws.com/hdf-wordpress-1/wp-content/uploads/manual/HDF5/HDF5_1_10_5/source/hdf5-1.10.5.tar.gz
tar -xvf hdf5-1.10.5.tar.gz
cd hdf5-1.10.5
```

执行以下命令进行配置、编译及安装

```shell
./configure --prefix=/path/to/install/HDF5 --enable-fortran --enable-parallel CC=mpicc FC=mpif90 CXX=mpicxx
make -j
make install
```

## 安装NetCDF-C

在安装NetCDF-C时,使用4.7.0版本的安装包报错,在GitHub上查到[issue](https://github.com/Unidata/netcdf-c/issues/1390),更换最新版本解决

```log
checking whether byte range support is enabled... no
configure: error: curl required for byte range support. Install curl or build without --enable-byterange.
```

源码地址为```https://github.com/Unidata/netcdf-c/archive/refs/tags/v4.8.1.tar.gz```,使用wget下载并解压源码

```shell
wget https://github.com/Unidata/netcdf-c/archive/refs/tags/v4.8.1.tar.gz
tar -zxvf netcdf-c-4.8.1.tar.gz
cd netcdf-c-4.8.1
```

执行以下命令进行配置、编译及安装

```shell
./configure --prefix=/path/to/NETCDF LDFLAGS="-L$HDF5/lib" CPPFLAGS="-I$HDF5/include" CC=mpicc --disable-dap
make -j
make install
```

编辑环境变量

```shell
vim ~/.bashrc
export PATH="/path/to/NETCDF/bin:$PATH"
export LD_LIBRARY_PATH="/path/to/NETCDF/lib:$LD_LIBRARY_PATH "
export NETCDF="/path/to/NETCDF"
```


## 安装NetCDF-Fortran

源码地址为```https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v4.5.4.tar.gz```,使用wget下载并解压

```shell
wget https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v4.5.4.tar.gz
tar -xzvf netcdf-fortran-4.5.4.tar.gz
cd netcdf-fortran-4.5.4.tar.gz
```

执行以下命令进行配置

```shell
./configure --prefix=/path/to/NETCDF CPPFLAGS="-I$HDF5/include -I$NETCDF/include" LDFLAGS="-L$HDF5/lib -L$NETCDF/lib" CC=mpicc FC=mpif90 F77=mpif90
make -j
make install
```


# 编译FVCOM主程序

## 获取安装包

源码地址为```http://fvcom.smast.umassd.edu/releases/fvcom-4.1.tar.gz```,将其下载到本地并解压

```shell
wget http://fvcom.smast.umassd.edu/releases/fvcom-4.1.tar.gz
mv fvcom-4.1.tar.gz /path/to/FVCOM
tar -xzvf fvcom-4.1.tar.gz
cd FVCOM4.1
```

## 配置

若要使用```Estuary```算例,则使用```Examples/Estuary/make.inc_example```;若要使用```Inlet```算例,则应使用```Examples/Inlets/make.inc_example```,在确认样例之后,将所选的模版文件复制到```FVCOM_source```之下.

```shell
# 复制模版文件(以Estuary为例)
cp Examples/Estuary/make.inc_example FVCOM_source/make.inc

# 创建软连接
ln -sf FVCOM_source/make.inc ./
```

编辑```make.inc```

```makefile
############
# make.inc #
############

######################################################################################
# TOPDIR = /path/to/FVCOM/FVCOM4.1/FVCOM_source
 51            TOPDIR        = /es01/yeesuan/yeesuan003/lhxone-test/software/FVCOM/FVCOM4.1/FVCOM_source

######################################################################################

 79              LIBDIR       =  -L$(INSTALLDIR)/lib -L../METIS_source/metis -L./libs/julian
 80              INCDIR       =  -I$(INSTALLDIR)/include -I../METIS_source/metis -I./libs/julia

######################################################################################

 97               IOLIBS = -L/es01/yeesuan/yeesuan003/lhxone-test/software/NETCDF/lib -L/es01/yeesuan/yeesuan003/lhxone-test/software/HDF5/lib  -lnetcdff -lnetcdf -lhdf5_hl -lhdf5 -lz -lm

######################################################################################

532 #--------------------------------------------------------------------------
533 #  Intel/MPI Compiler Definitions (SMAST)
534 #--------------------------------------------------------------------------
535          CPP      = /usr/bin/cpp
536          COMPILER = -DIFORT
537          CC       = mpiicc
538          CXX      = mpiicpc
539          CFLAGS   = -O3
540          FC       = mpiifort
```

## 编译Metis库

执行以下命令编译metis库

```shell
cd /path/to//FVCOM/FVCOM4.1/METIS_source
tar -zxvf metis.tgz
cd metis
mkdir -p /path/to/FVCOM/FVCOM4.1/FVCOM_source/libs/install/lib
mkdir -p /path/to/FVCOM/FVCOM4.1/FVCOM_source/libs/install/include
mkdir -p /path/to/FVCOM/FVCOM4.1/FVCOM_source/libs/install/bin
make -j
make install
```

## 编译julian库

执行以下命令编译julian库

```shell
cd /path/to/FVCOM4.1/FVCOM_source/libs
tar -zxvf julian.tgz
cd julian
make -j
make install
```

## 安装主程序

执行以下命令以编译安装FVCOM主程序

```shell
cd  /path/to/FVCOM/FVCOM4.1/FVCOM_source
vim swmod2.F
# 修改104行内容
104 !!!!$OMP&              KM_WAM, KM01, SIGM_10, SIGM01)
:wq!
vim mod_newinp.F
```

在```mod_newinp.F```中修改如下内容

```fortran
! 第一个"contains"语句添加如下内容。
! line_50
contains
  Character(Len=256) Function N_Fmt(c, n)
      Character(Len=*), Intent(IN) :: c
      Integer, Intent(IN) :: n
      integer :: i, j
      character(len=16) :: cn
      i = index(c, '<')
      j = index(c, '>')
      write (cn, '(g0)') n
      N_Fmt = c(:i - 1)//Trim(adjustL(cn))//c(j + 1:)
  End Function N_Fmt

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

! line_352
write(*,N_Fmt('(A20,<size>F10.4)',SIZE))trim(argname)//': ',fval(1:SIZE)

! line_421
write(*,N_Fmt('(A20,<size>I10)',SIZE))trim(argname)//': ',ival(1:SIZE)

! line_494
write(*,N_Fmt('(A20,<size>L10)',SIZE))trim(argname)//': ',cval(1:SIZE)

! line_567
write(*,N_Fmt('(A20,<size>A10)',SIZE))trim(argname)//': ',sval(1:SIZE)
```

执行以下命令以修改“==”为“.eqv.”,修改“/=”为“.neqv”

```shell
sed -i 's/\/=\.TRUE\./\.neqv\.\.TRUE\./g' mod_scal.F
sed -i 's/==\.TRUE/\.eqv\.\.TRUE/g' internal_step.F
sed -i 's/==\.FALSE\./\.eqv\.\.FALSE\./g' adv_t.F
sed -i 's/==\.FALSE\./\.eqv\.\.FALSE\./g' adv_s.F
```

修改完成后执行```make```命令,即可在当前目录下看到fvcom

```shell
make
find fvcom
fvcom
```


# 测试运算

## 复制算例到测试目录


```shell
mkdir -p /path/to/FVCOM/test
cd /path/to/FVCOM/test
cp cp -r /path/to/FVCOM/FVCOM4.1/Examples/Estuary/ ./
cd Estuary/run
```

## 运行算例

```shell
# 在当前目录下生成tst_0001.nc结果文件
mpirun -n 24  --bind-to core /path/to/fvcom  --casename=tst
```
