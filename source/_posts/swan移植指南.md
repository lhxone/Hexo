---
title: swan移植指南
date: 2022-04-11 14:27:01
tags: 实习日记
---

# swan

原文地址[swan](https://support.huaweicloud.com/prtg-kunpenghpcs/kunpengswan_02_0010.html)

### 修改platform.pl

修改第406行

```perl
print OUTFILE "F90_MPI = mpifort\n";
```

修改第414行

```perl
print OUTFILE "NETCDFROOT =/path/to/NETCDF\n";
```

将第788行修改为`$compiler = "gfortran"`，或在环境变量中导入`FC=gfortran`

```perl
sub getcmpl {

   my $compiler = $ENV{'FC'};

   unless ( $compiler ) {
      foreach ('ifort','gfortran','f90','ifc','efc','pgf90','xlf90', 'lf95','g95') {
         $compiler = "gfortran";
         my $path  = `which $compiler`;
         last if $path;
      }
   }

   return $compiler;
}
```


### 编译安装

执行`make config`生成配置文件，然后执行`make mpi`编译程序

```shell
make config
make mpi
```

### spack安装代码

{% codeblock package.py lang:python %}
from spack import *
from os import *

class Swan(MakefilePackage):
    """FIXME: Put a proper description of your package here."""

    # FIXME: Add a proper url for your package's homepage here.
    homepage = "https://blog.lhxone.com"
    url      = "file:///es01/yeesuan/yeesuan003/source/swan4131.tar.gz"

    # FIXME: Add a list of GitHub accounts to

    version('4131', sha256='cd3ba1f0d79123f1b7d42a43169f07575b59b01e604c5e66fbc09769e227432e')

    # FIXME: Add dependencies if required.
    # depends_on('intel-parallel-studio', type = ('build', 'run'))
    depends_on('mpich', type = 'build')
    depends_on('netcdf-fortran', type = ('build', 'run'))
    depends_on('libfabric', type = ('run'))

    def edit(self, spec, prefix):
        env['FC'] = 'gfortran'
        makefile = FileFilter('platform.pl')
        makefile.filter('F90_MPI = .*', 'F90_MPI = mpifort\\n";')
        makefile.filter('NETCDFROOT =', 'NETCDFROOT = /es01/yeesuan/yeesuan003/NETCDF')

    def build(self, spec, prefix):
        make('config')
        make('mpi')


    def install(self, spec, prefix):
        mkdir(prefix.bin)
        install('*.exe', prefix.bin)
{% endcodeblock %}

