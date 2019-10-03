---
title: 缩放 HPC 应用程序的 Azure 虚拟机 |Microsoft Docs
description: 了解如何缩放 Azure Vm 上的 HPC 应用程序。
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707839"
---
# <a name="scaling-hpc-applications"></a>缩放 HPC 应用程序

在 Azure 上的 HPC 应用程序的最佳纵向扩展和向外缩放性能，需要性能优化和优化特定工作负荷的试验。 本部分和 VM 特定于序列的页提供了有关缩放你的应用程序的一般指南。

## <a name="compiling-applications"></a>编译应用程序

尽管并非必需，编译应用程序与相应的优化标志 HB 和 HC 系列 Vm 上提供最佳的不断增长的性能。

### <a name="amd-optimizing-cc-compiler"></a>AMD 优化 C /C++编译器

AMD 优化 C /C++编译器 (AOCC) 编译器 system 提供了高级别的高级的优化，多线程和处理器支持，包括全局优化、 向量化、 过程间分析，循环转换和代码生成。 AOCC 编译器二进制文件都适合具有 GNU C 库 (glibc) 版本 2.17 Linux 系统及更高版本。 编译器套件包括 C /C++编译器 (clang)、 Fortran 编译器 (FLANG) 和 Fortran 前端到 Clang （龙鸡蛋）。

### <a name="clang"></a>Clang

Clang 是 C， C++，和 Objective C 编译器处理预处理、 分析、 优化、 代码生成、 程序集和链接。 Clang 支持`-march=znver1`标志以启用最佳代码生成和优化 AMD 的处理基于 x86 的体系结构。

### <a name="flang"></a>FLANG

FLANG 编译器是最近添加到 AOCC 套件 （已加入 2018 年 4 月），目前为开发人员若要下载和测试的预发布版。 基于 Fortran 2008，AMD 扩展 FLANG 的 GitHub 版本 (https://github.com/flangcompiler/flang) 。 FLANG 编译器支持所有 Clang 编译器选项和补充数量的特定于 FLANG 的编译器选项。

### <a name="dragonegg"></a>DragonEgg

DragonEgg 是 LLVM 项目中的这些替换 gcc 高级版的优化器和代码生成器的 gcc 插件。 DragonEgg AOCC 适用于 gcc 4.8.x，附带的 x86 32 x86 64 目标尚未测试和已成功使用各种 Linux 平台上。

GFortran 是 Fortran 程序负责处理、 分析，并生成 GCC GIMPLE 中间表示形式 (IR) 的语义分析的实际前端。 DragonEgg 是 GNU 插件，插入 GFortran 编译流。 它实现 GNU 插件 API。 使用插件体系结构，DragonEgg 成为编译器驱动程序，推动编译的不同阶段。  按照下载和安装说明之后, 以龙鸡蛋都可以使用调用： 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 编译器
PGI 社区版 ver. 17 确认能够使用 AMD EPYC。 流的 PGI 编译版本确实提供了平台的完整的内存带宽。 较新的社区版 18.10 (2018 年 11 月) 应同样能正常工作。 下面是以最佳方式使用 Intel 编译器的编译器的示例 CLI:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel 编译器
Intel 编译器 ver. 18 确认能够使用 AMD EPYC。 下面是示例 CLI 以最佳方式使用 Intel 编译器的编译器。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 编译器 
针对 HPC，AMD 建议 GCC 编译器 7.3 版或更高版本。 不建议使用较旧版本，如 4.8.5 附带 RHEL/CentOS 7.4。 GCC 7.3，和更高版本，可提供明显更高的性能上 HPL、 HPCG 和 DGEMM 测试。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>缩放应用程序 

以下建议适用于缩放效率、 性能和一致性的最佳应用程序：

* Pin 进程扩展到内核 0-59 使用顺序固定方法 （与自动平衡方法相反）。 
* 绑定通过 Numa/核心/HwThread 优于默认绑定。
* 对于混合并行应用程序 (OpenMP + MPI)，使用 4 个线程和每个 CCX 1 MPI 级别。
* 对于纯 MPI 应用程序，实验 1-4 MPI 每 CCX 排名为获得最佳性能。
* 内存带宽非常谨慎一些应用程序可能会受益于使用数量较少的每个 CCX 核心数。 对于这些应用程序，使用 3 个或 2 个核心 / CCX 可能会减少内存带宽争用并产生更高的实际性能或更一致的可伸缩性。 具体而言，MPI Allreduce 可能受益于。
* 明显更大的规模运行，建议使用 UD 或混合 RC + UD 传输。 许多 MPI 库/运行时库在内部 （例如 UCX 或 MVAPICH2） 执行此操作。 检查你大规模运行的传输配置。

## <a name="next-steps"></a>后续步骤

详细了解如何[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 上。
