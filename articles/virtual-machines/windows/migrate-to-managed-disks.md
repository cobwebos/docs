---
title: "将 Azure VM 迁移到托管磁盘 | Microsoft Docs"
description: "迁移在存储帐户中使用非托管磁盘创建的 Azure 虚拟机，以使用托管磁盘。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: 3471792bba92879e446837434cbdba667cf679ad
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2018
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>将 Azure VM 迁移到 Azure 中的托管磁盘

Azure 托管磁盘无需单独管理存储帐户，从而简化存储管理。  还可以将现有的 Azure VM 迁移到托管磁盘，以利用可用性集中更好的 VM 可靠性。 这可确保一个可用性集中不同 VM 的磁盘可充分地彼此隔离，避免出现单一故障点。 这会自动将可用性集中不同 VM 的磁盘置于不同的存储缩放单位（戳），限制由于硬件和软件故障引起的单个存储缩放单位故障影响。
可根据需要，从两种类型的存储选项中进行选择：

- [高级托管磁盘](premium-storage.md)是基于固态驱动器 (SSD) 的存储媒体，可为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 可以通过迁移到高级托管磁盘，充分利用这些磁盘的速度和性能。

- [标准托管磁盘](standard-storage.md)使用基于硬盘驱动器 (HDD) 的存储媒体，且最适合用于对性能变化不太敏感的开发/测试和其他不频繁的访问工作负荷。

可在以下方案中迁移到托管磁盘：

| 迁移...                                            | 文档链接                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 将独立的 VM 和可用性集中的 VM 转换为托管磁盘   | [转换 VM 以使用托管磁盘](convert-unmanaged-to-managed-disks.md) |
| 单个 VM 从经典迁移到托管磁盘上的 Resource Manager     | [迁移单个 VM](migrate-single-classic-to-resource-manager.md)  | 
| vNet 中的所有 VM 从经典迁移到托管磁盘上的 Resource Manager     | [将 IaaS 资源从经典迁移到 Resource Manager](migration-classic-resource-manager-ps.md)，然后[将 VM 从非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>计划到托管磁盘的转换

本部分有助于在 VM 和磁盘类型方面做出最佳决策。


## <a name="location"></a>Location

选择 Azure 托管磁盘的可用位置。 如果要迁移到高级托管磁盘，还请确保高级存储在计划迁移到的目标区域中可用。 有关可用位置的最新信息，请参阅 [Azure 服务（按区域）](https://azure.microsoft.com/regions/#services)。

## <a name="vm-sizes"></a>VM 大小

如果要迁移到高级托管磁盘，必须将 VM 的大小更新为该 VM 所在区域中提供的支持高级存储的大小。 查看支持高级存储的 VM 大小。 [虚拟机大小](sizes.md)中列出了 Azure VM 大小规范。
查看适用于高级存储的虚拟机的性能特征并选择最适合工作负荷的 VM 大小。 确保 VM 上有足够的带宽来驱动磁盘通信。

## <a name="disk-sizes"></a>磁盘大小

**高级托管磁盘**

有七种类型的高级托管磁盘可用于 VM，每种磁盘都具有特定的 IOPS 和吞吐量限制。 根据应用程序在容量、性能、可伸缩性和峰值负载方面的需要为 VM 选择高级磁盘类型时，需要考虑这些限制。

| 高级磁盘类型  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| 磁盘大小           | 128 GB| 512 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| 每个磁盘的 IOPS       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| 每个磁盘的吞吐量 | 每秒 25 MB  | 每秒 50 MB  | 每秒 100 MB | 每秒 125 MB |每秒 150 MB | 每秒 200 MB | 每秒 250 MB | 每秒 250 MB |

**标准托管磁盘**

有七种类型的标准托管磁盘可用于 VM。 每种类型的磁盘具有不同的容量，但具有相同的 IOPS 和吞吐量限制。 根据应用程序的容量需求选择标准托管磁盘的类型。

| 标准磁盘类型  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| 磁盘大小           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| 每个磁盘的 IOPS       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| 每个磁盘的吞吐量 | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB |每秒 60 MB | 每秒 60 MB | 每秒 60 MB | 每秒 60 MB | 

## <a name="disk-caching-policy"></a>磁盘缓存策略

**高级托管磁盘**

默认情况下，所有高级数据磁盘的磁盘缓存策略都是“只读”，所有附加到 VM 的高级操作系统都是“读写”。 为使应用程序的 IO 达到最佳性能，建议使用此配置设置。 对于频繁写入或只写的磁盘（例如 SQL Server 日志文件），禁用磁盘缓存可获得更佳的应用程序性能。

## <a name="pricing"></a>定价

查看[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。 高级托管磁盘的定价与高级非托管磁盘相同。 但标准托管磁盘的定价与标准非托管磁盘不同。



## <a name="next-steps"></a>后续步骤

- 详细了解[托管磁盘](managed-disks-overview.md)
