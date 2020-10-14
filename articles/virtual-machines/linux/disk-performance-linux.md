---
title: 虚拟机和磁盘性能
description: 详细了解 Vm 及其附加的磁盘如何结合使用以提高性能
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 3b849640bc7adb38fe51c3c7a9dda2d1d14f35fa
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017244"
---
# <a name="virtual-machine-and-disk-performance"></a>虚拟机和磁盘性能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虚拟机未缓存 vs 缓存限制
启用了高级存储且启用了高级存储缓存的虚拟机具有两个不同的存储带宽限制。 接下来，让我们继续查看 Standard_D8s_v3 的虚拟机。 下面是有关 [Dsv3 系列](../dv3-dsv3-series.md) 和 Standard_D8s_v3 的文档：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

让我们在此 VM 和磁盘组合上运行基准测试，这将创建 IO 活动，并可以在 [此处](disks-benchmarks.md)了解有关如何在 Azure 上对存储 IO 进行基准测试的所有信息。 通过基准测试工具，可以看到 VM 和磁盘组合能够实现 22800 IOPS：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]