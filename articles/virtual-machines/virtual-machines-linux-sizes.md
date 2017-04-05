---
title: "Azure 中的 Linux VM 大小 | Microsoft Docs"
description: "列出 Azure 中 Linux 虚拟机的不同可用大小。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 446f4257979e6725186b32ad65cef31cdd3e7ede
ms.lasthandoff: 03/24/2017

---

# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Azure 中 Linux 虚拟机的大小
本文介绍可用于运行 Linux 应用和工作负荷的 Azure 虚拟机的可用大小与选项。 此外，还提供了你在计划使用这些资源时要考虑的部署注意事项。 本文也适用于 [Windows 虚拟机](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

> [!IMPORTANT]
> * 有关不同大小的定价信息，请参阅[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)。 
> * 有关各 Azure 区域中推出的 VM 大小，请查看[可用产品(按区域)](https://azure.microsoft.com/regions/services/)。
> * 若要查看 Azure VM 的一般限制，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。
> * 了解有关 [Azure 计算单元 (ACU)](virtual-machines-linux-acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
> 
> 

<br>   


| 类型                     | 大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [常规用途](virtual-machines-linux-sizes-general.md)          | DSv2, Dv2, DS, D, Av2, A0-7,  | CPU 与内存之比平衡。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 |
| [计算优化](virtual-machines-linux-sizes-compute.md)        | Fs, F             | 高 CPU 与内存之比。 适用于中等流量的 Web 服务器、网络设备、批处理和应用程序服务器。        |
| [内存优化](virtual-machines-linux-sizes-memory.md)         | GS, G, DSv2, DS   | 高内存与内核之比。 适用于关系数据库服务器、中到大型规模的缓存和内存中分析。                 |
| [存储优化](virtual-machines-linux-sizes-storage.md)        | LS                | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| [GPU](virtual-machines-linux-sizes-gpu.md)            | NV, NC            | 专门针对大量图形绘制和视频编辑的虚拟机。 可选择单个或多个 GPU。       |
| [高性能计算](virtual-machines-linux-sizes-hpc.md) | H, A8-11          | 速度最快、功能最强大的 CPU 虚拟机具有可选的高吞吐量网络接口 (RDMA)。 

<br>

了解有关 [Azure 计算单元 (ACU)](virtual-machines-linux-acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

了解关于可用的各种 VM 大小的详细信息：
- [常规用途](virtual-machines-linux-sizes-general.md)
- [计算优化](virtual-machines-linux-sizes-compute.md)
- [内存优化](virtual-machines-linux-sizes-memory.md)
- [存储优化](virtual-machines-linux-sizes-storage.md)
- [GPU](virtual-machines-linux-sizes-gpu.md)
- [高性能计算](virtual-machines-linux-sizes-hpc.md)




