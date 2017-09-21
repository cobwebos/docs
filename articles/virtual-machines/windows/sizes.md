---
title: "Azure 中的 Windows VM 大小 | Microsoft Docs"
description: "列出 Azure 中 Windows 虚拟机的不同可用大小。"
services: virtual-machines-windows
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/07/2017
ms.author: jonbeck
ms.translationtype: HT
ms.sourcegitcommit: f2ac16c2f514aaa7e3f90fdf0d0b6d2912ef8485
ms.openlocfilehash: 0a02e65620d864a5bfa6926c598400a3223a69df
ms.contentlocale: zh-cn
ms.lasthandoff: 09/08/2017

---

# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Azure 中 Windows 虚拟机的大小

本文介绍可用于运行 Windows 应用和工作负荷的 Azure 虚拟机的可用大小和选项。 此外，还提供了在计划使用这些资源时要考虑的部署注意事项。  本文也适用于 [Linux 虚拟机](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


| 类型                     | 大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [常规用途](sizes-general.md)          | B（预览）、Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0-7 | CPU 与内存之比平衡。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 |
| [计算优化](sizes-compute.md)        | Fs, F             | 高 CPU 与内存之比。 适用于中等流量的 Web 服务器、网络设备、批处理和应用程序服务器。        |
| [内存优化](../virtual-machines-windows-sizes-memory.md)         | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | 高内存与 CPU 之比。 适用于关系数据库服务器、中到大型规模的缓存和内存中分析。                 |
| [存储优化](../virtual-machines-windows-sizes-storage.md)        | LS                | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| [GPU](sizes-gpu.md)            | NV, NC            | 专门针对大量图形绘制和视频编辑的虚拟机。 可选择单个或多个 GPU。       |
| [高性能计算](sizes-hpc.md) | H, A8-11          | 速度最快、功能最强大的 CPU 虚拟机具有可选的高吞吐量网络接口 (RDMA)。 

<br> 

- 有关不同大小的定价信息，请参阅[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)。 
- 若要查看 Azure VM 的一般限制，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-subscription-service-limits.md)。
- 存储成本根据存储帐户中的已使用页数进行单独计算。 有关详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。



## <a name="rest-api"></a>REST API

若要了解如何使用 REST API 查询 VM 大小，请参阅以下文章：

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)（列出可用虚拟机大小以重设大小）
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)（列出某个订阅的可用虚拟机大小）
- [List available virtual machine sizes in an availability set](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)（列出可用性集中的可用虚拟机大小）

## <a name="acu"></a>ACU

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

## <a name="next-steps"></a>后续步骤

了解关于可用的各种 VM 大小的详细信息：
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](../virtual-machines-windows-sizes-memory.md)
- [存储优化](../virtual-machines-windows-sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)




