---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/26/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: edb5836ace6f7a1a0cff703b9921f6e6999de578
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944495"
---
启用共享磁盘仅适用于磁盘类型的子集。 目前只有超磁盘和高级 Ssd 可以启用共享磁盘。 已启用共享磁盘的每个托管磁盘受到下列限制，按磁盘类型进行组织：

### <a name="ultra-disks"></a>超级磁盘

超磁盘有各自独立的限制列表，与共享磁盘无关。 有关 ultra 磁盘限制，请参阅 [使用 Azure 超磁盘](../articles/virtual-machines/disks-enable-ultra-ssd.md)。

共享超磁盘时，它们具有以下附加限制：

- 目前仅限于 Azure 资源管理器或 SDK 支持。 
- 在某些版本的 Windows Server 故障转移群集中只能使用基本磁盘，有关详细信息，请参阅 [故障转移群集硬件要求和存储选项](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。

共享的 ultra 磁盘在默认情况下支持的所有区域均提供，无需注册访问权限即可使用这些磁盘。

### <a name="premium-ssds"></a>高级 SSD

- 目前仅在 [部分区域](#regional-availability)中受支持。
- 目前仅限于 Azure 资源管理器或 SDK 支持。 
- 只能对数据磁盘而不是 OS 磁盘启用。
- **ReadOnly** 主机缓存不可用于的高级 ssd `maxShares>1` 。
- 磁盘突发不适用于的高级 Ssd `maxShares>1` 。
- 使用 Azure 共享磁盘的可用性集和虚拟机规模集时，不会为共享数据磁盘强制实施与虚拟机容错域的 [存储容错域对齐](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) 。
- 使用 [邻近组 (PPG) ](../articles/virtual-machines/windows/proximity-placement-groups.md)时，共享磁盘的所有虚拟机都必须属于同一个 PPG。
- 在某些版本的 Windows Server 故障转移群集中只能使用基本磁盘，有关详细信息，请参阅 [故障转移群集硬件要求和存储选项](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- Azure 备份和 Azure Site Recovery 支持尚不可用。

#### <a name="regional-availability"></a>区域可用性

仅在以下区域支持共享高级 Ssd：

- 美国东部
- 美国东部 2
- 美国西部
- 美国西部 2
- 美国中西部
- 美国中南部
- 美国中北部
- 美国中部
- 西欧
- 北欧
- 韩国中部
- 加拿大中部
- 加拿大东部
- 日本东部
- 日本西部
- US Gov 弗吉尼亚州
- US Gov 亚利桑那州

如果你对尝试使用共享高级 Ssd 感兴趣，请 [注册以获取访问权限](https://aka.ms/AzureSharedDiskGASignUp)。