---
title: "CUDA_1"
slug: ""
date: 2022-11-21T13:57:14+08:00
summary: ""
author: ["Jian"]
cover:
    image: ""
    alt: ""
tags: []
katex: true
mermaid: false
draft: true
---

[1] https://blog.csdn.net/Augusdi/article/details/12187291


## CUDA编程

### 1.什么是CUDA

CUDA(Compute Unified Device Architecture)，统一计算架构，是NVidia推出的并行计算平台。NVidia官方对其的解释是：一个并行计算平台和简单（简洁）地使用图像处理单元（GPU）进行通用计算的编程模型。利用GPU的能力在计算性能上有惊人的提升。

简单地说CUDA是便于程序员利用NVidia GPU进行通用计算的开发环境及工具，目前支持C/C++语言，将来还会支持Fortran语言。

### 2.为什么要用到CUDA

CPU主频要比GPU高2-3倍左右，但是通常情况下GPU核心的数量要比CPU多2-3个数量级以上。因此GPU的计算能力要远大于CPU，充分发挥GPU的计算能力，可以有成倍的性能提升。   

早期利用GPU的计算能力是使用着色器和着色语言（GLSL等）。目前广泛使用的是CUDA和OpenCL。CUDA是针对NVidia GPU硬件设备设计的，而 OpenCL是针对跨平台设计的。因此CUDA可充分发挥NVidia GPU的计算性能。

CUDA可以直接使用C/C++语言来开发GPU程序，省去了程序员重新学一种新语言的麻烦。


### 3.CUDA环境搭建

CUDA环境主要分为四点：硬件（GPU设备）、操作系统、C/C++编译器和CUDA工具包。

硬件（GPU设备），必须是支持CUDA的GPU。可到NVidia官网查询支持CUDA的GPU设备，具体地址为：http://www.nvidia.com/object/cuda_home_new.html 。

操作系统，支持Microsoft Windows、Mac OS X和Linux。

C/C++编译器，对不同的操作系统有不同的要求。

CUDA工具包，NVidia提供了不同操作系统对应的CUDA Toolkit，可从https://developer.nvidia.com/cuda-downloads 下载对应的版本。

本文只以Microsoft Windows为例介绍如何搭建CUDA环境。

准备材料：

·一台装有支持CUDA GPU的电脑。

·Microsoft Windows操作系统（Microsoft Windows XP,Vista,7,or 8 or Windows Server 2003 or 2008）。

·CUDA工具包（相应操作系统）。下载地址：https://developer.nvidia.com/cuda-downloads

·C/C++编译器：Microsoft Visual Studio 2008 或 2010，或者对应版本的Microsoft Visual C++ Express产品。

安装步骤：

·在装有支持CUDA GPU的电脑上安装Microsoft Windows操作系统（一般情况下都已经完成这步骤）。

·安装C/C++编译器，可只安装其中的C++编译器部分。

·安装CUDA工具包。（CUDA工具包中有NVidia GPU的驱动程序，尚未安装的请选择安装。）

安装验证：

Windows XP系统：进入 C:\Documents and Settings\All Users\Application Data\NVIDIA Corporation\CUDA Samples\v5.0\bin\win32\Release 目录运行deviceQuery.exe文件。

Windows Vista, Windows 7, Windows 8, Windows Server 2003, and Windows Server 2008系统：进入 C:\ProgramData\NVIDIA Corporation\CUDA Samples\v5.0\bin\win32\Release 目录运行deviceQuery.exe文件。

如果安装正确，执行deviceQuery.exe文件会得到GPU设备的相应信息。如果没有安装支持CUDA的GPU也会得出GPU的信息，其中CUDA Capability Major/Minor version number信息为9999.9999。

Microsoft Windows上更详细的安装信息请查看：

http://docs.nvidia.com/cuda/cuda-getting-started-guide-for-microsoft-windows/index.html 。

       Mac OS X的安装：

http://docs.nvidia.com/cuda/cuda-getting-started-guide-for-mac-os-x/index.html 。

       Linux的安装：

http://docs.nvidia.com/cuda/cuda-getting-started-guide-for-linux/index.html 。


### 4.第一个CUDA程序

在Microsoft Windows系统上，如果成功搭建了CUDA环境，则在Microsoft Visual Studio中已经集成了CUDA的开发组件。

以下以Windows 7 + Microsoft Visual Studio 2008为例，创建第一个CUDA程序。

打开Microsoft Visual Studio 2008，依次：File->New->Project->NVIDIA->CUDA->CUDA 5.0 Runtime，输入相应的项目名称确定即可。

默认会生成一个kernel.cu文件，内容如下：

```c
#include "cuda_runtime.h"
 
#include "device_launch_parameters.h"
 
#include <stdio.h>
 
void addWithCuda(int *c, const int *a, const int *b, size_t size);
 
__global__ void addKernel(int *c, const int *a, const int *b)
 
{
 
    int i = threadIdx.x;
 
    c[i] = a[i] + b[i];
 
}
 
int main()
 
{
 
    const int arraySize = 5;
 
    const int a[arraySize] = { 1, 2, 3, 4, 5 };
 
    const int b[arraySize] = { 10, 20, 30, 40, 50 };
 
    int c[arraySize] = { 0 };
 
    // Add vectors in parallel.
 
    addWithCuda(c, a, b, arraySize);
 
    printf("{1,2,3,4,5} + {10,20,30,40,50} = {%d,%d,%d,%d,%d}\n",
 
        c[0], c[1], c[2], c[3], c[4]);
 
    // cudaThreadExit must be called before exiting in order for profiling and
 
    // tracing tools such as Nsight and Visual Profiler to show complete traces.
 
    cudaThreadExit();
 
    return 0;
 
}
 
// Helper function for using CUDA to add vectors in parallel.
 
void addWithCuda(int *c, const int *a, const int *b, size_t size)
 
{
 
    int *dev_a = 0;
 
    int *dev_b = 0;
 
    int *dev_c = 0;
 
    // Choose which GPU to run on, change this on a multi-GPU system.
 
    cudaSetDevice(0);
 
    // Allocate GPU buffers for three vectors (two input, one output)    .
 
    cudaMalloc((void**)&dev_c, size * sizeof(int));
 
    cudaMalloc((void**)&dev_a, size * sizeof(int));
 
    cudaMalloc((void**)&dev_b, size * sizeof(int));
 
    // Copy input vectors from host memory to GPU buffers.
 
    cudaMemcpy(dev_a, a, size * sizeof(int), cudaMemcpyHostToDevice);
 
    cudaMemcpy(dev_b, b, size * sizeof(int), cudaMemcpyHostToDevice);
 
    // Launch a kernel on the GPU with one thread for each element.
 
    addKernel<<<1, size>>>(dev_c, dev_a, dev_b);
 
    // cudaThreadSynchronize waits for the kernel to finish, and returns
 
    // any errors encountered during the launch.
 
    cudaThreadSynchronize();
 
    // Copy output vector from GPU buffer to host memory.
 
    cudaMemcpy(c, dev_c, size * sizeof(int), cudaMemcpyDeviceToHost);
 
    cudaFree(dev_c);
 
    cudaFree(dev_a);
 
    cudaFree(dev_b);
}
```

代码1

这是一个将两个一维数组相加的例子。

其中addKernel是内核函数，它的计算过程是在GPU上实现的，用函数类型限定符__global__限制，且函数类型为void型。

cuda_runtime.h头文件包括了运行时API和其参数的定义。（如果使用驱动API则使用cuda.h头文件）。

device_launch_parameters.h头文件包含了内核函数的5个变量threadIdx、blockDim、blockIdx、gridDim和wrapSize。

对其中CUDA运行时API函数的解释：

·cudaSetDevice()：选择设备（GPU）。（可以不使用，不使用的情况下，默认选择设备0）

·cudaMalloc()：动态分配显存。

·cudaMemcpy()：设备与主机之内的数据拷贝。

·cudaThreadSynchronize()：同步所有设备上的线程，等待所有线程结束。

·cudaFree():释放由cudaMalloc分配的显存。

·cudaThreadExit():结束CUDA上下文环境，释放其中的资源。

这些函数的具体介绍在 http://docs.nvidia.com/cuda/cuda-runtime-api/index.html 中。

### 5. CUDA编程

#### 5.1. 基本概念

CUDA编程中需要注意一些基本概念，分别为：主机、设备、运行时API、驱动API、warp、bank、函数类型限定符、变量类型限定符、thread、block、grid、计算能力、SIMT、内置变量、纹理、CUDA数组等。

主机(host)：可理解为CPU与内存的组合。

设备(device)：可理解为GPU与显存的组合。

运行时API：是指CUDA运行时API是在驱动API的基础上封装而成的，简化了CUDA的开发。

驱动API：是指CUDA驱动API，相比运行时API更接近于设备，可灵活运用设备的特性开发CUDA，可实现运行时API无法实现的功能。

warp：多处理器激活、管理、调度和执行并行任务的单位。计算能力2.x的设备warp为32个线程。未来的设备可能不同，可以通过内置变量warpSize查询。

bank：为了获得较高的存储器带宽，共享存储器被划分为多个大小相等的存储器模块，称为存储体，这些存储体就叫bank，可同步访问。

函数类型限定符：是CUDA C中特有的，用来修饰是主机函数，设备调用的设备函数，还是主机调用的设备函数。有__device__、__global__、__host__。

变量类型限定符：是用来修饰设备变量的。有__device__、__constant__、__shared__。

thread：设备中的线程，与主机中的线程是同一个概念。

block：线程块，由一组线程组成。一个线程块中的所以线程会在同一个多处理器上执行，一个多处理器上可同时执行多个线程块。

grid：有所有线程块组成的网格。

计算能力：是NVidia GPU不同架构的计算能力。

SIMT：单指令多线程，与单指令多数据（SIMD）类似。一条指令多个线程一同执行，实现程序的并行化。

内置变量：有threadIdx、blockDim、blockIdx、gridDim、warpSize。其中threadIdx指此线程在线程块中的位置；blockDim指线程块维度；blockIdx指该线程块在网格中的位置；gridDim指线程块网格维度；warpSize指一个warp多少个线程。

纹理：本文主要涉及到的是纹理参考、纹理绑定、纹理获取。

CUDA数组：区别于线性存储器，对数据进行了对齐等的处理，包括一维、二维和三维。其中的数据为：一元、二元或四元组。

#### 5.2. 线程层次结构

CUDA线程的层次结构，由小到大依次为线程(thread)、线程块(block)、线程块网格(grid)。一维、二维或三维的线程组组成一个线程块，一维、二维或三维的线程块组组成一个线程块网格。

下图是由二维的线程块组组成的线程块网络，其中线程块是由二维的线程组组成。



图1 NVidia GPU的硬件结构是，一组流处理器组成一个多处理器，一个或多个多处理器组成一个GPU。其中流处理器，可以理解为处理计算的核心单元。多处理器类似于多核CPU。NVidia GPU从DX10（DirectX10）开始出现了Tesla、Fermi、Kepler架构，不同的架构多处理器中流处理器数量都有差别。


### 5.3. 存储器层次结构

CUDA存储器有：寄存器、共享存储器、常量存储器、本地存储器、全局存储器、纹理存储器等。其中寄存器和本地存储器是线程私有的，共享存储器是对线程块中的所有线程可见，常量存储器、全局存储器和纹理存储器是对网格中所有线程可见。

下图解释了存储器的层次结构：

#### 5.4. 运行时API

运用运行时API开发CUDA程序需要了解：初始化、设备管理、存储器管理、流管理、事件管理、纹理参考管理、OpenGL互操作和Direct3D互操作。

运行时API文档地址为：http://docs.nvidia.com/cuda/cuda-runtime-api/index.html 。

##### 5.4.1. 初始化

运行时API不存在显示初始化函数，初始化会在首次调用运行时函数时完成。虽然不需要调用初始化函数进行初始化，但是退出时需要调用退出函数cudaThreadExit()释放资源。

 

##### 5.4.2. 设备管理

有些电脑上可能有多块设备，因此对于不同的要求选择合适的设备。设备管理主要是获取设备信息和选择执行设备。

主要有三个函数：

·cudaGetDeviceCount()：得到电脑上设备的个数。

·cudaGetDeviceProperties()：获得对应设备的信息。

·cudaSetDevice()：设置CUDA上下文对应的设备。

运行__global__函数前需要提前选择设备，如果不调用cudaSetDevice()函数，则默认使用0号设备。

上面三个函数的具体用法请查看CUDA运行时API文档。

##### 5.4.3. 存储器管理

共享存储器、常量存储器、线性存储器和CUDA数组的使用是存储器管理的主要部分。

###### 5.4.3.1 共享存储器

共享存储器，使用__shared__变量限定符修饰，可静态或动态分配共享存储器。

代码一：
  - 静态分配共享存储器，是在设备代码中直接分配共享存储器的大小，如下代码：

```c
#define SHARED_MEM 16
 
__global__ void kernel(…)
 
{
       __shared__ int shared[SHARED_MEM];
}
 
void main()
{
       kernel<<<nBlock, nThread>>>(…);
}
```

代码2
  - 动态分配共享存储器，是在主机代码中使用内核函数的第三个特定参数传入分配共享存储器的大小，如下代码：
    ```c
    #define SHARED_MEM 16
    
    __global__ void kernel(…)
    {
        extern __shared__ int shared[];
    }
    
    void main()
    {
        int nSharedMem = (int)SHARED_MEM;
        kernel<<<nBlock, nThread, nSharedMem*sizeof(int)>>>(…);
    }
    ```

###### 5.4.3.2. 常量存储器

常量存储器，使用__constant__变量限定符修饰。使用常量存储器，是由于其在设备上有片上缓存，比全局存储器读取效率高很多。

使用常量存储器时会涉及的运行时API函数主要有：

·cudaMemcpyToSymbol()

·cudaMemcpyFromSymbol()

·cudaGetSymbolAddress()

·cudaGetSymbolSize()

主机代码中使用cudaGetSymbolAddress()获取__constant__或__device__定义的变量地址。设备代码中可通过提取__device__、__shared__或__constant__变量的指针获取变量地址。


###### 5.4.3.3. 线性存储器

线性存储器是使用cudaMalloc()、cudaMallocPitch()或cudaMalloc3D()分配的，使用cudaFree()释放。二维的时候建议使用cudaMallocPitch()分配，cudaMallocPitch()函数对对齐进行了调整。这三个分配函数对应cudaMemset()、cudaMemset2D()、cudaMemset3D()三个memset函数和cudaMemcpy()、cudaMemcpy2D()、cudaMemcpy3D()三个memcpy函数。

###### 5.4.3.4. CUDA数组

CUDA数组是使用cudaMallocArray()、cudaMalloc3DArray()分配的，使用cudaFreeArray()释放。

相关memcpy函数请查阅CUDA运行时API文档。

具体使用可查阅CUDA编程指南：http://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html 。

##### 5.4.4. 流管理

主机设备之间的内存拷贝与内核在设备上执行是异步的。在不使用流的情况下，是这样执行的：设备先从主机上拷贝内存，拷贝完成之后，再在设备上执行内核代码计算，最后当内核执行完毕，再把设备上的内存拷贝到主机上。当使用两个流的情况下，0号流执行内核代码的同时1号流拷贝主机内存到设备，1号流执行的同时0号流拷贝设备内存到主机（具体的实现并不一定如此，这里是为了说明流的作用简单做了假设）。两个流的情况下，部分内存拷贝和内置执行是同时进行的（异步的），比同步的内存拷贝和内核执行节省了时间。

 与流有关的函数有：

    ·cudaStreamCreate()：流的创建；

    ·cudaStreamDestroy()：流的销毁；

    ·cudaStreamSynchronize()：流同步；

    ·*Async：与流相关的其他函数。

    内核<<<…>>>的第四个参数为哪个流。

    CUDA编程指南中有对流具体实现的讲解。