---
title: Windows 上的 VM 磁盘的可伸缩性和性能目标
description: 了解附加到运行 Windows 的 VM 的虚拟机磁盘的可伸缩性和性能目标。
author: roygara
ms.author: rogarana
ms.date: 11/15/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: bf56f1fac1f12f9e4994221d672410715393ae9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88815132"
---
# <a name="scalability-and-performance-targets-for-vm-disks-on-windows"></a>Windows 上的 VM 磁盘的可伸缩性和性能目标

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

请参阅 [Windows VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)了解其他详细信息。

## <a name="managed-virtual-machine-disks"></a>托管虚拟机磁盘

用星号表示的大小当前处于预览阶段。 请参阅我们的[常见问题解答](../faq-for-disks.md#new-disk-sizes-managed-and-unmanaged)，了解它们可用的区域。

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>非托管虚拟机磁盘
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>另请参阅

[Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)