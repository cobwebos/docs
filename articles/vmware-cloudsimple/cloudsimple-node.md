---
title: Azure VMware 解决方案（按云简单 - 节点概述）
description: 了解云简单节点和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024919"
---
# <a name="cloudsimple-nodes-overview"></a>云简单节点概述

节点是私有云的构建基块。 节点为：

* 安装 VMware ESXi 虚拟机管理程序的专用裸机计算主机  
* 可以预配或保留以创建私有云的计算单元
* 可在云简单服务可用的区域提供或保留

从预配节点创建私有云。 要创建私有云，至少需要同一 SKU 的三个节点。 要扩展私有云，添加其他节点。  通过将节点预配到 Azure 门户中并将节点与 CloudSimple 服务相关联，可以将节点添加到现有群集，也可以创建新群集。  所有预配的节点在 CloudSimple 服务下可见。  

## <a name="provisioned-nodes"></a>预配节点

预配节点提供即用即付容量。 预配节点可帮助您按需快速扩展 VMware 群集。 您可以根据需要添加节点或删除预配节点以缩小 VMware 群集。 预配节点按月计费，并在预配时向订阅收费。

* 如果使用信用卡为 Azure 订阅付费，则该卡将立即计费。
* 如果您按发票计费，则费用将显示在下一张发票上。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware 解决方案（云简单节点 SKU）

以下类型的节点可用于预配或预留。

| SKU           | CS28 - 节点                 | CS36 - 节点                 | CS36m - 节点                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 区域        | 美国东部、美国西部            | 美国东部、美国西部            | 西欧                 |
| CPU           | 2x2.2 GHz，28 个内核 （56 HT） | 2x2.3 GHz，36 个内核 （72 HT） | 2x2.3 GHz，36 个内核 （72 HT） |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| 缓存磁盘    | 1.6 TB NVMe                 | 3.2 TB NVMe                 | 3.2 TB NVMe                 |
| 容量磁盘 | 5.625 TB 原始                | 11.25 TB 原始                | 15.36 TB 原始                |
| 存储类型  | 所有闪存                   | 所有闪存                   | 所有闪存                   |

## <a name="limits"></a>限制

以下节点限制适用于私有云。

| 资源 | 限制 |
|----------|-------|
| 创建私有云的最小节点数 | 3 |
| 私有云群集中的最大节点数 | 16 |
| 私有云中的最大节点数 | 64 |
| 新群集上的节点最小数量 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[预配节点](create-nodes.md)
* 了解[私有云](cloudsimple-private-cloud.md)
