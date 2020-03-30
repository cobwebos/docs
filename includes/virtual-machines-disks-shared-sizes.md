---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471669"
---
目前，只有高级 SSD 才能启用共享磁盘。 支持此功能的磁盘大小为 P15 和更高。 不同的磁盘大小可能具有不同的`maxShares`限制，在设置值时不能超过该`maxShares`限制。

对于每个磁盘，可以定义一个`maxShares`值，该值表示可以同时共享磁盘的最大节点数。 例如，如果您计划设置 2 节点故障转移群集，则设置`maxShares=2`。 最大值是上限。 只要节点数低于指定`maxShares`值，节点就可以加入或离开群集（装载或卸载磁盘）。

> [!NOTE]
> 仅当`maxShares`磁盘与所有节点分离时，才能设置或编辑该值。

下表说明了按磁盘大小表示的`maxShares`允许最大值：

|磁盘大小  |最大共享限制  |
|---------|---------|
|P15， P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁盘的 IOPS 和带宽限制不受`maxShares`该值的影响。 例如，P15 磁盘的最大 IOPS 是 1100，无论是最大共享 = 1 还是最大共享> 1。