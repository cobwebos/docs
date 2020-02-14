---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4c150d874d56e3de495b0682bee979d13304a01d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202211"
---
在预览中，启用了共享磁盘的托管磁盘受到以下限制：

- 目前仅适用于高级 Ssd。
- 目前仅在美国西部地区受支持。
- 共享磁盘的所有虚拟机都必须部署在相同的[邻近位置组](../articles/virtual-machines/windows/proximity-placement-groups.md)中。
- 只能对数据磁盘而不是 OS 磁盘启用。
- 在某些版本的 Windows Server 故障转移群集中只能使用基本磁盘，有关详细信息，请参阅[故障转移群集硬件要求和存储选项](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- ReadOnly 主机缓存不适用于 `maxShares>1`的高级 Ssd。
- 只能将可用性集和虚拟机规模集用于 `FaultDomainCount` 设置为1。
- Azure 备份和 Azure Site Recovery 支持尚不可用。

如果你对尝试共享磁盘感兴趣，请[注册预览](https://aka.ms/shareddisksignup)。
