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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008315"
---
目前，只有 ultra 磁盘和高级 Ssd 可以启用共享磁盘。 不同的磁盘大小可能具有不同`maxShares`的限制，设置`maxShares`值时不能超过此限制。 对于高级 Ssd，支持共享磁盘的磁盘大小为 P15 和更高版本。

对于每个磁盘，你可以定义`maxShares`一个值，该值表示可同时共享磁盘的最大节点数。 例如，如果计划设置2节点故障转移群集，则需要设置`maxShares=2`。 最大值是上限。 只要节点数低于指定`maxShares`值，节点就可以加入或离开群集（装载或卸载磁盘）。

> [!NOTE]
> 仅`maxShares`当磁盘从所有节点分离时，才能设置或编辑该值。

### <a name="premium-ssd-ranges"></a>高级 SSD 范围

下表说明了高级磁盘大小允许的`maxShares`最大值：

|磁盘大小  |maxShares 限制  |
|---------|---------|
|P15、P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁盘的 IOPS 和带宽限制不受`maxShares`值的影响。 例如，P15 磁盘的最大 IOPS 为1100，无论 maxShares = 1 还是 maxShares > 1。

### <a name="ultra-disk-ranges"></a>超磁盘范围

最小`maxShares`值为1，最大`maxShares`值为5。 超磁盘没有大小限制，任何大小的超磁盘都可以使用的任何值， `maxShares`最大值为，包括最大值。