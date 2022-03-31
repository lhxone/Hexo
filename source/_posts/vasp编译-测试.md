---
title: vasp编译&测试
date: 2022-03-02 13:55:45
tags: 实习日记
---

# 编译前准备

- 执行以下命令以确保环境中含有```icc```,```icpc```,```mpiifort```等


```shell
spack load openmpi@4.1.1
spack load intel-oneapi-mpi@2021.4.0%gcc@4.8.5
spack load intel-mpi@2019.10.317%gcc@4.8.5
spack load intel-parallel-studio@cluster.2019.5
```


- 源码在```/es01/jnist/mirror```下可以找到,本次安装的版本为```5.4.4```


## mkl(Math Kernel Library)

其地址在```/es01/jnist/software/general/linux-centos7-cascadelake/gcc-10.2.0/intel-parallel-studio-cluster.2018.1-yn56mgyhtg5knpfmpgtcax3o3wfonhhp/compilers_and_libraries_2018.1.163/linux/mkl```,在使用前需载入```intel-parallel-studio@cluster.2019.5```

## 并行fftw

这里选择自行编译并行fftw

将```fftw```源码解压到```/es01/yeesuan/yeesuan003/source/```之下,执行以下命令进行编译并行fftw

```shell
./configure --prefix=/es01/yeesuan/yeesuan003/lhxone-test/software/fftw-3.3.9 CC=icc F77=ifort MPICC=mpiicc --enable-mpi
make -j 30
make install
cd /es01/yeesuan/yeesuan003/lhxone-test/software
mv fftw-3.3.9 fftw-mpi
```



# 编译

## 获取源码

源码在```/es01/jnist/mirror```下可以找到,本次安装的版本为```5.4.4```,将源码移到```~/source```并解压

```shell
cp vasp/vasp.5.4.4.tgz ~/source
tar xzvf vasp.5.4.4.tgz
```




## makefile配置

在```vasp.5.4.4/arch```之下,提供了不同的```makefile```模版,这里使用```makefile.include.linux_intel```,并在此基础上对其进行修改

```shell
cp arch/makefile.include.linux_intel makefile.include
```


在```makefile.include```中,修改以下路径

```sh
 24 $(MKLROOT) = /es01/jnist/software/general/linux-centos7-cascadelake/gcc-10.2.0/intel-parallel-studio-cluster.2018.1-yn56mgyhtg5knpfmpgtcax3o3wfonhhp/compilers_and_libraries_2018.1.163/linux/mkl
 25 MKL_PATH   = $(MKLROOT)/lib/intel64
 26 BLAS       = -L$(MKL_PATH) -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread
 27 LAPACK     = -L$(MKL_PATH) -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread
 28 
 29 
 30 #BLACS      = -L$(MKL_PATH) -lmkl_blacs_openmpi_lp64
 31 BLACS       = -lmkl_blacs_intelmpi_lp64
 32 
 33 
 34 #SCALAPACK  = $(MKL_PATH)/libmkl_scalapack_lp64.a $(BLACS)
 35 SCALAPACK   = $(MKL_PATH)/libmkl_scalapack_lp64.a $(BLACS)
 36 OBJECTS    = fftmpiw.o fftmpi_map.o fft3dlib.o fftw3d.o
 37 
 38 INCS       =-I/es01/yeesuan/yeesuan003/lhxone-test/software/fftw-mpi/include
 39 
 40 LLIBS      = $(SCALAPACK) $(LAPACK) $(BLAS)
```

我的makefile.include[下载地址](http://124.222.217.156/static/file/makefile.include)

编译完成后,在```bin```目录下生成```vasp_std```,```vasp_gam```以及```vasp_ncl```.

```
bin
├── vasp_gam
├── vasp_ncl
└── vasp_std
```

将其添加到环境变量

```shell
vim ~/.bashrc
# vasp
export PATH="/es01/yeesuan/yeesuan003/source/vasp.5.4.4/bin:$PATH"
source ~/.bashrc
```


# 测试运行

[测试样例下载地址](http://124.222.217.156/static/file/vasp_suanli.tar.gz)

## 本地运行

```shell
wget http://124.222.217.156/static/file/vasp_suanli.tar.gz
tar xzvf vasp_suanli.tar.gz
cd vasp_suanli
vasp_std
```

## 使用slurm提交作业

这里采用```sbatch```提交作业的方式,脚本为```submit.sh```,其内容如下

```shell
cd vasp_suanli
vim submit.sh
#!/bin/bash
#SBATCH -J vasp_job
#SBATCH -o vasp_on_slurm_%j.log
#SBATCH -e vasp_on_slurm_error_%j.log
echo Running on hosts
echo Time is `date`
echo Directory is $PWD
echo This job runs on the following nodes:
echo $SLURM_JOB_NODELIST
echo Starting
vasp_std
:wq
sbatch -n1  submit.sh
```

输出日志文件为```vasp_on_slurm_381.log```,```vasp_on_slurm_error_381.log```


```vasp_on_slurm_381.log```


```log
Running on hosts
Time is 2022年 03月 02日 星期三 15:27:23 CST
Directory is /es01/yeesuan/yeesuan003/vasp_suanli
This job runs on the following nodes:
node1
Starting
 running on    1 total cores
 distrk:  each k-point on    1 cores,    1 groups
 distr:  one band on    1 cores,    1 groups
 using from now: INCAR     
 vasp.5.4.4.18Apr17-6-g9f103f2a35 (build Mar 02 2022 10:43:08) complex          
  
 POSCAR found type information on POSCAR  C  H 
 POSCAR found :  2 types and       8 ions
 scaLAPACK will be used
 LDA part: xc-table for Pade appr. of Perdew
 POSCAR, INCAR and KPOINTS ok, starting setup
 FFT: planning ...
 WAVECAR not read
 WARNING: random wavefunctions but no delay for mixing, default for NELMDL
 prediction of wavefunctions initialized - no I/O
 entering main loop
       N       E                     dE             d eps       ncg     rms          rms(c)
RMM:   1     0.158376549325E+03    0.15838E+03   -0.25387E+03    12   0.538E+02
RMM:   2     0.724768511531E+02   -0.85900E+02   -0.98209E+02    12   0.133E+02
RMM:   3     0.203902846357E+02   -0.52087E+02   -0.57350E+02    12   0.108E+02
RMM:   4    -0.269785606514E+02   -0.47369E+02   -0.32864E+02    12   0.101E+02
RMM:   5    -0.402228244268E+02   -0.13244E+02   -0.12484E+02    12   0.730E+01
RMM:   6    -0.440000594840E+02   -0.37772E+01   -0.34955E+01    12   0.429E+01
RMM:   7    -0.450389920389E+02   -0.10389E+01   -0.10145E+01    12   0.245E+01
RMM:   8    -0.453706927154E+02   -0.33170E+00   -0.32172E+00    12   0.140E+01
RMM:   9    -0.455134036475E+02   -0.14271E+00   -0.14058E+00    25   0.810E+00
RMM:  10    -0.455323634295E+02   -0.18960E-01   -0.18213E-01    28   0.256E+00
RMM:  11    -0.455326567164E+02   -0.29329E-03   -0.26452E-03    25   0.296E-01
RMM:  12    -0.455326951120E+02   -0.38396E-04   -0.37298E-04    26   0.975E-02    0.106E+01
RMM:  13    -0.413835805330E+02    0.41491E+01   -0.75991E+00    24   0.145E+01    0.551E+00
RMM:  14    -0.405264166634E+02    0.85716E+00   -0.24824E+00    24   0.803E+00    0.266E+00
RMM:  15    -0.403450150777E+02    0.18140E+00   -0.28285E-01    24   0.318E+00    0.295E-01
RMM:  16    -0.403393957409E+02    0.56193E-02   -0.30123E-02    24   0.957E-01    0.265E-01
RMM:  17    -0.403475645736E+02   -0.81688E-02   -0.92386E-03    24   0.542E-01    0.149E-01
RMM:  18    -0.403517032644E+02   -0.41387E-02   -0.15901E-02    24   0.693E-01    0.233E-01
RMM:  19    -0.403523808258E+02   -0.67756E-03   -0.94824E-03    24   0.498E-01    0.648E-02
RMM:  20    -0.403532027230E+02   -0.82190E-03   -0.20094E-04    24   0.104E-01    0.430E-02
RMM:  21    -0.403533437443E+02   -0.14102E-03   -0.38563E-04    24   0.100E-01    0.107E-02
RMM:  22    -0.403534062939E+02   -0.62550E-04   -0.37874E-05    24   0.324E-02    0.583E-03
RMM:  23    -0.403534085406E+02   -0.22467E-05   -0.56604E-06    23   0.123E-02    0.123E-03
RMM:  24    -0.403534159726E+02   -0.74320E-05   -0.23326E-06    18   0.860E-03    0.143E-03
RMM:  25    -0.403534159979E+02   -0.25234E-07   -0.23628E-07    17   0.353E-03
    1 T=   292. E= -.40167351E+02 F= -.40431391E+02 E0= -.40431391E+02  EK= 0.26404E+00 SP= 0.00E+00 SK= 0.00E+00
 bond charge predicted
       N       E                     dE             d eps       ncg     rms          rms(c)
RMM:   1    -0.403421091978E+02    0.11307E-01   -0.80579E-01    24   0.774E+00    0.216E-01
RMM:   2    -0.403419842052E+02    0.12499E-03   -0.58599E-03    24   0.583E-01    0.140E-01
RMM:   3    -0.403417880987E+02    0.19611E-03   -0.27993E-03    24   0.315E-01    0.487E-02
RMM:   4    -0.403417097041E+02    0.78395E-04   -0.15132E-04    24   0.776E-02    0.282E-02
RMM:   5    -0.403417288527E+02   -0.19149E-04   -0.16732E-04    24   0.709E-02    0.165E-02
RMM:   6    -0.403417179833E+02    0.10869E-04   -0.50066E-05    24   0.373E-02    0.484E-03
RMM:   7    -0.403417237679E+02   -0.57845E-05   -0.66094E-06    22   0.157E-02    0.408E-03
RMM:   8    -0.403417231446E+02    0.62322E-06   -0.11301E-06    17   0.615E-03
    2 T=   167. E= -.40267330E+02 F= -.40418524E+02 E0= -.40418524E+02  EK= 0.15119E+00 SP= 0.00E+00 SK= 0.00E+00
 bond charge predicted
 prediction of wavefunctions
       N       E                     dE             d eps       ncg     rms          rms(c)
RMM:   1    -0.403217475988E+02    0.19976E-01   -0.20868E-01    24   0.394E+00    0.937E-02
RMM:   2    -0.403217564222E+02   -0.88234E-05   -0.12861E-03    24   0.266E-01    0.593E-02
RMM:   3    -0.403217118401E+02    0.44582E-04   -0.31401E-04    24   0.112E-01    0.111E-02
RMM:   4    -0.403217092852E+02    0.25549E-05   -0.10095E-05    25   0.231E-02    0.729E-03
RMM:   5    -0.403217105375E+02   -0.12523E-05   -0.84718E-06    24   0.157E-02    0.320E-03
RMM:   6    -0.403217105390E+02   -0.14845E-08   -0.25112E-06    18   0.877E-03
    3 T=   206. E= -.40211156E+02 F= -.40397468E+02 E0= -.40397468E+02  EK= 0.18631E+00 SP= 0.00E+00 SK= 0.00E+00
 bond charge predicted
 prediction of wavefunctions
       N       E                     dE             d eps       ncg     rms          rms(c)
RMM:   1    -0.403117808886E+02    0.99296E-02   -0.39721E-01    24   0.537E+00    0.158E-01
RMM:   2    -0.403117625130E+02    0.18376E-04   -0.29369E-03    24   0.398E-01    0.104E-01
RMM:   3    -0.403116565207E+02    0.10599E-03   -0.11484E-03    24   0.208E-01    0.277E-02
RMM:   4    -0.403116311378E+02    0.25383E-04   -0.55540E-05    24   0.497E-02    0.164E-02
RMM:   5    -0.403116387094E+02   -0.75717E-05   -0.65271E-05    24   0.434E-02    0.890E-03
RMM:   6    -0.403116363827E+02    0.23268E-05   -0.17024E-05    24   0.217E-02    0.378E-03
RMM:   7    -0.403116417002E+02   -0.53175E-05   -0.29605E-06    19   0.107E-02    0.349E-03
RMM:   8    -0.403116414128E+02    0.28742E-06   -0.12248E-06    17   0.640E-03
    4 T=   196. E= -.40208892E+02 F= -.40385911E+02 E0= -.40385911E+02  EK= 0.17702E+00 SP= 0.00E+00 SK= 0.00E+00
 bond charge predicted
 prediction of wavefunctions
       N       E                     dE             d eps       ncg     rms          rms(c)
RMM:   1    -0.403062082681E+02    0.54334E-02   -0.16770E-02    24   0.119E+00    0.417E-02
RMM:   2    -0.403061969844E+02    0.11284E-04   -0.15896E-04    24   0.916E-02    0.270E-02
RMM:   3    -0.403061936019E+02    0.33826E-05   -0.10873E-04    24   0.615E-02    0.101E-02
RMM:   4    -0.403061899714E+02    0.36305E-05   -0.84127E-06    24   0.170E-02    0.621E-03
RMM:   5    -0.403061909422E+02   -0.97076E-06   -0.89813E-06    24   0.161E-02
    5 T=   201. E= -.40196935E+02 F= -.40379213E+02 E0= -.40379213E+02  EK= 0.18228E+00 SP= 0.00E+00 SK= 0.00E+00
 bond charge predicted
 prediction of wavefunctions
 wavefunctions rotated
```


