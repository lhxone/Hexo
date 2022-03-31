---
title: ABACUS编译&测试
date: 2022-02-28 09:04:07
tags: 实习日记
---

# 安装

## 安装依赖

需要载入```intel-parallel-studio@cluster.2019.5```、```intel-mpi```以及```intel=oneapi-mpi```

```shell
mkdir -p ~/code/intel
cd ~/code/intel
ROOT=$PWD
mkdir $ROOT/source

###fftw3
cd $ROOT/source
wget https://cndaqiang.gitee.io/packages//mirrors/fftw/fftw-3.3.4.tar.gz
tar xzvf fftw-3.3.4.tar.gz
cd fftw-3.3.4
./configure --prefix=$ROOT/fftw-3.3.4 CC=icc  MPICC=mpiicc --enable-mpi
make  install -j20

###boost
cd $ROOT/source
wget https://cndaqiang.gitee.io/packages//mirrors/boost/boost_1_66_0.tar.bz2
tar --bzip2 -xf boost_1_66_0.tar.bz2
cd boost_1_66_0/
./bootstrap.sh --prefix=$ROOT/boost_1_66_0 --with-toolset=intel-linux
./b2 install -j20


###elpa
TAG=2016.05.004
ROOT=~/code/intel
cd $ROOT/source
if [ ! -f elpa-$TAG.tar.gz ]
then
    wget https://elpa.mpcdf.mpg.de/software/tarball-archive/Releases/$TAG/elpa-$TAG.tar.gz
fi
rm -rf elpa-$TAG
rm -rf $ROOT/elpa-$TAG
tar xzvf elpa-$TAG.tar.gz
cd elpa-$TAG
MKL_HOME=$MKLROOT
if [ ! -f configure ]
then
    ./autogen.sh
fi

rm -rf build
mkdir build
cd build

../configure --prefix=$ROOT/elpa-$TAG  CC=mpiicc CXX=mpiicpc FC=mpiifort   FCFLAGS="-mkl=cluster"
#使用 FCFLAGS="-mkl=cluster" 或者下面的方式定义SCALAPACK都是可以的
#  SCALAPACK_LDFLAGS="-L$MKL_HOME/lib/intel64 -lmkl_scalapack_lp64 -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lmkl_blacs_intelmpi_lp64 -lpthread -lm -Wl,-rpath,$MKL_HOME/lib/intel64" \
#  SCALAPACK_FCFLAGS="-L$MKL_HOME/lib/intel64 -lmkl_scalapack_lp64 -lmkl_intel_lp64 -lmkl_sequential  -lmkl_core -lmkl_blacs_intelmpi_lp64 -lpthread -lm -I$MKL_HOME/include/intel64/lp64" \
#--enable-openmp
make install -j20   #多线程加速编译
```


## 安装


```shell
#cd ~/code/abacus/ABACUS_v2.1.0/source/

cd $ROOT
if [ ! -f ABACUS_v2.1.0.tar.gz ]
then
wget https://abacus.ustc.edu.cn/uploadfile/ABACUS_v2.1.0.tar.gz
fi
rm -rf ABACUS_v2.1.0
tar xzvf ABACUS_v2.1.0.tar.gz
cd ABACUS/source

cat > Makefile.vars << EOF
CPLUSPLUS      = icpc
# C++ compiler
CPLUSPLUS_MPI  = mpiicpc
# intel mpi or mpich
FORTRAN       = ifort
# Fortran compiler
# 如果报错先检查是否正确导入模块
LAPACK_DIR = $MKLROOT
# intel mkl
FFTW_DIR       = $ROOT/fftw-3.3.4
# lib fftw
BOOST_DIR      = $ROOT/boost_1_66_0
# lib boost
ELPA_DIR = $ROOT/elpa-$TAG
# lib elpa
EOF
make
```

# 测试运行

在```ABACUS/example```下有几个测试样例,选取第一个测试样例

```shell
$ mpirun -np 10 ABACUS.mpi.2.1.0 | tee result
 *********************************************************
 *                                                       *
 *                  WELCOME TO ABACUS                    *
 *                                                       *
 *            'Atomic-orbital Based Ab-initio            *
 *                  Computation at UStc'                 *
 *                                                       *
 *          Website: http://abacus.ustc.edu.cn/          *
 *                                                       *
 *********************************************************
 Mon Feb 28 09:37:29 2022
 MAKE THE DIR         : OUT.ABACUS/
 DONE(0.0481156  SEC) : SETUP UNITCELL
 DONE(0.0736459  SEC) : SYMMETRY
 DONE(0.0949633  SEC) : INIT K-POINTS
 ---------------------------------------------------------
 This calculation is self-consistent
 ---------------------------------------------------------
 SPIN    KPOINTS         PROCESSORS  
 1       8               10          
 ---------------------------------------------------------
 Use plane wave basis
 ---------------------------------------------------------
 ELEMENT NATOM       XC          
 Si      2           PZ-LDA
 ---------------------------------------------------------
 Initial plane wave basis and FFT box
 ---------------------------------------------------------
 DONE(0.116594   SEC) : INIT PLANEWAVE
 UNIFORM GRID DIM     : 36 * 36 * 36
 UNIFORM GRID DIM(BIG): 36 * 36 * 36
 MEMORY FOR PSI (MB)  : 0.0820312
 DONE(0.120233   SEC) : LOCAL POTENTIAL
 DONE(0.129086   SEC) : NON-LOCAL POTENTIAL
 START POTENTIAL      : atomic
 DONE(0.135346   SEC) : INIT POTENTIAL
 DONE(0.17863    SEC) : INIT BASIS
 -------------------------------------------
 SELF-CONSISTENT : 
 -------------------------------------------
 ITER   ETOT(eV)       EDIFF(eV)      DRHO2      CG_ITER    TIME(S)    
 CG1    -2.192279e+02  0.000000e+00   6.928e-02  3.000e+00  4.000e-02  
 CG2    -2.154958e+02  3.732172e+00   5.745e-03  2.750e+00  3.000e-02  
 CG3    -2.155043e+02  -8.536782e-03  2.622e-05  2.406e+00  2.000e-02  
 CG4    -2.155047e+02  -3.698259e-04  5.403e-07  3.875e+00  3.000e-02  
 CG5    -2.155047e+02  -1.855886e-06  1.931e-08  2.875e+00  3.000e-02  
 CG6    -2.155047e+02  -7.198581e-08  1.776e-10  2.969e+00  2.000e-02  

  |CLASS_NAME---------|NAME---------------|TIME(Sec)-----|CALLS----|AVG------|PER%-------
 A DC_Driv             solve_eachf         0.24           1         0.24      68        %
 B Run_Frag            frag_pw_line        0.24           1         0.24      68        %
 X FFT                 FFT3D               0.13           1876      7.1e-05   37        %
 H Hamilt_PW           h_psi               0.14           828       0.00017   38        %
 C Ions                opt_ions_pw         0.18           1         0.18      50        %
 D electrons           self_consistent     0.18           1         0.18      50        %
 E electrons           c_bands             0.14           6         0.023     38        %
 F Hamilt              diago               0.13           48        0.0028    37        %
 ----------------------------------------------------------------------------------------

 START  Time  : Mon Feb 28 09:37:29 2022
 FINISH Time  : Mon Feb 28 09:37:30 2022
 TOTAL  Time  : 1
 SEE INFORMATION IN : OUT.ABACUS/
```

## 输入与输出

```shell
$ tree
.
├── INPUT           #控制参数
├── KPT             #k个点
├── OUT.ABACUS      #输出结果
│   ├── INPUT
│   ├── istate.info
│   ├── running_scf.log
│   ├── Si
│   │   ├── Si.NONLOCAL
│   │   ├── Si-P.ORBITAL
│   │   ├── Si-S.ORBITAL
│   │   └── v_loc_g.dat
│   ├── STRU_READIN_ADJUST.cif
│   └── warning.log
├── README          #说明文档
├── result
├── Si.pz-vbc.UPF
└── STRU            #结构
```



