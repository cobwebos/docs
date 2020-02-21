---
title: M 系列-Azure 虚拟机
description: M 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/05/2019
ms.author: lahugh
ms.openlocfilehash: 49b12341e5ca119ee20c7e509d9bbef64d4d5b37
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493665"
---
# <a name="m-series"></a>M 系列

M 系列提供高 vCPU 计数（最高128个 vcpu）和大量内存（最大为 3.8 TiB）。 它也适用于极大型数据库或其他应用程序，这些应用程序可以受益于高 vCPU 计数和大量内存。 M 系列大小基于 Intel®强® CPU E7-8890 v3 @ 2.50 GHz

M 系列 VM 的 Intel®超线程技术功能

ACU：160-180

高级存储：支持

高级存储缓存：支持

写入加速器：[支持](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量： IOPS/MBps （GiB 中的缓存大小） | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_M8ms <sup>2</sup>       | 8   | 218.75 | 256   | 8  | 10000/100 （793）     | 5000/125   | 4/2000  |
| Standard_M16ms <sup>2</sup>      | 16  | 437.5  | 512   | 16 | 20000/200 （1587）    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 （3174）    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 （3174）    | 20000/500  | 8/8000  |
| Standard_M32ms <sup>2</sup>      | 32  | 875    | 1024  | 32 | 40000/400 （3174）    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2048  | 64 | 80000/800 （6348）    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2048  | 64 | 80000/800 （6348）    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>3</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 （6348）    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>      | 128 | 2048   | 4096  | 64 | 160000/1600 （12696） | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1、2、3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600 （12696） | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 （1228）    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1792   | 7168  | 64 | 80000/800 （1228）    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2048   | 14336 | 64 | 250000/1600 （2456）  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 （2456）  | 80000/2000 | 8/32000 |

<sup>1</sup>超过64的 vCPU 需要以下受支持的来宾操作系统之一： Windows Server 2016、UBUNTU 16.04 LTS、SLES 12 SP2 和 Red Hat Enterprise Linux、CentOS 7.3 或 Oracle Linux 7.3 和 .lis 4.2.1。

<sup>2</sup> 受约束的可用核心大小。

<sup>3</sup> 实例与专用于单个客户的硬件隔离。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。