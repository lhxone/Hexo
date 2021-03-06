---
title: spack-Makefile使用手册
date: 2022-04-01 09:55:44
tags: 实习日记
---


原文档链接：[https://spack.readthedocs.io/en/latest/spack.build_systems.html#module-spack.build_systems.makefile](https://spack.readthedocs.io/en/latest/spack.build_systems.html#module-spack.build_systems.makefile)


# spack.build_systems.makefile 模块介绍

使用可编辑 `Makefile` 构建的包的专用类

这个类提供了三个可以被覆盖的方法：

1. `edit`
2. `build`
3. `install`


通常来说`edit`方法需要被覆盖，而`build`，`install`方法并不需要覆盖（bushi）

|方法|目的|
|---|---|
|`build_targets`       |指定`build`阶段的`make`目标|
|`install_targets`     |指定`install`阶段的`make`目标|
|`build_directory()`   |`Makefile`所在的位置（build阶段所在的目录）|


# 常用方法

### `edit(self, spec, prefix)`

主要作用为在执行`build`之前编辑`Makefile`，例如修改各类依赖库的位置

经常用到的操作：

需要引用源码中的某些文件，因此要获取安装过程中源码位置

```py
pwd = os.getcwd()
```

需要编辑`Makefile`的某一项，示例地址：[stream](https://github.com/spack/spack/blob/develop/var/spack/repos/builtin/packages/stream/package.py)

```py
# 使用FileFilter正则表达式替换
def edit(self, spec, prefix):
    makefile = FileFilter('Makefile')

    makefile.filter(r'^\s*CC\s*=.*',  'CC = '  + spack_cc)
    makefile.filter(r'^\s*CXX\s*=.*', 'CXX = ' + spack_cxx)
    makefile.filter(r'^\s*F77\s*=.*', 'F77 = ' + spack_f77)
    makefile.filter(r'^\s*FC\s*=.*',  'FC = '  + spack_fc)
```



或编辑类似`make.inc`的文件，示例地址：[elk](https://github.com/spack/spack/blob/develop/var/spack/repos/builtin/packages/elk/package.py)

```py
def edit(self, spec, prefix):
    config = {
        'CC': 'cc',
        'MAKE': 'make',
    }

    if '+blas' in spec:
        config['BLAS_LIBS'] = spec['blas'].libs.joined()

    with open('make.inc', 'w') as inc:
        for key in config:
            inc.write('{0} = {1}\n'.format(key, config[key]))
```

编辑环境变量，示例地址：[cebench](https://github.com/spack/spack/blob/develop/var/spack/repos/builtin/packages/cbench/package.py)，[esmf](https://github.com/spack/spack/blob/develop/var/spack/repos/builtin/packages/esmf/package.py)(较复杂)

```py
def edit(self, spec, prefix):
    env['PREFIX'] = prefix
    env['BLASLIB'] = spec['blas'].libs.ld_flags

# 或是
    def setup_build_environment(self, env):
        # The location of the PRESTO source tree
        env.set('PRESTO', self.stage.source_path)
```





### `build(self, spec,prefix)`

通过传递的`build_targets`参数，调用`make`


常用操作：

修改`build`目录(更喜欢用`with working_dir('src'):`)

```py
build_directory = 'src'
make('target')
```

### `setup_build_environment(self, env)`

设置`build`阶段的环境变量，示例如下：

```py
    def setup_build_environment(self, env):
        env.append_path('LD_LIBRARY_PATH', self.stage.source_path + '/lib')
        env.set('PRESTO', self.stage.source_path)
```


# 其余方法



build(spec, prefix)[source]
Calls make, passing build_targets as targets.

propertybuild_directory
Returns the directory containing the main Makefile

Returns
build directory

build_system_class= 'MakefilePackage'
This attribute is used in UI queries that need to know the build system base class

build_targets= []
Targets for make during the build() phase

build_time_test_callbacks= ['check']
Callback names for build-time test

check()[source]
Searches the Makefile for targets test and check and runs them if found.

edit(spec, prefix)[source]
Edits the Makefile before calling make. This phase cannot be defaulted.

install(spec, prefix)[source]
Calls make, passing install_targets as targets.

install_targets= ['install']
Targets for make during the install() phase

install_time_test_callbacks= ['installcheck']
Callback names for install-time test

installcheck()[source]
Searches the Makefile for an installcheck target and runs it if found.

phases= ['edit', 'build', 'install']
Phases of a package that is built with an hand-written Makefile
