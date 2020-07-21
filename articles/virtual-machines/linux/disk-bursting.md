---
title: 托管磁盘突发
description: 了解 Azure 磁盘的磁盘突发和 Azure 虚拟机的磁盘突发
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4408b7d442ce3f4fbb1ea61031295375ae036493
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494541"
---
# <a name="managed-disk-bursting"></a>托管磁盘突发
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虚拟机级突发
在公有云中的所有区域中，已在以下受支持大小的 VM 上启用了 VM 级突发支持： 
- [Lsv2 系列](../lsv2-series.md)

默认情况下会为支持突发的虚拟机启用突发。

## <a name="disk-level-bursting"></a>磁盘级突发
在 Azure 公有云、政府云和中国云的所有区域中，还为磁盘大小为 P20 和更小的[高级 SSD](disks-types.md#premium-ssd) 提供了突发功能。 在支持磁盘突发的磁盘大小的所有新的和现有的部署上，默认已启用磁盘突发。 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
