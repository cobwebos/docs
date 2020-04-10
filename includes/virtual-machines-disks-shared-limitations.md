---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008318"
---
在预览版中，启用共享磁盘仅适用于磁盘类型的子集。 目前只有超磁盘和高级 SSD 才能启用共享磁盘。 每个已启用共享磁盘的托管磁盘都受以下限制的约束，按磁盘类型组织：

### <a name="ultra-disks"></a>超级磁盘

Ultra 磁盘有其各自的限制列表，与共享磁盘无关。 有关超磁盘限制，请参阅[使用 Azure 超级磁盘](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)。

共享超磁盘时，它们具有以下附加限制：

- 目前仅在美国西部支持。
- 当前仅限于 Azure 资源管理器或 SDK 支持。
- 只有基本磁盘可用于某些版本的 Windows 服务器故障转移群集，有关详细信息，请参阅[故障转移群集硬件要求和存储选项](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。

### <a name="premium-ssds"></a>高级 SSD

- 目前仅在美国中西部地区得到支持。
- 共享磁盘的所有虚拟机都必须部署在同一[接近放置组中](../articles/virtual-machines/windows/proximity-placement-groups.md)。
- 只能在数据磁盘上启用，不能在 OS 磁盘上启用。
- 只有基本磁盘可用于某些版本的 Windows 服务器故障转移群集，有关详细信息，请参阅[故障转移群集硬件要求和存储选项](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- 只读主机缓存不适用于具有`maxShares>1`的高级 SSD。
- 可用性集和虚拟机规模集只能与设置为 1`FaultDomainCount`一起使用。
- Azure 备份和 Azure 站点恢复支持尚不可用。

如果您有兴趣尝试共享磁盘，请[注册我们的预览](https://aka.ms/AzureSharedDiskPreviewSignUp)版。
