---
title: 规划 Azure Stack 的存储容量 |Microsoft Docs
description: 了解如何规划 Azure Stack 部署的存储容量。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: 9ea46860817d60c2ffbde68c0fc5ae6f6ca14877
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368974"
---
# <a name="azure-stack-storage-capacity-planning"></a>Azure Stack 存储容量规划
以下部分提供 Azure Stack 的存储容量规划信息以帮助规划存储需求的解决方案。

## <a name="uses-and-organization-of-storage-capacity"></a>使用和组织使用的存储容量
Azure Stack 的超聚合配置允许的物理存储设备共享。 基础结构、 临时存储的租户虚拟机和存储区支持 blob、 表和队列的 Azure 一致的存储 (ACS) 服务之间的可用存储三个主要部分。

## <a name="spaces-direct-cache-and-capacity-tiers"></a>空间直通缓存和容量层级
没有用于操作系统、 本地日志记录、 转储和其他临时基础结构存储需求存储容量。 此本地存储容量是分开进行管理的存储空间直通配置的存储设备 （设备和容量）。 存储设备的其余部分被放置在单个池而不考虑缩放单位中的服务器数量的存储容量。 这些设备有两种类型： 缓存和容量。  缓存设备都只是该 – 缓存。 空间直通将使用这些设备写回和读取缓存。 这些缓存设备的容量使用，而不是致力于到带格式的虚拟磁盘的带格式的、"可见"容量。 容量设备用于此目的，并提供"家庭"的位置管理存储空间的数据。

所有存储容量分配和管理直接通过 Azure Stack 基础结构。 运算符确实需要做出选择的配置，分配，或容量扩展时处理选项。 这些设计决策进行以符合解决方案要求，将自动进行在任一初始安装/部署期间或在扩展容量。 作为设计的一部分考虑过有关复原、 保留的容量以重新生成，以及其他详细信息的详细信息。 

运算符可以选择所有 flash 或混合存储配置：

![Azure 存储容量规划](media/azure-stack-capacity-planning/storage.png)

在所有闪存配置中，缓存是使用容量的所选的 SATA SSD 或 NVMe NVMe。 在混合配置中，缓存是 NVMe 和 SATA SSD 之间进行选择时的容量是 HDD。

存储空间直通和 Azure Stack 存储配置的简短摘要如下所示：
- 一个存储空间池每个缩放单位 （所有存储设备都配置单个池内）
- 虚拟磁盘创建为最佳性能并提高恢复能力的三个副本镜像
- 每个虚拟磁盘格式化为 ReFS 文件系统
- 虚拟磁盘容量计算，并在一种方式将保留一个容量设备的数量的未分配的池中的数据容量中分配。 这是每个服务器的一个容量驱动器的等效项。
- 每个 ReFS 文件系统会为静态数据加密启用 BitLocker。 

-创建的虚拟磁盘自动及其容量如下所示：




|名称|容量计算|说明|
|-----|-----|-----|
|本地/启动设备|最小为 340 GB<sup>1</sup>|适用于操作系统映像和"本地"基础结构 Vm 的单个服务器存储|
|基础结构|3.5 TB|所有 Azure Stack 基础结构使用情况|
|VmTemp|如下所示<sup>2</sup>|租户虚拟机已连接的临时磁盘和该数据存储在这些虚拟磁盘|
|ACS|如下所示<sup>3</sup>|有关 blob、 表和队列服务的 azure 一致的存储容量|

<sup>1</sup>的 Azure Stack 解决方案合作伙伴所需的最小存储容量。

<sup>2</sup>用于租户虚拟机的临时磁盘的虚拟磁盘大小计算为服务器的物理内存的比率。 如下面的 Azure IaaS 虚拟机大小的表中所述，临时磁盘将分配给虚拟机的物理内存的比率。 完成 Azure Stack 中的"临时磁盘"存储的分配将在捕获大多数使用情况下完成，但可能无法满足所有临时磁盘存储需求。 选择为提供临时存储时不会占用大部分仅临时磁盘容量解决方案的存储容量的利弊。 针对每个缩放单位中的服务器创建一个临时存储磁盘。 临时存储的容量不会增长到 10%的缩放单位的存储池中的总体的可用存储容量超过。 计算公式是类似于下面的示例：

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> -创建的虚拟磁盘使用 ACS 是简单部门的剩余容量。 如前所述，所有虚拟磁盘是三向镜像和未分配的容量来用于每个服务器的一个容量驱动器的价值。 各种虚拟磁盘枚举上面第一次分配和剩余容量然后用于 ACS 虚拟磁盘。

## <a name="next-steps"></a>后续步骤
[了解 Azure Stack 容量规划电子表格](capacity-planning-spreadsheet.md)
