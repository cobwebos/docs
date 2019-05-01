---
title: 节点由 CloudSimple-Azure 的 VMware 解决方案概述
description: 了解有关 CloudSimple 节点和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b3c8fca1dd93f379860cc3b084fbb14d4a0c6380
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577355"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 节点概述

节点可以是：

* 专用的裸机计算安装 VMware ESXi 虚拟机监控程序主机  
* 若要创建私有云计算可以购买或保留的单元  
* 可供购买，或者保留 CloudSimple 服务可用的区域

节点是私有云的构建基块。  若要创建私有云，需要在同一 SKU 的三个节点的最小值。  若要展开私有云，请添加其他节点。  您可以向现有群集添加节点。 或者，您可以通过购买在 Azure 门户中的节点并将其关联到 CloudSimple 服务创建新群集。  购买的所有节点都都可见 CloudSimple 服务下。  从 CloudSimple 门户上购买的节点创建私有云。

## <a name="purchased-nodes"></a>已购买的节点

已购买的节点提供即用即付的容量。 购买节点可帮助你快速缩放按需将 VMware 群集。 您可以根据需要添加节点或删除已购买的节点，以减少 VMware 群集。 已购买的节点是按月进行计费，购买所在的订阅收费：

* 如果你的 Azure 订阅付款的信用卡，卡将立即计费。
* 如果通过发票付款计费，费用将显示在下次的账单上。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware 解决方案由 CloudSimple 节点 SKU

以下节点类型都可用于购买或保留项。

| SKU | CS28-节点 | CS36-节点 |
|-----|-------------|-------------|
| CPU | 2x2.2 g h z，28 个核心 (56 超线程) | 2x2.3 g h z，36 个核心 (72 超线程) |
| RAM | 256 GB | 512 GB |
| 缓存磁盘 |  1.6-TB NVMe | 3.2-TB NVMe |
| 容量的磁盘 | 5.625 原始 TB | 11.25 原始 TB |
| 存储类型 | 所有闪存 | 所有闪存 |

## <a name="limits"></a>限制

下面的节点限制适用于私有云。

| 资源 | 限制 |
|----------|-------|
| 要创建私有云的节点的最小数量 | 3 |
| 私有云上的群集中的节点的最大数目 | 16 |
| 在私有云中的节点最大数目 | 64 |
| 在新群集上的节点的最小数目 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[购买节点](create-nodes.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)