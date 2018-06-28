---
title: Azure 中的 Windows VM 大小 | Microsoft Docs
description: 列出 Azure 中 Windows 虚拟机的不同可用大小。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/22/2018
ms.author: jonbeck
ms.openlocfilehash: d4e1b56ba695f3305d1be023c4352a3c4bcfe27d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "34658343"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Azure 中 Windows 虚拟机的大小

本文介绍可用于运行 Windows 应用和工作负荷的 Azure 虚拟机的可用大小和选项。 此外，还提供了你在计划使用这些资源时要考虑的部署注意事项。  本文也适用于 [Linux 虚拟机](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


| Type                     | 大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [常规用途](sizes-general.md)          | B、Dsv3、Dv3、DSv2、Dv2、Av2 | CPU 与内存之比平衡。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 |
| [计算优化](sizes-compute.md)        | Fsv2、Fs、F             | 高 CPU 与内存之比。 适用于中等流量的 Web 服务器、网络设备、批处理和应用程序服务器。        |
| [内存优化](../virtual-machines-windows-sizes-memory.md)         | Esv3、Ev3、M、GS、G、DSv2、Dv2  | 高内存与 CPU 之比。 适用于关系数据库服务器、中到大型规模的缓存和内存中分析。                 |
| [存储优化](../virtual-machines-windows-sizes-storage.md)        | LS                | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| [GPU](sizes-gpu.md)            | NV、NC、NCv2、NCv3、ND            | 针对大量图形绘制和视频编辑的专用虚拟机，以及带有深度学习功能的模型定型和推断 (ND)。 可选择单个或多个 GPU。       |
| [高性能计算](sizes-hpc.md) | H       | 速度最快、功能最强大的 CPU 虚拟机具有可选的高吞吐量网络接口 (RDMA)。 


<br> 

- 有关不同大小的定价信息，请参阅[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)。 
- 若要查看 Azure VM 的一般限制，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-subscription-service-limits.md)。
- 存储成本根据存储帐户中的已使用页数进行单独计算。 有关详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。


## <a name="rest-api"></a>REST API

若要了解如何使用 REST API 查询 VM 大小，请参阅以下文章：

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)（列出可用虚拟机大小以重设大小）
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)（列出某个订阅的可用虚拟机大小）
- [List available virtual machine sizes in an availability set](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)（列出可用性集中的可用虚拟机大小）

## <a name="acu"></a>ACU

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

## <a name="benchmark-scores"></a>基准评分

使用 [CoreMark 基准测试分数](compute-benchmark-scores.md)，详细了解 Windows VM 的计算性能。

## <a name="next-steps"></a>后续步骤

了解关于可用的各种 VM 大小的详细信息：
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](../virtual-machines-windows-sizes-memory.md)
- [存储优化](../virtual-machines-windows-sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- 查看[上一代](sizes-previous-gen.md)页面，了解 A Standard、Dv1（D1-4 和 D11-14 v1）以及 A8-A11 系列




