---
title: 扩展 HPC 应用程序 - Azure 虚拟机 |微软文档
description: 了解如何在 Azure VM 上扩展 HPC 应用程序。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707839"
---
# <a name="scaling-hpc-applications"></a>扩展 HPC 应用程序

Azure 上 HPC 应用程序的最佳扩展和横向扩展性能需要针对特定工作负荷进行性能调整和优化实验。 本节和 VM 系列特定页面提供了扩展应用程序的一般指南。

## <a name="compiling-applications"></a>编译应用程序

尽管不是必需的，但使用适当的优化标志编译应用程序可在 HB 和 HC 系列 VM 上提供最佳的放大性能。

### <a name="amd-optimizing-cc-compiler"></a>AMD 优化 C/C++ 编译器

AMD 优化 C/C++ 编译器 （AOCC） 编译器系统提供高水平的高级优化、多线程和处理器支持，包括全局优化、矢量化、过程间分析、环路转换和代码生成。 AOCC编译器二进制文件适用于具有GNU C库（glibc）版本2.17及以上版本的Linux系统。 编译器套件由 C/C++ 编译器 （clang）、Fortran 编译器 （FLANG） 和 Clang 前端（龙蛋）组成。

### <a name="clang"></a>铛

Clang 是处理预处理、分析、优化、代码生成、程序集和链接的 C、C++ 和 Objective-C 编译器。 Clang 支持`-march=znver1`该标志，以实现 AMD 基于 Zen 的 x86 架构的最佳代码生成和调优。

### <a name="flang"></a>法朗

FLANG 编译器是 AOCC 套件（2018 年 4 月添加）的最近添加，目前正在预发布，供开发人员下载和测试。 基于 Fortran 2008，AMD 扩展了 FLANG 的https://github.com/flangcompiler/flang)GitHub 版本（ 。 FLANG 编译器支持所有 CLANG 编译器选项和任意数量的特定于 FLANG 的编译器选项。

### <a name="dragonegg"></a>龙蛋

DragonEgg 是 gcc 插件，用 LLVM 项目中的优化器和代码生成器替换。 AOCC 附带的龙蛋与 gcc-4.8.x 配合使用，已针对 x86-32/x86-64 目标进行了测试，并已在各种 Linux 平台上成功使用。

GFortran 是 Fortran 程序的实际前端，负责预处理、分析和语义分析，生成 GCC GIMPLE 中间表示 （IR）。 龙蛋是一个GNU插件，插入GFortran编译流。 它实现了 GNU 插件 API。 使用插件体系结构，DragonEgg 成为编译器驱动程序，驱动编译的不同阶段。  遵循下载和安装说明后，可以使用以下语言调用龙蛋： 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 编译器
PGI社区版 17 已确认与 AMD EPYC 合作。 PGI 编译的 STREAM 版本确实可提供平台的完整内存带宽。 较新的社区版 18.10 （2018 年 11 月） 也应同样工作良好。 下面是使用英特尔编译器以最佳方式编译的 CLI 示例：

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>英特尔编译器
英特尔编译器 18 已确认与 AMD EPYC 合作。 下面是使用英特尔编译器以最佳方式编译器的 CLI 示例。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 编译器 
对于 HPC，AMD 建议 GCC 编译器 7.3 或更新。 不建议使用旧版本（如 RHEL/CentOS 7.4 中包含的 4.8.5）。 GCC 7.3 和更高版本将在 HPL、HPCG 和 DGEMM 测试中提供更高的性能。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>缩放应用程序 

以下建议适用于最佳应用程序扩展效率、性能和一致性：

* 使用顺序固定方法（而不是自动平衡方法）将进程固定到内核 0-59。 
* Numa/Core/HwThread 绑定优于默认绑定。
* 对于混合并行应用（OpenMP_MPI），每个 CCX 使用 4 个线程和 1 个 MPI 排名。
* 对于纯 MPI 应用，试验 1-4 MPI，每个 CCX 排名最佳性能。
* 某些对内存带宽具有极高敏感性的应用程序可能受益于每个 CCX 使用数量的内核。 对于这些应用程序，每个 CCX 使用 3 或 2 个内核可能会减少内存带宽争用，并产生更高的实际性能或更一致的可扩展性。 特别是，MPI全减可能受益于这一点。
* 对于更大的比例运行，建议使用 UD 或混合 RC+UD 传输。 许多 MPI 库/运行时库在内部执行此操作（如 UCX 或 MVAPICH2）。 检查传输配置有无大规模运行。

## <a name="next-steps"></a>后续步骤

在 Azure 上了解有关[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多详细信息。
