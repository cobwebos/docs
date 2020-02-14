---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9000cab026ec08bd78d8a8f334c8967a8a747c95
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202289"
---
目前，只有高级 Ssd 可以启用共享磁盘。 支持此功能的磁盘大小 P15 和更高版本。 不同的磁盘大小可能具有不同的 `maxShares` 限制，在设置 `maxShares` 值时不能超过此限制。

对于每个磁盘，你可以定义一个 `maxShares` 值，该值表示可同时共享磁盘的最大节点数。 例如，如果计划设置一个2节点故障转移群集，则可以设置 `maxShares=2`。 最大值是上限。 只要节点数低于指定的 `maxShares` 值，节点就可以加入或离开群集（装载或卸载磁盘）。

> [!NOTE]
> 仅当磁盘从所有节点分离时，才能设置或编辑 `maxShares` 值。

下表说明了按磁盘大小 `maxShares` 允许的最大值：

|磁盘大小  |maxShares 限制  |
|---------|---------|
|P15、P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁盘的 IOPS 和带宽限制不受 `maxShares` 值的影响。 例如，P15 磁盘的最大 IOPS 为1100，无论 maxShares = 1 还是 maxShares > 1。