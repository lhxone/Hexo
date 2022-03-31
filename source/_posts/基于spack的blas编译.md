---
title: 基于spack的blas编译
date: 2022-03-07 10:27:51
tags: 实习日记
---

# 直接编译blas


首先从netlib官网下载源码,然后进入blas目录,修改make.inc文件最后一行,将其改为```BLASLIB      = libblas.a```

```shell
wget http://www.netlib.org/blas/blas-3.10.0.tgz
tar xzvf blas-3.10.0.tgz
cd BLAS-3.10.0
vim make.inc
```

修改完成后,执行```make all```即可在当前目录下编译生成```libblas.a```

```shell
make all
find libblas.a
# libblas.a
```

# 使用spack脚本编译blas

### 基本命令

- ```spack create [-n name] [-t TEMPLATE, --template TEMPLATE] [url]```
  
  创建一个以[template]为模版的包,并生成对应目录和package.py

- ```spack info [name]```

  显示一个包的信息

- ```spack edit [name]```
  
  快速编辑某个包目录下的package.py文件
  
- ```spack install [package_name] [-v]
  
  安装某个包,-v参数可以显示安装编译过程的详细日志
  

### 开始编译

首先创建一个包,并编辑package.py文件.值得注意的是,包名不可以为blas,这会与现有规则冲突(```$spack/etc/spack/defaults/packages.yaml```)

```
# 执行此命令会在创建完成后进入编辑package.py,加入--skip-editor以跳过
spack create netlib-blas
```

```python
# 在package.py中修改以下内容
from spack import *

# netlib-blas -> NetlibBlas
# 3dm -> _3dm
class NetlibBlas(MakefilePackage):
    """FIXME: Put a proper description of your package here."""
    homepage = "https://www.example.com"

    # 指定url、版本以及哈希值
    url      = "file:///es01/yeesuan/yeesuan003/mirror/blasblas-3.10.0.tgz"
    version('3.10.0', sha256='2e360d99c9bdc8407a61888c40aa853fb4219420ebb8264db486cb8860468ab3')

    def edit(self, spec, prefix):
        # 文件操作函数filter_file(regex, repl, *filenames, **kwargs)
        # 使用正则表达式修改编译生成的文件名称
        makefile = FileFilter('make.inc')
        makefile.filter(r'^\s*BLASLIB\s*=.*',  'BLASLIB      = libblas.a')  

    def install(self, spec, prefix):
        # 生成lib目录
        mkdir(prefix.lib)
        install("libblas.a",prefix.lib)
```

编辑完成后,执行```spack install netlib-blas```

```shell
[yeesuan003@spack ~]$ spack install netlib-blas
==> Installing netlib-blas-3.10.0-lvh47amv4vn23kti7cwdkbe4izugjsr4
==> No binary for netlib-blas-3.10.0-lvh47amv4vn23kti7cwdkbe4izugjsr4 found: installing from source
==> Using cached archive: /es01/yeesuan/yeesuan003/lhxone-test/spack/var/spack/cache/_source-cache/archive/2e/2e360d99c9bdc8407a61888c40aa853fb4219420ebb8264db486cb8860468ab3.tgz
==> No patches needed for netlib-blas
==> netlib-blas: Executing phase: 'edit'
==> netlib-blas: Executing phase: 'build'
==> netlib-blas: Executing phase: 'install'
==> netlib-blas: Successfully installed netlib-blas-3.10.0-lvh47amv4vn23kti7cwdkbe4izugjsr4
  Fetch: 0.00s.  Build: 1.08s.  Total: 1.09s.
[+] /es01/yeesuan/yeesuan003/lhxone-test/spack/software/linux-centos7-haswell/gcc-4.8.5/netlib-blas-3.10.0-lvh47amv4vn23kti7cwdkbe4izugjsr4
[yeesuan003@spack ~]$ tree /es01/yeesuan/yeesuan003/lhxone-test/spack/software/linux-centos7-haswell/gcc-4.8.5/netlib-blas-3.10.0-lvh47amv4vn23kti7cwdkbe4izugjsr4
/es01/yeesuan/yeesuan003/lhxone-test/spack/software/linux-centos7-haswell/gcc-4.8.5/netlib-blas-3.10.0-lvh47amv4vn23kti7cwdkbe4izugjsr4
└── lib
    └── libblas.a

1 directory, 1 file
```



### 备注

#### 依赖

```python
depends_on('gmake', type='build')
```

#### 环境变量

```python
def edit(self, spec, prefix):
    env['PREFIX'] = prefix
    env['BLASLIB'] = spec['blas'].libs.ld_flags
```

#### 编辑Makefile

```python
def edit(self, spec, prefix):
    makefile = FileFilter('Makefile')

    makefile.filter(r'^\s*CC\s*=.*',  'CC = '  + spack_cc)
    makefile.filter(r'^\s*CXX\s*=.*', 'CXX = ' + spack_cxx)
    makefile.filter(r'^\s*F77\s*=.*', 'F77 = ' + spack_f77)
    makefile.filter(r'^\s*FC\s*=.*',  'FC = '  + spack_fc)
```

#### 手动安装

```python
def install(self, spec, prefix):
    mkdir(prefix.bin)
    install('foo', prefix.bin)
    install_tree('lib', prefix.lib)
```
