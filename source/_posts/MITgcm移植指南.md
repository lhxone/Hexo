---
title: MITgcm移植指南
date: 2022-04-19 14:08:09
tags:
- 实习日记
- 安装指北
- MITgcm
- 气候气象
- 海洋科学
---

# MITgcm移植指南

[MITgcm官网](http://mitgcm.org/)软件介绍：
>MITgcm 灵活的非流体静力学公式是为研究大气、海洋和气候而设计的数值模型，使其能够有效地模拟各种尺度的流体现象；它的伴随能力使其能够应用于敏感性问题以及参数和状态估计问题。通过采用流体方程同构，可以使用单个动力学核来模拟大气和海洋的流动。该模型的开发是为了在各种计算平台上高效执行。

[官方文档地址](https://mitgcm.readthedocs.io/en/latest/getting_started/getting_started.html)

## 获取源码

源代码在GiThub:[https://github.com/MITgcm/MITgcm/releases](https://github.com/MITgcm/MITgcm/releases)

我所使用的版本为2020mid

```shell
wget https://github.com/MITgcm/MITgcm/archive/refs/tags/checkpoint67s.tar.gz
mv checkpoint67s.tar.gz MITGCM-2020-mid.tar.gz
tar xzvf MITGCM-2020-mid.tar.gz
cd MITgcm-checkpoint67s
```

## 安装

需要先进入`build`目录，然后使用`genmake2`生成`Makefile`

```SHELL
cd verification/exp2/build
../../../tools/genmake2 -mods ../code -optfile «/PATH/TO/OPTFILE» # ../../../tools/build_options/linux_amd64_gfortran
```

然后执行make等命令

```shell
make depend
make
```

在`build`目录下生成`mitgcmuv`，完成

## mpi安装

[mpi安装的官方文档](https://mitgcm.readthedocs.io/en/latest/getting_started/getting_started.html#building-with-mpi)

与上面的类似，只需要将`genmake2`命令替换为如下即可

```shell
../../../tools/genmake2 -mods=../code -mpi -of=«/PATH/TO/OPTFILE» # ../../../tools/build_options/linux_amd64_gfortran
make depend
make
```
## openmp安装


[openmp安装的官方文档](https://mitgcm.readthedocs.io/en/latest/getting_started/getting_started.html#building-with-openmp)

与上面的类似，只需要将`genmake2`命令替换为如下即可


```shell
../../../tools/genmake2 -mods=../code -omp -of=«/PATH/TO/OPTFILE» # ../../../tools/build_options/linux_amd64_gfortran
make depend
make
```

## spack安装脚本

{%codeblock package.py lang:python%}
# Copyright 2013-2021 Lawrence Livermore National Security, LLC and other
# Spack Project Developers. See the top-level COPYRIGHT file for details.
#
# SPDX-License-Identifier: (Apache-2.0 OR MIT)

from spack import *
from os import system


class Mitgcm(MakefilePackage):
    """FIXME: Put a proper description of your package here."""

    # FIXME: Add a proper url for your package's homepage here.
    homepage = "https://github.com/MITgcm/MITgcm"
    url      = "https://github.com/MITgcm/MITgcm/archive/refs/tags/checkpoint67s.tar.gz"

    maintainers = ['lhxone', 'MITgcm']

    version('67z', sha256='917c23045dab1939a5f0879192b2347c38d178e58f1ff6b2d91bb67bc8cc7e31')
    version('67s', sha256='0ad3139c5835db20c744fd50b46d307593a5c8fc3d6b6dd5e01a0eb22438977b')
    version('67m', sha256='d176bb4b8a0e49ebe0936d753aff1c9e2bb8800de63cd5b4001700df1292b72e')
    version('67g', sha256='3d1947584588ea75566f24185830481681467ad70f3498a0b8600d51d04f1d7b')

    variant('mpi',
            default=False,
            description='Use the specified C++ standard when building.')
    variant('openmp',
            default=False,
            description='Use the specified C++ standard when building.')


    def build(self, spec, prefix):
        with working_dir('verification/exp2/build'):
            if '+mpi' in spec:
                system('cd {0}/verification/exp2/build && ../../../tools/genmake2 -mods=../code -mpi -of=../../../tools/build_options/linux_amd64_gfortran'.format(self.stage.source_path))
            elif '+omp' in spec:
                system('cd {0}/verification/exp2/build && ../../../tools/genmake2 -mods=../code -omp -of=../../../tools/build_options/linux_amd64_gfortran'.format(self.stage.source_path))
            else
                system('cd {0}/verification/exp2/build && ../../../tools/genmake2 -mods ../code -optfile ../../../tools/build_options/linux_amd64_gfortran'.format(self.stage.source_path))
            make('depend')
            make()

    def install(self, spec, prefix):
        mkdir(prefix.bin)
        with working_dir('verification/exp2/build'):
            install('mitgcmuv', prefix.bin)
{%endcodeblock%}