---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471668"
---
在预览版中，已启用共享磁盘的托管磁盘受以下限制：

- 目前仅适用于高级 SSD。
- 目前仅在美国中西部地区得到支持。
- 共享磁盘的所有虚拟机都必须部署在同一[接近放置组中](../articles/virtual-machines/windows/proximity-placement-groups.md)。
- 只能在数据磁盘上启用，不能在 OS 磁盘上启用。
- 只有基本磁盘可用于某些版本的 Windows 服务器故障转移群集，有关详细信息，请参阅[故障转移群集硬件要求和存储选项](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- 只读主机缓存不适用于具有`maxShares>1`的高级 SSD。
- 可用性集和虚拟机规模集只能与设置为 1`FaultDomainCount`一起使用。
- Azure 备份和 Azure 站点恢复支持尚不可用。

如果您有兴趣尝试共享磁盘，请[注册我们的预览](https://aka.ms/AzureSharedDiskPreviewSignUp)版。
