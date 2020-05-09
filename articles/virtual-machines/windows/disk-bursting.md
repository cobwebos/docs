---
title: 托管磁盘突发
description: 了解 azure 磁盘的磁盘突发和 Azure 虚拟机的磁盘突发
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594361"
---
# <a name="disk-bursting"></a>磁盘突发
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虚拟机级别突发
在公有云中的所有区域中已启用 VM 级别的突发支持： 
- [Lsv2 系列](../lsv2-series.md)

默认情况下，对支持的虚拟机启用了突发。

## <a name="disk-level-bursting"></a>磁盘级突发
在我们的[高级 ssd](disks-types.md#premium-ssd)上还提供了突发功能，适用于 P20 和更小的磁盘大小。 在支持磁盘突发的磁盘大小的新部署上，默认已启用磁盘突发。 支持磁盘突发的现有磁盘大小可以通过以下任一方法启用突发： 
- **重新启动 VM** 
- **分离并重新附加磁盘**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
