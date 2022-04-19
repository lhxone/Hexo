---
title: suntans移植指南
date: 2022-04-19 14:29:43
tags:
- 实习日记
- suntans
- sunplot
- 海洋科学
---

# suntans移植指南

[suntans-stanford官网](https://web.stanford.edu/group/suntans/cgi-bin/documentation/user_guide/node1.html)

[suntans-GitHub官网](https://github.com/ofringer/suntans)


suntans介绍：
>斯坦福非结构化网格、非流体静力、平行沿海海洋模型。用于在河口和沿海以高分辨率模拟非静水流。需要网格生成器和 ParMETIS（如果并行运行）。

## 获取源码

使用GitHub上的源码包：

```shell
wget https://github.com/ofringer/suntans/archive/master.zip
unzip suntans-master.zip
```
## 编译安装

>如果已经安装`X11`则无需执行此步骤
>
>进入`main/`，并修改`Makefile`
>
>```Makefile
>XLDFLAGS= -lX11 -lm
>XINC=/path/to/X11/lib
>XLIBDIR = /path/to/X11/lib
>
>
>INCLUDES = $(PARMETISINCLUDE) $(TRIANGLEINCLUDE) $(NETCDFINCLUDE) $(XINC)
>```

执行`make`安装

```
cd suntans-master/examples/cylinder
make test
cd ../../main
make sunplot
./sunplot --datadir=../examples/cylinder/data
```

## spack安装脚本

{%codeblock package.py lang:python%}
# Copyright 2013-2021 Lawrence Livermore National Security, LLC and other
# Spack Project Developers. See the top-level COPYRIGHT file for details.
#
# SPDX-License-Identifier: (Apache-2.0 OR MIT)

from spack import *

class Suntans(MakefilePackage):
    """The Stanford unstructured-grid,
    nonhydrostatic, parallel coastal
    ocean model. """

    homepage = "https://github.com/ofringer/suntans"
    url      = "https://github.com/ofringer/suntans/archive/master.zip"

    maintainers = ['ofringer', 'zyaj', 'mrayson', 'jadelson', 'lhxone']

    version('0.0.1', '9b5d0dee5502a5650e37bf72f64b1124be86c084e7408aa517c214ddb25e36de')

    depends_on('libx11@1.6.3', type = ('build', 'run'))
    depends_on('openmpi', type = ('build'))

    def edit(self, spec, prefix):
        env['MPIHOME'] = spec['openmpi'].prefix
        with working_dir('main'):
            makefile = FileFilter('Makefile')
            makefile.filter('XINC=.*', 'XINC= -I{0}'.format(spec['libx11'].prefix.include))
            makefile.filter('XLIBDIR = .*', 'XLIBDIR = {0}'.format(spec['libx11'].prefix.lib))
            makefile.filter('INCLUDES = .*', 'INCLUDES = $(PARMETISINCLUDE) $(TRIANGLEINCLUDE) $(NETCDFINCLUDE) $(XINC)')

    def build(self, spec, prefix):
        build_targets = ['CC = mpicc']
        with working_dir('examples/cylinder'):
            make('test')
        with working_dir('main'):
            make('sunplot')

    def install(self, spec, prefix):
        mkdir(prefix.bin)
        mkdir(prefix.include)
        with working_dir('main'):
            install('sunplot', prefix.bin)
            install('*.h', prefix.include)
{%endcodeblock%}
