---
title: 托管磁盘突发
description: 了解 Azure 磁盘的磁盘突发，以及 Azure 虚拟机的磁盘突发
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: baabad550f5e6b0ae39936fc182e6d9789d189d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650941"
---
# <a name="disk-bursting"></a>磁盘突发
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虚拟机级突发
在公有云中的所有区域中，已在以下受支持大小的 VM 上启用了 VM 级突发支持： 
- [Lsv2 系列](../lsv2-series.md)

默认情况下会为支持突发的虚拟机启用突发。

## <a name="disk-level-bursting"></a>磁盘级突发
在 Azure 公有云、政府云和中国云的所有区域中，还为磁盘大小为 P20 和更小的[高级 SSD](disks-types.md#premium-ssd) 提供了突发功能。 默认情况下会在支持突发的磁盘大小的新部署上启用磁盘突发。 支持磁盘突发的现有磁盘大小可以通过以下任一方法启用突发： 
- **重启 VM** 
- **分离再重新附加磁盘**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
