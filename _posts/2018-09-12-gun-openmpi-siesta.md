---
layout: post
title:  "gcc Openmpi 编译siesta "
date:   2018-09-12 19:14:00 +0800
categories: DFT
tags: siesta gnu
author: cndaqiang
mathjax: true
---
* content
{:toc}

编译方法主要参考Peiwei师兄的组会ppt<br>
环境centos7，软件版本gcc-4.8.4,openmpi-1.10.3,scalapack-2,siesta-4.1-b1<br>
分别编译上述软件





此文直接将在我计算机上的编译过程输入的命令复制了过来，请适当更改
## 下载
```
cd /home/cndaqiang/soft/package
wget https://mirrors.tuna.tsinghua.edu.cn/gnu/gcc/gcc-4.8.4/gcc-4.8.4.tar.gz
wget https://download.open-mpi.org/release/open-mpi/v1.10/openmpi-1.10.3.tar.gz
wget http://www.netlib.org/scalapack/scalapack_installer.tgz
wget https://launchpad.net/siesta/4.1/4.1-b1/+download/siesta-4.1-b1.tar.gz
```

## 编译gcc
编译
```
tar xzvf gcc-4.8.4.tar.gz 
cd gcc-4.8.4/
#下载编译所需的依赖包
./contrib/download_prerequisites
#建立编译目录
mkdir build-gcc4.8.4
cd build-gcc4.8.4/
#检测编译环境，生成Makefile
../configure --disable-multilib --prefix=/home/cndaqiang/soft/gcc-4.8.4
#编译，此处使用40个进程进行编译
make -j40
#安装
make install
```
添加PATH路径，库路径
```
export PATH=/home/cndaqiang/soft/gcc-4.8.4/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/gcc-4.8.4/lib64:$LD_LIBRARY_PATH
which gcc
```
**注意：要将新编译gcc的路径写在$PATH前面，否则，gcc为/usr/bin/gcc,仍为系统自带的版本**

## 编译openmpi
```
cd ..
tar xzvf openmpi-1.10.3.tar.gz 
cd openmpi-1.10.3/
./configure --prefix=/home/cndaqiang/soft/openmpi-1.10.3_gcc-4.8.4 CC=gcc FC=gfortran CXX=g++
make -j40
make install
export PATH=/home/cndaqiang/soft/openmpi-1.10.3_gcc-4.8.4/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/openmpi-1.10.3_gcc-4.8.4/lib:$LD_LIBRARY_PATH
```

## 编译scalapack 
```
cd ..
tar xzvf scalapack_installer.tgz 
cd scalapack_installer/
./setup.py --prefix=/home/cndaqiang/soft/scalapack2_openmpi-1.10.3_gcc-4.8.4 --downall
```

最后可能会卡在这，终止就可以了
```
========================================
ScaLAPACK installer is starting now. Buckle up!
========================================
Downloading ScaLAPACK... done
Installing  scalapack-2.0.2 ...
Writing SLmake.inc... done.
Compiling BLACS, PBLAS and ScaLAPACK... done
Getting ScaLAPACK version number... 2.0.1
Installation of ScaLAPACK successful.
(log is in  /home/cndaqiang/soft/package/scalapack_installer_gcc8_openmpi_2/build/log/scalog )
Compiling test routines... done
Running BLACS test routines...
```

## 编译siesta
```
cd ..
ls
tar xzvf siesta-4.1-b1.tar.gz 
cd siesta-4.1-b1/
mkdir build_openmpi-1.10.3_gcc-4.4.8
cd build_openmpi-1.10.3_gcc-4.4.8/
../Src/obj_setup.sh
../Src/configure --enable-mpi
```
修改arch.make
```
FC=mpif90
```
和
```
BLAS_LIBS=/home/cndaqiang/soft/scalapack2_openmpi-1.10.3_gcc-4.8.4/lib/librefblas.a
LAPACK_LIBS=/home/cndaqiang/soft/scalapack2_openmpi-1.10.3_gcc-4.8.4/lib/libreflapack.a
BLACS_LIBS=
SCALAPACK_LIBS=/home/cndaqiang/soft/scalapack2_openmpi-1.10.3_gcc-4.8.4/lib/libscalapack.a
```
编译
``` 
make -j40
```
编译完之后，会在当前目录生成siesta可执行文件


## 后记
如有需要，可将以下命令添加`.bashrc`,或者在计算前先输入下列命令即可
```
export PATH=/home/cndaqiang/soft/gcc-4.8.4/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/gcc-4.8.4/lib64:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/openmpi-1.10.3_gcc-4.8.4/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/openmpi-1.10.3_gcc-4.8.4/lib:$LD_LIBRARY_PATH
```
