---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008315"
---
目前，只有超磁盘和高级 SSD 才能启用共享磁盘。 不同的磁盘大小可能具有不同的`maxShares`限制，在设置值时不能超过该`maxShares`限制。 对于高级 SSD，支持共享其磁盘的磁盘大小为 P15 和更高。

对于每个磁盘，可以定义一个`maxShares`值，该值表示可以同时共享磁盘的最大节点数。 例如，如果您计划设置 2 节点故障转移群集，则设置`maxShares=2`。 最大值是上限。 只要节点数低于指定`maxShares`值，节点就可以加入或离开群集（装载或卸载磁盘）。

> [!NOTE]
> 仅当`maxShares`磁盘与所有节点分离时，才能设置或编辑该值。

### <a name="premium-ssd-ranges"></a>高级 SSD 范围

下表说明了按高级磁盘大小允许的最大`maxShares`值：

|磁盘大小  |最大共享限制  |
|---------|---------|
|P15， P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁盘的 IOPS 和带宽限制不受`maxShares`该值的影响。 例如，P15 磁盘的最大 IOPS 是 1100，无论是最大共享 = 1 还是最大共享> 1。

### <a name="ultra-disk-ranges"></a>超磁盘范围

最小`maxShares`值为 1，最大值`maxShares`为 5。 超磁盘的大小限制，任何大小的超磁盘都可以使用任何值`maxShares`，最高包括最大值。