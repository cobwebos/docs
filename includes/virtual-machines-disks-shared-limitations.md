---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ce964ac197fbff64bbb7cc36e8c2bf762f93663f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84337338"
---
在预览中，启用共享磁盘仅适用于磁盘类型的子集。 目前只有超磁盘和高级 Ssd 可以启用共享磁盘。 已启用共享磁盘的每个托管磁盘受到下列限制，按磁盘类型进行组织：

### <a name="ultra-disks"></a>超级磁盘

超磁盘有各自独立的限制列表，与共享磁盘无关。 有关 ultra 磁盘限制，请参阅[使用 Azure 超磁盘](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)。

共享超磁盘时，它们具有以下附加限制：

- 目前仅限于 Azure 资源管理器或 SDK 支持。
- 在某些版本的 Windows Server 故障转移群集中只能使用基本磁盘，有关详细信息，请参阅[故障转移群集硬件要求和存储选项](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。

### <a name="premium-ssds"></a>高级 SSD

- 目前仅在美国西部地区受支持。
- 共享磁盘的所有虚拟机都必须部署在相同的[邻近位置组](../articles/virtual-machines/windows/proximity-placement-groups.md)中。
- 只能对数据磁盘而不是 OS 磁盘启用。
- 在某些版本的 Windows Server 故障转移群集中只能使用基本磁盘，有关详细信息，请参阅[故障转移群集硬件要求和存储选项](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- ReadOnly 主机缓存不可用于的高级 Ssd `maxShares>1` 。
- 只能将可用性集和虚拟机规模集用于 `FaultDomainCount` 设置为1的。
- Azure 备份和 Azure Site Recovery 支持尚不可用。

如果你对尝试共享磁盘感兴趣，请[注册预览](https://aka.ms/AzureSharedDiskPreviewSignUp)。
