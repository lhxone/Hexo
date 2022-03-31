---
title: spack配置与使用
date: 2022-02-21 16:42:47
tags: 实习日记
---


# 安装spack

```shell
git clone -c feature.manyFiles=true https://github.com/spack/spack.git
cd spack/bin
./spack install zlib
```

# 常用命令

常用命令如下：

|命令|功能|例子|
|---|---|---|
|spack list|列出可用spack安装的全部软件||
|spack info|查询可安装软件的基本信息|spack info vasp|
|spack find|查询已安装的软件|spack find gcc|
|spack load|加载已安装软件的环境变量|spack load cmake|
|spack unload|卸载加载的软件的环境变量|spack unload cmake|
|spack install|安装软件|spack install gromacs|


### 加载spack包管理器环境变量

```shell
source spack/share/spack/setup-env.sh
```

### 将编译器加入spack编译器配置

执行```spack compiler find```可以将当前环境变量的编译器加载到spack编译器配置中,默认的配置文件在 ```$HOME/.spack/linux/compilers.yaml```

```shell
spack find gcc
spack load gcc@8.5.0
spack compiler find
```

### 纳入已安装的软件到spack

执行```spack exteranl find```可以将当前环境变量中包含bin目录的软件纳入spack管理，默认的配 置文件在```$HOME/.spack/packages.yaml```

### 添加软件源码镜像仓库

执行```spack mirror add <name> <path>```,例如添加软件源码镜像仓库```spack mirror add local /es01/jnist/mirror```,我们默认添加了这个镜像仓库

### 其他基础配置

通过```spack config get config > config.yaml```生成一份spack基础配置到```$HOME/.spack```下


# 命令详解

### 查询可安装软件

执行```spack list```可以查询可由spack安装的全部软件.有的时候,所需的软件的名字可能在spack软件列表不同,比如数学库blas、lapack、scalapack的实现会有好多种,下面我们展示下所有lapack的实现, 只需要在```list```后加```-d```参数

```shell
spack list -d lapack
```

### 查询可安装软件的基本信息

执行```spack info <package>```可以查询可安装软件的基本信息.例如,查询```vasp```的基本信息:```spack info vasp```这样会打印出：

```shell
spack info vasp
MakefilePackage:   vasp

Description:
     The Vienna Ab initio Simulation Package (VASP) is a computer program
    for atomic scale materials modelling, e.g. electronic structure
    calculations and quantum-mechanical molecular dynamics, from first
    principles.

Homepage: https://vasp.at

Externally Detectable: 
    False

Tags: 
    None

Preferred version:  
    6.1.1        file:///es01/yeesuan/yeesuan003/vasp.6.1.1.tgz

Safe versions:  
    6.1.1        file:///es01/yeesuan/yeesuan003/vasp.6.1.1.tgz
    5.4.4.pl2    file:///es01/yeesuan/yeesuan003/vasp.5.4.4.pl2.tgz
    5.4.4        file:///es01/yeesuan/yeesuan003/vasp.5.4.4.tgz

Deprecated versions:  
    None

Variants:
    Name [Default]     When    Allowed values    Description
    ===============    ====    ==============    =============================================================

    cuda [off]         --      on, off           Enables running on Nvidia GPUs
    scalapack [off]    --      on, off           Enables build with SCALAPACK
    vaspsol [off]      --      on, off           Enable VASPsol implicit solvation model
                                                 https://github.com/henniggroup/VASPsol

Installation Phases:
    edit    build    install

Build Dependencies:
    blas  cuda  fftw  lapack  mpi  netlib-scalapack  qd  rsync

Link Dependencies:
    blas  cuda  fftw  lapack  mpi  netlib-scalapack  qd

Run Dependencies:
    mpi

Virtual Packages: 
    None
```

### 查询已安装软件

执行```spack find```可以查询使用spack安装好的全部软件

在```find```后加入```-d```(取依赖的英语单词dependencies的首字母)参数可以查看软件使用的依赖,例 如查看```vasp6.1.0```使用的依赖

```shell
[yeesuan003@spack ~]$ spack find -d vasp@6.1.0
==> 1 installed package
-- linux-centos7-cascadelake / intel@19.0.5.281 -----------------
vasp@6.1.0
    fftw@3.3.10
        intel-mpi@2019.5.281
    intel-mkl@2019.5.281
    netlib-scalapack@2.1.0
```

# 环境变量

### 加载软件的环境变量

```shell
spack load <package>
```

有时候我们需要加载多个软件的环境变量，我们可以使用```spack load <package1> <package2> ...```例如加载```intel2019```和```gcc6.5.0```

```shell
spack load intel-parallel-studio@cluster.2019.5 gcc@6.5.0
```

### 使用hash值加载软件的环境变量

有时候我们想加载某个软件，但它存在多个同名的软件时直接加载会报错,可以使用```spack load /<hash>```

```shell
spack load gcc
==> Error: gcc matches multiple packages.
  Matching packages:
    io5n6ud gcc@6.5.0%gcc@4.8.5 arch=linux-centos7-haswell
    x4guukb gcc@7.5.0%gcc@4.8.5 arch=linux-centos7-haswell
    k63wbs4 gcc@8.5.0%gcc@4.8.5 arch=linux-centos7-haswell
    6icy7ve gcc@10.2.0%gcc@4.8.5 arch=linux-centos7-haswell
  Use a more specific spec.
spack load /k63wbs4
```

### 查看加载的环境变量

```shell
spack load --sh <package>
```

### 获取软件安装位置和可执行文件名字

```shell
spack location -i <package>
```

当然也可以使用hash值获取,

```shell
spack location -i /<hash>
```

# 安装软件

```shell
spack install <package>
```
通常,我们需要指定编译软件所需要的编译器,使用```spack compilers```可以查看spack可以使用的编译器











