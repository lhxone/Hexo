---
title: presto系列软件的安装与使用
date: 2022-03-31 11:23:10
tags: 实习日记
---


`presto系列软件的安装与使用`



##  fftw


安装

```sh
# float
./configure --enable-float --prefix=/es01/yeesuan/yeesuan003/software/fftw CC=gcc F77=gfortran --enable-shared --enable-static --enable-sse --enable-sse2 --enable-avx --enable-avx2 --enable-fma --enable-mpi --enable-threads --enable-openmp


make -j && make install

make clean

# double
./configure --prefix=/es01/yeesuan/yeesuan003/software/fftw3d CC=gcc F77=gfortran --enable-shared --enable-static --enable-sse2 --enable-avx --enable-avx2 --enable-fma --enable-mpi --enable-threads --enable-openmp

```

环境变量配置

```sh
export PATH=/es01/yeesuan/yeesuan003/software/fftw/bin${PATH:+:${PATH}}
export PATH=/es01/yeesuan/yeesuan003/software/fftw3d/bin${PATH:+:${PATH}}

export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/fftw/lib
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/fftw3d/lib

export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/fftw/include
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/fftw3d/include
```

测试fftw

```cpp
#include<stdio.h>
#include<stdlib.h>
#include<fftw3.h>

#define N 5

int main(){
    fftw_complex *in,*out;
    fftw_plan p;
    in = (fftw_complex*)fftw_malloc(sizeof (fftw_complex) * N);
    out= (fftw_complex*)fftw_malloc(sizeof (fftw_complex) * N);
    p= fftw_plan_dft_1d(N, in, out, FFTW_FORWARD, FFTW_ESTIMATE);
    int n;
    for (n= 0; n < N; n++)
    {
        in[n][0]=1;
        in[n][1]= 2;
    }
    fftw_execute(p);
    fftw_destroy_plan(p) ;
    for (n = 0; n < N ;n++)
    {
        printf("%3.2lf + %3.2lfi\t", out[n][0],out[n][1]);
    }
    printf("\n");
    fftw_free(in); fftw_free(out);
    return 0;
}
```

编译运行

```sh
[yeesuan003@spack test]$ gcc -o fftwtest fftwtest.c -I/es01/yeesuan/yeesuan003/software/fftw/include -I/es01/yeesuan/yeesuan003/software/fftw3d/include -L/es01/yeesuan/yeesuan003/software/fftw/lib -L/es01/yeesuan/yeesuan003/software/fftw3d/lib -lfftw3 -lfftw3f
[yeesuan003@spack test]$ ./fftwtest
5.00 + 10.00i   0.00 + 0.00i    0.00 + 0.00i    0.00 + 0.00i    0.00 + 0.00i
```

##  pgplot


修改```drivers.list```（去掉一些注释）

```
 17 ! GCDRIV 0 /GENICOM   Genicom 4410 dot-matrix printer, landscape
 18 !             Caution: use of GIDRIV may require a license from Unisys:
 19   GIDRIV 1 /GIF       GIF-format file, landscape
 20   GIDRIV 2 /VGIF      GIF-format file, portrait
 21 ! GLDRIV 1 /HPGL      Hewlett-Packard HP-GL plotters, landscape         Std F77

···
···
···

 36 ! MFDRIV 0 /FILE      PGPLOT graphics metafile
 37 ! NEDRIV 0 /NEXT      Computers running NeXTstep operating system
 38   NUDRIV 0 /NULL      Null device (no output)                           Std F77
 39 ! PGDRIV 0 /PGMF      PGPLOT metafile (new format, experimental)        Std F77
 40 ! PNDRIV 1 /PNG       Portable Network Graphics file                    C
 41 ! PNDRIV 2 /TPNG      Portable Network Graphics file - transparent background C
 42 ! PPDRIV 1 /PPM       Portable Pixel Map file, landscape
 43 ! PPDRIV 2 /VPPM      Portable PIxel Map file, portrait
 44   PSDRIV 1 /PS        PostScript printers, monochrome, landscape        Std F77
 45   PSDRIV 2 /VPS       Postscript printers, monochrome, portrait         Std F77
 46   PSDRIV 3 /CPS       PostScript printers, color, landscape             Std F77
 47   PSDRIV 4 /VCPS      PostScript printers, color, portrait              Std F77
 48 ! PXDRIV 0 /PRINTRONI Printronix P300 or P600 dot-matrix printer
 49 ! QMDRIV 1 /QMS       QUIC devices (QMS and Talaris), landscape         Std F77

···
···
···

 67 ! WDDRIV 1 /WD        X Window dump file, landscape
 68 ! WDDRIV 2 /VWD       X Window dump file, portrait
 69 ! WSDRIV 0 /WS        VAX workstations running VWS software             VMS
 70 ! X2DRIV 0 /XDISP     PGDISP or FIGDISP server for X workstations       C
 71   XWDRIV 1 /XWINDOW   Workstations running X Window System              C
 72   XWDRIV 2 /XSERVE    Persistent window on X Window System              C
 73 ! ZEDRIV 0 /ZETA      Zeta 8 Digital Plotter
 74 !
 75 ! The following drivers can only be used in PGPLOT installations on MS-DOS
 76 ! systems with appropriate hardware and software. Do not select these
 77 ! on UNIX or VMS systems.
```

将driver.list拷贝到安装目录下

```sh
cp drivers.list ~/software/pgplot/
```


```sh
# manual
export PGPLOT_DIR=/es01/yeesuan/yeesuan003/software/pgplot
export PGPLOT_DEV=/Xserve
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/pgplot/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/pgplot/include
export PGPLOT_FONT=/es01/yeesuan/yeesuan003/software/pgplot/grfont.dat
export PGPLOT_LIB="-L/es01/jnist/software/common/linux-centos7-skylake_avx512/gcc-8.5.0/libx11-1.7.0-2ovdqwa5f77pnonzmft6ttzmblxxibbe/lib -lX11 -L/es01/yeesuan/yeesuan003/software/pgplot/lib -lpgplot"


# spack
export PGPLOT_DIR=/es01/yeesuan/yeesuan003/software/pgplot
export PGPLOT_DEV=/Xserve
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/lhxone-test/spack/software/linux-centos7-skylake_avx512/gcc-8.5.0/pgplot-5.2.2-nwyu3d24mn4mrjgrg2khh34gwxsiegnc/lib/
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/lhxone-test/spack/software/linux-centos7-skylake_avx512/gcc-8.5.0/pgplot-5.2.2-nwyu3d24mn4mrjgrg2khh34gwxsiegnc/include
export PGPLOT_FONT=/es01/yeesuan/yeesuan003/software/pgplot/grfont.dat
export PGPLOT_LIB="-L/es01/jnist/software/common/linux-centos7-skylake_avx512/gcc-8.5.0/libx11-1.7.0-2ovdqwa5f77pnonzmft6ttzmblxxibbe/lib -lX11 -L/es01/yeesuan/yeesuan003/software/pgplot/lib -lpgplot"
```


测试，执行```./pgdemo1```出现下图

![](pgplot.png)


##  tempo2


```
./configure --prefix=/es01/yeesuan/yeesuan003/software/tempo2
make -j && make install
```

环境变量

```sh
export TEMPO2=/es01/yeesuan/yeesuan003/software/tempo2
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/tempo2/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/tempo2/include
```


##  libffi


```sh
./configure --prefix=/es01/yeesuan/yeesuan003/software/libffi
```

```sh
export LIBFFI=/es01/yeesuan/yeesuan003/software/libffi
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/libffi/lib64
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/libffi/include
```


##  glib

```sh
xz -d glib-2.48.1.tar.xz
tar xvf glib-2.48.1.tar
cd glib-2.48.1
./configure --prefix=/es01/yeesuan/yeesuan003/software/glib
```


```sh
cp /es01/yeesuan/yeesuan003/software/glib/lib/glib-2.0/include/glibconfig.h /es01/yeesuan/yeesuan003/software/glib/include/glib-2.0
export GLIB=/es01/yeesuan/yeesuan003/software/glib
export PATH=/es01/yeesuan/yeesuan003/software/glib/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/glib/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/glib/include
```


测试


```c
//glibtest.c
#include <stdio.h>
#include "glib.h"

int main(){
    g_printf("hello world\n");
    return 0;
}
```

```sh
gcc -o glibtest glibtest.c -I/es01/yeesuan/yeesuan003/software/glib/include/glib-2.0 -L/es01/yeesuan/yeesuan003/software/glib/lib -lglib-2.0
./glibtest
```

##  curl


```sh
./configure --prefix=/es01/yeesuan/yeesuan003/software/curl
```

```sh
export PATH=/es01/yeesuan/yeesuan003/software/curl/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/curl/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/curl/include
```

##  cfitsio

```sh
./configure --prefix=/es01/yeesuan/yeesuan003/software/cfitsio
```


```sh
export CFITSIO=/es01/yeesuan/yeesuan003/software/cfitsio
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/cfitsio/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/cfitsio/include
```

```cpp
// cfitsiotest.cpp
#include <string.h>
#include <stdio.h>
#include "fitsio.h"
int main(int argc, char *argv[])
{
    fitsfile *fptr;         
    char card[FLEN_CARD]; 
    int status = 0,  nkeys, ii;  /* MUST initialize status */
    fits_open_file(&fptr, argv[1], READONLY, &status);
    fits_get_hdrspace(fptr, &nkeys, NULL, &status);
    for (ii = 1; ii <= nkeys; ii++)  { 
      fits_read_record(fptr, ii, card, &status); /* read keyword */
      printf("%s\n", card);
    }
    printf("END\n\n");  /* terminate listing with END */
    fits_close_file(fptr, &status);
    if (status)          /* print any error messages */
        fits_report_error(stderr, status);
    return(status);
}
```



```sh
g++ -o cfitsiotest cfitsiotest.cpp -L/es01/yeesuan/yeesuan003/software/cfitsio/lib -L/es01/yeesuan/yeesuan003/software/curl/lib -I/es01/yeesuan/yeesuan003/software/cfitsio/include -lm -lcfitsio -lcurl

# 测试样例  https://www.cv.nrao.edu/~sransom/Parkes_70cm_PSR.fits
./cfitsiotest Parkes_70cm_PSR.fits
```

##  zlib


```sh
./configure --prefix=/es01/yeesuan/yeesuan003/software/zlib
```


```sh
export ZLIB=/es01/yeesuan/yeesuan003/software/zlib
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/zlib/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/zlib/include
```

##  libpng


```sh
./configure --prefix=/es01/yeesuan/yeesuan003/software/libpng LDFLAGS="-L/es01/yeesuan/yeesuan003/software/zlib/lib -lz"
```

```sh
export LIBPNG=/es01/yeesuan/yeesuan003/software/libpng
export PATH=/es01/yeesuan/yeesuan003/software/libpng/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/software/libpng/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/software/libpng/include
```


##  X11

>
>```sh
>./configure --prefix=/es01/yeesuan/yeesuan003/software/X11
>```
>
>>报错缺少pixman
>>```sh
>>wget https://gitlab.freedesktop.org/pixman/pixman/-/archive/master/pixman-master.tar.gz
>>./configure --prefix=/es01/yeesuan/yeesuan003/software/pixman
>>```
>>
>>>报错缺少autoconf
>>>```sh
>>>wget http://mirrors.kernel.org/gnu/autoconf/autoconf-latest.tar.gz
>>>./configure --prefix=/es01/yeesuan/yeesuan003/software/autoconf
>>>export PATH=/es01/yeesuan/yeesuan003/software/autoconf/bin${PATH:+:${PATH}}
>>>```
>最后的解决办法：
>>在另一台电脑上yum install x11，然后把/usr/lib64/X11文件打包拷贝过来



##  presto-c

配置环境变量

```sh
export PRESTO=/es01/yeesuan/yeesuan003/source/presto-4.0
export PYTHONPATH=/es01/yeesuan/yeesuan003/source/presto-4.0/lib/python
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/es01/yeesuan/yeesuan003/source/presto-4.0/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/es01/yeesuan/yeesuan003/source/presto-4.0/include
```


#### libpgplot.so:undefined

```makefile
# 尝试更换PGPLOTINC与PGPLOTLINK，使用源码编译安装或spack安装
# Include and link information for PGPLOT v5.X (including shared libs!)
# Typically you need to have your PGPLOT_DIR environment variable set
PGPLOTINC = -I/es01/yeesuan/yeesuan003/software/pgplot/include
PGPLOTLINK = -L/es01/yeesuan/yeesuan003/software/pgplot/lib -lcpgplot -lpgplot $(X11LINK) $(PNGLINK)

```

#### slalib 报错(未解决,但是编译成功了)


```log
tps2c.f(1): remark #15009: sla_tps2c_ has been targeted for automatic cpu dispatch
tpv2c.f(1): remark #15009: sla_tpv2c_ has been targeted for automatic cpu dispatch
ue2pv.f(1): remark #15009: sla_ue2pv_ has been targeted for automatic cpu dispatch
unpcd.f(1): remark #15009: sla_unpcd_ has been targeted for automatic cpu dispatch
v2tp.f(1): remark #15009: sla_v2tp_ has been targeted for automatic cpu dispatch
vdv.f(1): remark #15009: sla_vdv_ has been targeted for automatic cpu dispatch
vn.f(1): remark #15009: sla_vn_ has been targeted for automatic cpu dispatch
vxv.f(1): remark #15009: sla_vxv_ has been targeted for automatic cpu dispatch
xy2xy.f(1): remark #15009: sla_xy2xy_ has been targeted for automatic cpu dispatch
zd.f(1): remark #15009: sla_zd_ has been targeted for automatic cpu dispatch
gresid.F(2): remark #15009: sla_gresid_ has been targeted for automatic cpu dispatch
rm slalib/sla_test.o
cd slalib ; mpiifort -shared -o /es01/yeesuan/yeesuan003/source/presto-4.0/lib/libsla.so -fno-second-underscore *.o
ifort: command line warning #10006: ignoring unknown option '-fno-second-underscore'
cd slalib ; mpiifort -o sla_test sla_test.f -fno-second-underscore -L/es01/yeesuan/yeesuan003/source/presto-4.0/lib -lsla
ifort: command line warning #10006: ignoring unknown option '-fno-second-underscore'
sla_test.f(901): remark #15009: t_bear_ has been targeted for automatic cpu dispatch
sla_test.f(4397): remark #15009: t_planet_ has been targeted for automatic cpu dispatch
sla_test.f(5561): remark #15009: t_sep_ has been targeted for automatic cpu dispatch
sla_test.f(5770): remark #15009: t_svd_ has been targeted for automatic cpu dispatch
sla_test.f(6205): remark #15009: t_vecmat_ has been targeted for automatic cpu dispatch
slalib/sla_test
 sla_GMSTA test   fails:
   expected =      3.907497135648731579
   actual =        3.907497135670340072
 sla_PA test zenith fails:
   expected =      0.000000000000000000
   actual =        3.141592653589793116
 SLALIB validation failed!
make: *** [slalib] Error 1
```

#### undefined reference to 'cpg***' && undefined reference to `XSync'（未解决）



```log
mpif90 -g -fPIC -Wl,-rpath,/es01/yeesuan/yeesuan003/source/presto-4.0/lib -fopenmp -o /es01/yeesuan/yeesuan003/source/presto-4.0/bin/psrorbit powerplot.o xyline.o psrorbit.o -L/es01/yeesuan/yeesuan003/software/cfitsio/lib -L/es01/yeesuan/yeesuan003/software/curl/lib -lm -lcfitsio -lcurl -L/es01/yeesuan/yeesuan003/source/presto-4.0/lib -lpresto -L/es01/yeesuan/yeesuan003/software/fftw/lib -L/es01/yeesuan/yeesuan003/software/fftw3d/lib  -lfftw3f -lfftw3 -L/es01/yeesuan/yeesuan003/software/pgplot/lib  -lpgplot -L/usr/lib64/X11 -L/es01/yeesuan/yeesuan003/software/libpng/lib -lm
/es01/jnist/software/common/linux-centos7-haswell/gcc-4.8.5/binutils-2.37-7zolnrfrz4erct4f54lkgdpne3p6xj3z/bin/ld: psrorbit.o: in function `main':
/es01/yeesuan/yeesuan003/source/presto-4.0/src/psrorbit.c:205: undefined reference to `cpgend'
/es01/jnist/software/common/linux-centos7-haswell/gcc-4.8.5/binutils-2.37-7zolnrfrz4erct4f54lkgdpne3p6xj3z/bin/ld: powerplot.o: in function `powerplot':
/es01/yeesuan/yeesuan003/source/presto-4.0/src/powerplot.c:27: undefined reference to `cpgenv'
/es01/jnist/software/common/linux-centos7-haswell/gcc-4.8.5/binutils-2.37-7zolnrfrz4erct4f54lkgdpne3p6xj3z/bin/ld: /es01/yeesuan/yeesuan003/source/presto-4.0/src/powerplot.c:30: undefined reference to `cpgscf'


···
···
···
···

/es01/jnist/software/common/linux-centos7-haswell/gcc-4.8.5/binutils-2.37-7zolnrfrz4erct4f54lkgdpne3p6xj3z/bin/ld: /es01/yeesuan/yeesuan003/software/pgplot/lib/libpgplot.so: undefined reference to `XGetSelectionOwner'
/es01/jnist/software/common/linux-centos7-haswell/gcc-4.8.5/binutils-2.37-7zolnrfrz4erct4f54lkgdpne3p6xj3z/bin/ld: /es01/yeesuan/yeesuan003/software/pgplot/lib/libpgplot.so: undefined reference to `XCloseDisplay'
/es01/jnist/software/common/linux-centos7-haswell/gcc-4.8.5/binutils-2.37-7zolnrfrz4erct4f54lkgdpne3p6xj3z/bin/ld: /es01/yeesuan/yeesuan003/software/pgplot/lib/libpgplot.so: undefined reference to `XSync'
/es01/jnist/software/common/linux-centos7-haswell/gcc-4.8.5/binutils-2.37-7zolnrfrz4erct4f54lkgdpne3p6xj3z/bin/ld: /es01/yeesuan/yeesuan003/software/pgplot/lib/libpgplot.so: undefined reference to `XMapRaised'
/es01/jnist/software/common/linux-centos7-haswell/gcc-4.8.5/binutils-2.37-7zolnrfrz4erct4f54lkgdpne3p6xj3z/bin/ld: /es01/yeesuan/yeesuan003/software/pgplot/lib/libpgplot.so: undefined reference to `XPutImage'
collect2: error: ld returned 1 exit status
make: *** [psrorbit] Error 1
```

1. 尝试使用spack load pgplot，无效
2. 尝试更换pgplot版本（原版本pgplot5.2），更换为[552版本](ftp://ftp.astro.caltech.edu/pub/pgplot/pgplot522.tar.gzpgplot)，无效
3. 尝试从源码编译x11，无效，找不到```X11/Xos.h```


#### 测试

```sh
(base) [yeesuan003@spack bin]$ ./readfile
   [-page] [-byte] [-b] [-float] [-f] [-double] [-d] [-fcomplex] [-fc] [-dcomplex] [-dc] [-short] [-s] [-int] [-i] [-long] [-l] [-rzwcand] [-rzw] [-bincand] [-bin] [-position] [-pos] [-pkmb] [-bcpm] [-wapp] [-spigot] [-filterbank] [-psrfits] [-fortran] [-index [index]] [-nph nph] [--] file
      Reads raw data from a binary file and displays it on stdout.
          -page: Paginate the output like 'more'
          -byte: Raw data in byte format
             -b: Raw data in byte format
         -float: Raw data in floating point format
             -f: Raw data in floating point format
        -double: Raw data in double precision format
             -d: Raw data in double precision format
      -fcomplex: Raw data in float-complex format
            -fc: Raw data in float-complex format
      -dcomplex: Raw data in double-complex format
            -dc: Raw data in double-complex format
         -short: Raw data in short format
             -s: Raw data in short format
           -int: Raw data in integer format
             -i: Raw data in integer format
          -long: Raw data in long format
             -l: Raw data in long format
       -rzwcand: Raw data in rzw search candidate format
           -rzw: Raw data in rzw search candidate format
       -bincand: Raw data in bin search candidate format
           -bin: Raw data in bin search candidate format
      -position: Raw data in position struct format
           -pos: Raw data in position struct format
          -pkmb: Raw data in Parkes Multibeam format
          -bcpm: Raw data in BCPM format
          -wapp: Raw data in WAPP format
        -spigot: Raw data in Spigot Card format
    -filterbank: Raw data in SIGPROC filterbank format
       -psrfits: Raw data in PSRFITS format
       -fortran: Raw data was written by a fortran program
         -index: The range of objects to display
                 0...2 int values between -1 and oo
                 default: `0' ` -1'
           -nph: 0th FFT bin amplitude (for 'RZW' data)
                 1 double value
                 default: `1.0'
           file: Input data file name.
                 1 value
  version: 12Mar10
```

## Anaconda

```sh
wget https://repo.anaconda.com/archive/Anaconda3-2021.11-Linux-x86_64.sh
```




## pySLALIB

```sh
wget https://files.pythonhosted.org/packages/3e/e1/e731f0b69d9145faa2aa321a82ff27d6da1d929ac02d7206ca5ae6ec23e3/pySLALIB-1.0.4.tar.gz
```




## presto-python

修改`setup.py`

```py
ppgplot_libraries = ["gfortran", "cpgplot", "pgplot", "X11", "png", "m"]
ppgplot_library_dirs = ["/usr/lib64", "/es01/jnist/software/common/linux-centos7-skylake_avx512/gcc-8.5.0/libx11-1.7.0-2ovdqwa5f77pnonzmft6ttzmblxxibbe/lib"]

presto_libraries = ["presto", "fftw3", "m"]
presto_library_dirs = ["/es01/yeesuan/yeesuan003/software/fftw/lib", "/es01/jnist/software/common/linux-centos7-skylake_avx512/gcc-8.5.0/libx11-1.7.0-2ovdqwa5f77pnonzmft6ttzmblxxibbe/lib"]

####  ~Lines~  #####
####  ~Lines~  #####
####  ~Lines~  #####
####  ~Lines~  #####
####  ~Lines~  #####

setup(name="presto",
      version=version,
      install_requires=['numpy', 'future', 'six', 'scipy', 'matplotlib', 'astropy', 'pyslalib'],
      # scripts=scripts,
      description="Python interfaces to PGPLOT and PRESTO",
      author="Scott Ransom (ppgplot from Nick Patavlis)",
      author_email="sransom@nrao.edu",
      url="https://github.com/scottransom/presto",
      packages=['presto', 'presto.ppgplot', 'presto.presto', 'presto.singlepulse'],
      package_dir={'presto.ppgplot': 'python/ppgplot_src',
                   'presto.presto': 'python/presto_src',
                   'presto': 'python/presto',
                   'presto.singlepulse': 'python/presto/singlepulse',
                   },
      package_data={'presto': ['*.json']},
      ext_modules=[ext_ppgplot, ext_presto, ext_fftfit])
```

执行以下命令安装

```sh
CC='gcc -std=c99' python3 setup.py install --home=${PRESTO}
```

环境变量配置

```sh
export export PYTHONPATH=/es01/yeesuan/yeesuan003/source/presto-4.0/lib/python/presto-4.0-py3.9-linux-x86_64.egg
```

测试

```sh
(base) [yeesuan003@spack presto-4.0]$ cd bin/
(base) [yeesuan003@spack bin]$ ./DDplan.py

    usage:  DDplan.py [options] [raw PSRFITS or filterbank file]
  [-h, --help]                    : Display this help
  [-o outfile, --outfile=outfile] : Output .eps plot file (default is xwin)
  [-l loDM, --loDM=loDM]          : Low DM to search   (default = 0 pc cm-3)
  [-d hiDM, --hiDM=HIDM]          : High DM to search  (default = 1000 pc cm-3)
  [-f fctr, --fctr=fctr]          : Center frequency   (default = 1400MHz)
  [-b BW, --bw=bandwidth]         : Bandwidth in MHz   (default = 300MHz)
  [-n #chan, --numchan=#chan]     : Number of channels (default = 1024)
  [-k blocklen, --blocklen=#spec] : Spectra per subint (for downsampling) (default = 1024)
  [-c cDM, --cohdm=cDM]           : Coherent DM in each chan  (default = 0.0)
  [-t dt, --dt=dt]                : Sample time (s)    (default = 0.000064 s)
  [-s subbands, --subbands=nsub]  : Number of subbands (default = #chan)
  [-r resolution, --res=res]      : Acceptable time resolution (ms)
  [-w, --write]                   : Write a dedisp.py file for the plan

  The program generates a good plan for de-dispersing raw data.  It
  trades a small amount of sensitivity in order to save computation costs.
  It will determine the observation parameters from the raw data file
  if it exists.


```


## fitsio

地址：`https://github.com/esheldon/fitsio`

解压并进入`cfitsio3490`目录，执行`./configure`,`make`,`make install`

进入上层目录，执行`python3 setup.py install`


## mpi4py

安装

```sh
pip3 install mpi4py
```

测试

```py
# mpi_helloworld.py

from mpi4py import MPI

comm = MPI.COMM_WORLD
size = comm.Get_size()
rank = comm.Get_rank()
node_name = MPI.Get_processor_name() # get the name of the node

print('Hello world from process {0} at {1}.'.format(rank, node_name))
```

```sh
mpiexec -n 3 python3 test.py
```
