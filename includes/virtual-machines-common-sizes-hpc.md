---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 16e2a9cfbd9f08428fddade290117b27bc3401f7
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2018
ms.locfileid: "44369320"
---
Azure H 系列虚拟机是最新的高性能计算 VM，旨在处理批处理、分析、分子建模和流体动力学等工作负载。 这些 8 vCPU 和 16 vCPU 的 VM 基于采用 DDR4 内存和基于 SSD 的临时存储的 Intel Haswell E5-2667 V3 处理器技术构建。 

除强大的 CPU 功能外，H 系列还提供支持使用 InfiniBand 的低延迟 RDMA 网络的各种选项，以及支持内存密集型计算要求的多项内存配置。



## <a name="h-series"></a>H 系列

ACU：290-300

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 磁盘最大吞吐量：IOPS | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> 对于 MPI 应用程序来说，专用 RDMA 后端网络是通过 FDR InfiniBand 网络启用的，后者可以提供相当低的延迟和高带宽。

<br>






