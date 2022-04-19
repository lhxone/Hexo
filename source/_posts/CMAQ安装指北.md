---
title: CMAQ安装指北
date: 2022-04-15 09:35:37
tags: 
- 实习日记
- 安装指北
- CMAQ
- ioapi
- 环境科学
---

# CMAQ移植安装

CMAQ介绍：
>美国环保署的一个活跃的开源开发项目，由一套用于进行空气质量模型模拟的程序组成。CMAQ 结合了当前在大气科学和空气质量建模、多处理器计算技术和开源框架方面的知识，以提供对臭氧、颗粒物、有毒物质和酸沉降的快速、技术上合理的估计。

## 前序准备

此软件需要用到的依赖如下


|软件名称|版本|下载地址|参考|
|---|---|---|---|
|CMAQ|5.3.1|https://codeload.github.com/USEPA/CMAQ/tar.gz/CMAQv5.3.1_19Dec2019|/|
|HDF5|1.10.1|https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.10/hdf5-1.10.1/src/|[HDF5安装手册](https://blog.lhxone.com/2022/04/11/HDF5%E5%AE%89%E8%A3%85%E6%8C%87%E5%8C%97/)|
|PnetCDF|1.9.0|https://parallel-netcdf.github.io/wiki/Download.html|/|
|NetCDF-C|4.7.0|https://github.com/Unidata/netcdf-c/releases/tag/v4.7.0|[netcdf-c安装手册](https://blog.lhxone.com/2022/04/11/netcdf-c-netcdf-fortran%E5%AE%89%E8%A3%85%E6%8C%87%E5%8C%97/#netcdf-c)|
|NetCDF-fortran|4.4.5|https://github.com/Unidata/netcdf-fortran/releases/tag/v4.4.5|[netcdf-fortran安装手册](https://blog.lhxone.com/2022/04/11/netcdf-c-netcdf-fortran%E5%AE%89%E8%A3%85%E6%8C%87%E5%8C%97/#netcdf-fortran)|
|OPTIMIZED-ROUTINES|V20.02|https://codeload.github.com/ARM-software/optimized-routines/tar.gz/v20.02|/|
|ioapi|3.2|https://codeload.github.com/cjcoats/ioapi-3.2/tar.gz/2020111|/|
|测试算例|2016_12SE1|https://dataverse.unc.edu/dataset.xhtml?persistentId=doi:10.15139/S3/IQVABD|/|


## ioapi安装

先来看一下目录结构

```
CMAQ/
├── CMAQ-CMAQv5.3.1_19Dec2019
│   ├── bldit_project.csh
│   ├── CCTM
│   ├── CMAQ_Project
│   ├── config_cmaq.csh
│   ├── DOCS
│   ├── POST
│   ├── PREP
│   ├── README.md
│   └── UTIL
├── CMAQ_Project
├── CMAQv5.3.1_19Dec2019.tar.gz
├── ioapi-3.2
│   ├── exclude
│   ├── index.html
│   ├── ioapi
│   ├── LICENSE
│   ├── Linux4_aarch64
│   ├── m3tools
│   ├── Makefile
│   ├── Makefile.template
│   ├── README
│   ├── README.md
│   ├── README.txt
│   ├── tests
│   ├── V32-CHANGES.txt
│   └── VERSION.txt
└── ioapi-3.2-2020111.tar.gz
```

先创建一个`CMAQ`工作目录，在此目录下解压CMAQ以及ioapi，并将解压好的ioapi重新命名为`ioapi-3.2`

```shell
tar -xvf ioapi-3.2-2020111.tar.gz
mv ioapi-3.2-2020111 ioapi-3.2
```

### ioapi配置

复制配置文件

```shell
cd ioapi-3.2
cp ioapi/Makeinclude.Linux2_ia64gfort ioapi/Makeinclude.Linux4_aarch64
```

修改`ioapi/Makeinclude.Linux4_aarch64`文件部分内容

{%codeblock Makeinclude.Linux4_aarch64 lang:Makefile%}
CC   = mpicc
CXX  = mpicxx
FC   = mpifort


#FSFLAGS = -save
{%endcodeblock%}

复制`ioapi`的`Makefile`文件

```shell
cp ioapi/Makefile.nocpl ioapi/Makefile
export HOME=/path/to/CMAQ #很重要，CMAQ需要靠他寻找ioapi位置
```

### m3tools配置

复制`m3tools`的`Makefile`文件

```shell
cp m3tools/Makefile.nocpl m3tools/Makefile
```

修改`m3tools/Makefile`内容

{%codeblock Makefile lang:Makefile%}
LIBS = -L${OBJDIR} -lioapi -L/path/to/NETCDF/lib -lnetcdff –lnetcdf -L/path/to /HDF5/lib -lhdf5_hl -lhdf5 -lz $(OMPLIBS) $(ARCHLIB) $(ARCHLIBS)
{%endcodeblock%}

### CMAQ配置

复制`CMAQ`的`Makefile`文件

```shell
cp Makefile.template Makefile
```

修改如下内容

{%codeblock lang:Makefile%}
BIN        = Linux4_aarch64
BASEDIR    = ${PWD}
INSTALL    = ${HOME}
LIBINST    = $(INSTALL)/$(BIN)
BININST    = $(INSTALL)/$(BIN)
CPLMODE    = nocpl
IOAPIDEFS  = "-DIOAPI_NCF4"


NCFLIBS    = -L/path/to/NETCDF/lib -lnetcdff -lnetcdf -L/path/to/HDF5/lib -lhdf5_hl -lhdf5 -lz

{%endcodeblock%}

### 编译ioapi

执行以下命令编译`ioapi`

```shell
make BIN=Linux4_aarch64
```

修改`STATE3.EXT`文件,将行尾`&`去掉

```

        !!  non-character portion of current I/O API state
        !!  Note that DOUBLE components are listed first, in order
        !!  to ensure appropriate (64-bit, usually) alignment.

        COMMON  / BSTATE3 /
     &          P_ALP3, P_BET3, P_GAM3,
     &          XCENT3, YCENT3, XORIG3, YORIG3, XCELL3, YCELL3,
     &          VGTYP3, VGTOP3, VGLVS3,
     &          FINIT3, COUNT3, CURDATE, CURTIME, LOGDEV,
     &          CDFID3, FTYPE3, SDATE3, STIME3, TSTEP3, MXREC3,
     &          NVARS3, NLAYS3, NROWS3, NCOLS3, NTHIK3,
     &          TINDX3, NINDX3, SINDX3, LINDX3, WCNDX3, WRNDX3,
     &          XINDX3, YINDX3, ZINDX3, DXNDX3, DYNDX3, VINDX3,
     &          GDTYP3, VOLAT3, RONLY3,
     &          BSIZE3, LDATE3, LTIME3, NDATE3, NTIME3, ILAST3,
     &          VTYPE3,
     &          ILCNT3, NLIST3, IFRST3, ILIST3, BEGRC3, ENDRC3,
     &          STDOUT, PN_MODE

        !!  character portion of current I/O API state

        COMMON  / CSTATE3 /
     &          EXECN3, SCNDSC, FLIST3, GDNAM3, VLIST3, UNITS3, VERSN3

        SAVE / BSTATE3 / , / CSTATE3 /

!................   end   STATE3.EXT   ....................................
```


## 编译CMAQ

修改`bldit_project.csh`

```shell
cd CMAQ-CMAQv5.3.1_19Dec2019
vim bldit_project.csh

# 修改为对应的目录
set CMAQ_HOME = /path/to/CMAQ/CMAQ_Project
```

运行以下命令来初始化所需文件

```shell
./bldit_project.csh
```

进入工作目录并在`case gcc`处改配置文件

```shell
cd ../CMAQ_Project/
vim config_cmaq.csh
```

{%codeblock config_cmaq.csh lang:shell%}
setenv IOAPI_MOD_DIR   /path/to/CMAQ/ioapi-3.2/Linux4_aarch64/
setenv IOAPI_INCL_DIR  /path/to/CMAQ/ioapi-3.2/ioapi/
setenv IOAPI_LIB_DIR   /path/to/CMAQ/ioapi-3.2/Linux4_aarch64/
setenv NETCDF_LIB_DIR  /path/to/NETCDF/lib/
setenv NETCDF_INCL_DIR /path/to/NETCDF/include/
setenv MPI_LIB_DIR     /path/to/OPENMPI/


setenv myCC mpicc
setenv myLINK_FLAG  "-fopenmp"
setenv mpi_lib "-lmpi"


setenv netcdf_lib "-lnetcdf -lnetcdff -lgomp"  #> -lnetcdff -lnetcdf for netCDF v4.2.0 and later

{%endcodeblock%}

执行以下命令链接各种依赖库

```shell
./config_cmaq.csh gcc 9.3.0
```

依次执行以下命令，进入编译目录，并编译主程序

```shell
cd CCTM/scripts/
./bldit_cctm.csh gcc 9.3.0
```

编译完成后，会在“BLD_CCTM_v531_gcc9.3.0”目录下生成CMAQ的可执行程序`CCTM_v531.exe`

## spack安装代码

{%codeblock package.py lang:python%}
from spack import *
from os import *


class Cmaq(MakefilePackage):
    """Code base for the U.S. EPA's Community Multiscale Air Quality Model
    (CMAQ)."""
    homepage = "https://www.epa.gov/CMAQ"
    url      = "https://github.com/USEPA/CMAQ/archive/CMAQv5.3.1_19Dec2019.tar.gz"

    version('5.3.1', sha256='659156bba27f33010e0fdc157a8d33f3b5b779b95511e2ade870284b6bcb4bc8',
            url='https://github.com/USEPA/CMAQ/archive/CMAQv5.3.1_19Dec2019.tar.gz')
    version('5.3', sha256='e245c291c7e88d481b13f577d1af9aeb5aef4de8c59f7fa06fa41d19bb2ed18c',
            url='https://github.com/USEPA/CMAQ/archive/CMAQv5.3_27Aug2019.tar.gz')

    depends_on('openmpi', type = ('build', 'run'))
    # depends_on('mpich', type = ('build'))
    # depends_on('netcdf-c', type = ('build', 'run'))
    # depends_on('netcdf-fortran', type = ('build', 'run'))
    depends_on('ioapi', type = ('build'))

    def edit(self, spec, prefix):
        csh = which('csh')

        bldit_project = FileFilter('bldit_project.csh')
        mkdirp('CMAQ_Project')
        bldit_project.filter('set CMAQ_HOME .*', 'set CMAQ_HOME = ' + self.stage.source_path + '/CMAQ_Project')
        csh('./bldit_project.csh')

        config_cmaq = FileFilter('config_cmaq.csh')
        config_cmaq.filter('setenv IOAPI_INCL_DIR   .*', 'setenv IOAPI_INCL_DIR   {0}'.format(spec['ioapi'].prefix.include))
        config_cmaq.filter('setenv IOAPI_LIB_DIR    .*', 'setenv IOAPI_LIB_DIR    {0}'.format(spec['ioapi'].prefix.Linux4_aarch64))

        # config_cmaq.filter('setenv NETCDF_LIB_DIR   .*', 'setenv NETCDF_LIB_DIR   {0}'.format(spec['netcdf-c'].prefix.lib))
        # config_cmaq.filter('setenv NETCDF_INCL_DIR  .*', 'setenv NETCDF_INCL_DIR  {0}'.format(spec['netcdf-c'].prefix.include))
        # config_cmaq.filter('setenv NETCDFF_LIB_DIR  .*', 'setenv NETCDFF_LIB_DIR  {0}'.format(spec['netcdf-fortran'].prefix.lib))
        # config_cmaq.filter('setenv NETCDFF_INCL_DIR .*', 'setenv NETCDFF_INCL_DIR {0}'.format(spec['netcdf-fortran'].prefix.include))

        config_cmaq.filter('setenv NETCDF_LIB_DIR   .*', 'setenv NETCDF_LIB_DIR   {0}'.format('/es01/yeesuan/yeesuan003/cmaq/NETCDF/lib'))
        config_cmaq.filter('setenv NETCDF_INCL_DIR  .*', 'setenv NETCDF_INCL_DIR  {0}'.format('/es01/yeesuan/yeesuan003/cmaq/NETCDF/include'))
        config_cmaq.filter('setenv NETCDFF_LIB_DIR  .*', 'setenv NETCDFF_LIB_DIR  {0}'.format('/es01/yeesuan/yeesuan003/cmaq/NETCDF/lib'))
        config_cmaq.filter('setenv NETCDFF_INCL_DIR .*', 'setenv NETCDFF_INCL_DIR {0}'.format('/es01/yeesuan/yeesuan003/cmaq/NETCDF/include'))


        config_cmaq.filter('setenv MPI_LIB_DIR      .*', 'setenv MPI_LIB_DIR      {0}'.format(spec['openmpi'].prefix.lib))
        config_cmaq.filter('setenv myCC .*', 'setenv myCC mpicc')
        config_cmaq.filter('setenv myLINK_FLAG.*', "setenv myLINK_FLAG  -fopenmp")
        config_cmaq.filter('setenv mpi_lib .*', 'setenv mpi_lib "-lmpi"')
        config_cmaq.filter('setenv netcdff_lib .*', 'setenv netcdf_lib "-lnetcdf -lnetcdff -lgomp"  #> -lnetcdff -lnetcdf for netCDF v4.2.0 and later')

        

    def build(self, spec, prefix):
        csh = which('csh')
        csh('./config_cmaq.csh', 'gcc', '9.3.0')
        with working_dir('CMAQ_Project/CCTM/scripts'):
            csh('./bldit_cctm.csh', 'gcc', '9.3.0')
            with working_dir('BLD_CCTM_v531_gcc9.3.0'):
                makefile = FileFilter('Makefile')
                makefile.filter(' include_path = -I .*', ' include_path = -I{0} \\'.format(spec['ioapi'].prefix.lib))
                makefile.filter('                -I \$\(LIB\)/ioapi/include_files.*', '                -I{0} \\'.format(spec['ioapi'].prefix.include))
                makefile.filter('                -I \$\(LIB\)/mpi .*', '                -I{0} -I.'.format(spec['openmpi'].prefix.lib))
                makefile.filter('IOAPI  =.*', 'IOAPI  = -L{0} -lioapi'.format(spec['ioapi'].prefix.lib))
                makefile.filter('NETCDF =.*', 'NETCDF = -L{0} -lnetcdff -lnetcdf'.format("/es01/yeesuan/yeesuan003/NETCDF/lib"))
                makefile.filter('C_FLAGS   = -O2 -DFLDMN .*', ' C_FLAGS   = -O2 -DFLDMN -I{0} -I.'.format("/es01/yeesuan/yeesuan003/lhxone-test/spack/software/linux-centos7-skylake_avx512/gcc-8.5.0/intel-parallel-studio-cluster.2020.4-dsfubhta2i6ox2a666fhw2fvj6p2dupj/compilers_and_libraries_2020.4.304/linux/mpi/intel64/include"))
                makefile.filter('LINK_FLAGS =.*', 'LINK_FLAGS =  -fopenmp')
                makefile.filter('MPI_INC  =.*', 'MPI_INC  = /es01/yeesuan/yeesuan003/lhxone-test/spack/software/linux-centos7-skylake_avx512/gcc-8.5.0/mpich-3.4.2-433hbugctpbwp5trpk55d533gealrgvp/include')
                make()

    def install(self, spec, prefix):
        mkdir(prefix.bin)
        with working_dir('CMAQ_Project/CCTM/scripts/BLD_CCTM_v531_gcc9.3.0'):
            install('CCTM_v531.exe', prefix.bin)
{%endcodeblock%}
