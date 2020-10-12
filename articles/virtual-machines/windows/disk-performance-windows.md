---
title: 虚拟机和磁盘性能
description: 详细了解 Vm 及其附加的磁盘如何结合使用以提高性能
author: albecker1
ms.author: albecker
ms.date: 09/25/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 18c07fceb65623c286b31398314e2b6f124955a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539966"
---
# <a name="virtual-machine-and-disk-performance"></a>虚拟机和磁盘性能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虚拟机未缓存 vs 缓存限制
 启用了高级存储且启用了高级存储缓存的虚拟机具有两个不同的存储带宽限制。 接下来，让我们继续查看 Standard_D8s_v3 的虚拟机。 下面是有关 [Dsv3 系列](../dv3-dsv3-series.md) 和 Standard_D8s_v3 的文档：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
