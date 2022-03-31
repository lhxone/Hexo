---
title: gromacs编译&测试
date: 2022-02-23 14:12:12
tags: 实习日记
---


## 编译gromacs(单精度版本)


需要的编译环境为：

- 串行编译器：Intel Compiler / GNU Compiler
- MPI编译器：mpich / openmpi（如果是MPI版本）
- cmake
- fftw
- CUDA（如果是GPU版本）
- plumed（如果开启plumed）

```shell
tar -zxvf gromacs-5.1.4.tar.gz # 以5.1.4版本为例
cd gromacs-5.1.4 
mkdir build
cd build
cmake .. -DCMAKE_INSTALL_PREFIX=$HOME/software/gromacs-5.1.4 \
  -DCMAKE_C_COMPILER=icc -DCMAKE_CXX_COMPILER=icpc \
  -DGMX_DOUBLE=off \
  -DGMX_FFT_LIBRARY=fftw3 \
  -DFFTWF_LIBRARY="$HOME/fftw/lib/libfftw3f.a" \
  -DFFTWF_INCLUDE_DIR="$HOME/fftw/include/"  \
  -DGMX_MPI=on \
  -DGMX_GPU=off \
  2>&1 | tee cm.log
make -j12 2>&1 | tee m.log
make install 2>&1 | tee mi.log
```

|参数 |  含义|
| -- | ---|
|	-DCMAKE_INSTALL_PREFIX |	安装目录	|
|-DCMAKE_C_COMPILER=icc	| 使用 icc 编译 c  |
|-DCMAKE_CXX_COMPILER=icpc |	使用 icpc 编译 cpp	|
|-DGMX_DOUBLE=off |	关闭double，编译单精度版本	|
|-DGMX_FFT_LIBRARY=fftw3 |	使用 fftw3	|
|-DFFTWF_LIBRARY="$HOME/fftw/lib/libfftw3f.a" |	fftw库,请改为自己的安装目录	|
|-DFFTWF_INCLUDE_DIR="$HOME/fftw/include" |	fftw的头文件目录,请改为自己的安装目录	|
|-DGMX_MPI=on |	开启 MPI 支持	|
|-DGMX_GPU=off |	关闭 GPU 支持   |


在安装完成后,运行以下脚本以配置环境:

```shell
$ source $HOME/software/gromacs-5.1.4/bin/GMXRC
$ gmx_mpi --version #输出版本信息
```

## gromacs测试样例

测试样例为~/gromacs. 

首先执行以下命令生成可执行.tpr 文件:

```shell
$ gmx_mpi grompp -f md0.mdp -c pr.gro -p SYS.top -o md.tpr
```

grompp参数列表:   

|  参数 |含义  |
|  ---- | ----  |
| -f[<.mdp>] (grompp.mdp)  | 带有 MD 参数的 grompp 输入文件 |
| -c[<.gro/.g96/...>] (conf.gro)  | 结构文件:gro g96 pdb brk ent esp tpr |
| -p[<.top>] (topol.top)   | 拓扑文件 |
| -o[<.tpr>] (topol.tpr)  | 便携式 xdr 运行输入文件 |
|-po[<.mdp>] (mdout.mdp)        |   带有 MD 参数的 grompp 输入文件  |

然后执行以下命令进行计算:

```shell
$ gmx_mpi mdrun -deffnm md
```


mdrun 参数列表:


|参数|含义|
|---|---|
|-deffnm <字符串>|为所有文件选项设置默认文件名|
|-s [<.tpr>] (topol.tpr)|便携式 xdr 运行输入文件|
|-g [<.log>] (md.log)|日志文件|

## mpicxx/mpirun

### mpicxx

```mpicxx```可用于编译和链接用 C++ 编写的 MPI 程序,在使用前需载入 ```inteloneapi-mpi```

hello.c

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char **argv) {

    // 初始化 MPI 环境 
    MPI_Init(NULL, NULL);

    // 通过调用以下方法来得到所有可以工作的进程数量 
    int world_size;
    MPI_Comm_size(MPI_COMM_WORLD, &world_size);

    // 得到当前进程的秩 
    int world_rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);

    // 得到当前进程的名字 
    char processor_name[MPI_MAX_PROCESSOR_NAME];
    int name_len;
    MPI_Get_processor_name(processor_name, &name_len);
    // 打印一条带有当前进程名字,秩以及 
    // 整个 communicator 的大小的 hello world 消息。 
    printf("Hello world from processor %s, rank %d out of %d processors\n", processor_name, world_rank, world_size);

    // 释放 MPI 的一些资源 
    MPI_Finalize();

}
```


编译 hello.c 文件

```shell
$ mpicxx -c hello.c
```

链接

```shell
$ mpicxx -o hello hello.o
```

组合编译和链接

```shell
$ mpicxx -o hello hello.c
```


mpicxx 参数列表:

|参数|含义|
|---|---|
|-show|在不运行的情况下展示使用的指令|
|-c [<.c/.cxx/.cpp>] (hello.c)|编译|
|-o [<.o>] (hello.o)|链接|


### mpirun

orterun、mpirun、mpiexec 在 Open MPI 中执行串行和并行作业。 oshrun, shmemrun 在 Open SHMEM 中执行串行和并行作业。

mpirun 参数列表:

|参数|含义|
|---|---|
|-q, --quiet|在应用程序执行期间减少来自orterun的信息|
|-v, --verbose|显示更多信息|
|-N <num>|在每个分配的node上启动num个进程(与npernode 相同)|


在本地直接执行 hello

```shell
$ ./hello 
Hello world from processor master1, rank 0 out of 1 processors
```

使用 mpirun 执行 hello

```shell
$ mpirun -n 4 ./hello 
Hello world from processor master1, rank 1 out of 4 processors 
Hello world from processor master1, rank 3 out of 4 processors 
Hello world from processor master1, rank 0 out of 4 processors 
Hello world from processor master1, rank 2 out of 4 processors
```

## slurm

Slurm(Simple Linux Utility for Resource Management，http://slurm.schedmd.com/) 是开源的、具有容错性和高度可扩展大型和小型 Linux 集群资源管 理和作业调度系统。超级计算系统可利用 Slurm 进行资源和作业管理，以避免 相互干扰，提高运行效率。

### 三种基本模式

#### 批处理作业(采用 sbatch 命令提交，最常用方式)

- 使用 sbatch 命令提交作业脚本 
- 提交后立即返回该命令行终端，用户可进行其它操作 
- 在作业脚本中也可使用 srun 命令加载作业任务

#### 交互式作业提交(采用 srun 命令提交)

- 资源分配与任务加载两步均通过 srun 命令进行 
- 用户在该终端需等待任务结束才能继续其它操作
- 如果提交时的命令行终端断开，则任务终止

#### 实时分配模式作业(采用 salloc 命令提交)

- 分配作业模式类似于交互式作业模式和批处理作业模式的融合 
- 请求资源->队列等待->在提交节点执行指定命令->资源释放 
- 在作业结束前，如果提交时的命令行终端断开，则任务终止。

提交作业

### 使用 srun 命令提交作业

```shell
$ srun -n 4 ./hello 
Hello world from processor node1, rank 3 out of 4 processors 
Hello world from processor node1, rank 0 out of 4 processors 
Hello world from processor node1, rank 1 out of 4 processors 
Hello world from processor node1, rank 2 out of 4 processors
```

### 使用 sbatch 命令提交作业 

```shell
$ cat myscript.sh 
#!/bin/sh
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1 
#SBATCH -o job.hello%j.out 
srun -n 1 ./hello
$ sbatch myscript.sh 
Submitted batch job 375 
$ cat job.hello375.out 
Hello world from processor node1, rank 0 out of 1 processors
```



