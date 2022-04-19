---
title: CaNS移植指南
date: 2022-04-19 14:46:14
tags:
- 实习日记
- CaNS
- 流体力学
- 工业仿真
---

# CaNS移植指南

[GitHub地址](https://github.com/p-costa/CaNS)

CaNS介绍：
>CaNS（Canonical Navier-Stokes）是用于流体流动的大规模并行数值模拟的代码。它旨在求解不可压缩牛顿流体的任何流体流动，该流体可以受益于基于 FFT 的求解器，用于 3D 笛卡尔网格中的二阶有限差分泊松方程。

## 获取源码

采用`v1.1.4`版本的代码

```shell
wget  https://github.com/p-costa/CaNS/archive/refs/tags/v1.1.4.tar.gz
tar xzvf v1.1.4.tar.gz
cd CaNS-1.1.4/
```

## 编译安装

进入`src/main`目录,修改`Makefile`中的`LIBS`

```Makefile
LIBS = -L/path/to/fftw/lib -lfftw3 -lfftw3_threads
```

然后执行`make`,在当前目录下生成`cans`，或者可以执行`make run`，将生成的cans、data、dns.ini移动到`../run`，

```shell
make
make run
cd ../run
mpirun -np 4 cans dns.in
```

## spack 安装脚本

{%codeblock package.py lang:py%}
# Copyright 2013-2021 Lawrence Livermore National Security, LLC and other
# Spack Project Developers. See the top-level COPYRIGHT file for details.
#
# SPDX-License-Identifier: (Apache-2.0 OR MIT)

from spack import *


class Cans(MakefilePackage):
    """FIXME: Put a proper description of your package here."""

    homepage = "https://github.com/p-costa/CaNS"
    url      = "https://github.com/p-costa/CaNS/archive/refs/tags/v1.1.4.tar.gz"

    maintainers = ['lhxone', 'github_user2']

    version('1.1.4', sha256='8334c67810472edc18d5403a0bcb27fd57a620722c1e8c317518db4506867b81')
    version('1.1.3', sha256='01fa42e51ddcf6161fb63a124a0f2218c67f85ff4cc5236b995a5650d85e7615')
    version('1.1.2', sha256='31c8d6c1f619fb60b7919922c7a3a64dd614a1a2f89f38560184f75ed0526171')
    version('1.1.0', sha256='e3fd84902e18715c6476fe780e2395ca04db9e6b0c830b55a7aa9204b1fd0886')

    # FIXME: Add dependencies if required.
    depends_on('openmpi', type = ('build', 'run'))
    depends_on('fftw', type = ('build', 'run'))

    def edit(self, spec, prefix):
        with working_dir('src'):
            makefile = FileFilter('Makefile')
            makefile.filter('LIBS =.*', 'LIBS = -L{} -lfftw3 -lfftw3_threads'.format(spec['fftw'].prefix.lib))

    def build(self, spec, prefix):
        with working_dir('src'):
            make()

    def install(self, spec, prefix):
        mkdir(prefix.bin)
        with working_dir('src'):
            install('cans', prefix.bin)

{%endcodeblock%}



