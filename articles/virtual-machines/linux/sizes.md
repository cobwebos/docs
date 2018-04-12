---
title: Azure 中的 Linux VM 大小 | Microsoft Docs
description: 列出 Azure 中 Linux 虚拟机的不同可用大小。
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jonbeck
ms.openlocfilehash: e615cf4760ef3c75bdb398744a1454cd4d5121a4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Azure 中 Linux 虚拟机的大小
本文介绍可用于运行 Linux 应用和工作负荷的 Azure 虚拟机的可用大小与选项。 此外，还提供了你在计划使用这些资源时要考虑的部署注意事项。 本文也适用于 [Windows 虚拟机](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


| Type                     | 大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [常规用途](sizes-general.md)          | B、Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0-7  | CPU 与内存之比平衡。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 |
| [计算优化](sizes-compute.md)        | Fsv2、Fs、F             | 高 CPU 与内存之比。 适用于中等流量的 Web 服务器、网络设备、批处理和应用程序服务器。        |
| [内存优化](sizes-memory.md)         | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | 高内存与 CPU 之比。 适用于关系数据库服务器、中到大型规模的缓存和内存中分析。                 |
| [存储优化](sizes-storage.md)        | LS                | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| [GPU](sizes-gpu.md)            | NV、NC、NCv2、NCv3、ND            | 针对大量图形绘制和视频编辑的专用虚拟机，以及带有深度学习功能的模型定型和推断 (ND)。 可选择单个或多个 GPU。       |
| [高性能计算](sizes-hpc.md) | H, A8-11          | 速度最快、功能最强大的 CPU 虚拟机具有可选的高吞吐量网络接口 (RDMA)。 

<br>

- 有关不同大小的定价信息，请参阅[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)。 
- 有关各 Azure 区域中推出的 VM 大小，请查看[可用产品(按区域)](https://azure.microsoft.com/regions/services/)。
- 若要查看 Azure VM 的一般限制，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-subscription-service-limits.md)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。


## <a name="rest-api"></a>REST API

若要了解如何使用 REST API 查询 VM 大小，请参阅以下文章：

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)（列出可用虚拟机大小以重设大小）
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)（列出某个订阅的可用虚拟机大小）
- [List available virtual machine sizes in an availability set](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)（列出可用性集中的可用虚拟机大小）

## <a name="acu"></a>ACU

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

## <a name="next-steps"></a>后续步骤

了解关于可用的各种 VM 大小的详细信息：
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)



