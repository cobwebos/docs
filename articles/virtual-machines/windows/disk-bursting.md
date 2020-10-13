---
title: 托管磁盘突发
description: 了解 Azure 磁盘的磁盘突发和 Azure 虚拟机的磁盘突发
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 547834542b605b226ebffd68e05296ee847dc6de
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974322"
---
# <a name="disk-bursting"></a>磁盘突发
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虚拟机级突发
在公有云中的所有区域中，已在以下受支持大小的 VM 上启用了 VM 级突发支持： 
- [Lsv2 系列](../lsv2-series.md)

对于以下受支持的大小，还可在美国西部提供 VM 级别的突发：
- [Dsv3 系列](../dv3-dsv3-series.md)
- [Esv3 系列](../ev3-esv3-series.md)

默认情况下会为支持突发的虚拟机启用突发。

## <a name="disk-level-bursting"></a>磁盘级别突发
对于所有区域中大小为 P20 和更小的磁盘，在我们的[高级 SSD](../disks-types.md#premium-ssd) 上也提供了突发功能。 在支持磁盘突发的磁盘大小的新部署上，默认已启用磁盘突发。 支持磁盘突发的现有磁盘大小可以通过以下任一方法启用突发： 
- **重启 VM** 
- **分离再重新附加磁盘**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]