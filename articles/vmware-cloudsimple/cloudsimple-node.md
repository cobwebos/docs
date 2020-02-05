---
title: Azure VMware 解决方案（AVS）-节点概述
description: 了解 AVS 节点和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024919"
---
# <a name="avs-nodes-overview"></a>AVS 节点概述

节点是 AVS 私有云的构建基块。 节点是：

* 安装了 VMware ESXi 虚拟机监控程序的专用裸机计算主机  
* 一种计算单元，可以购买或保留以创建 AVS 私有云
* 可在 AVS 服务可用的区域购买或保留

从购买的节点创建一个 AVS 私有云。 若要创建 AVS 私有云，你需要至少三个相同 SKU 的节点。 若要扩展 AVS 私有云，请添加其他节点。 你可以向现有群集添加节点或通过在 Azure 门户中购买节点并将其与 AVS 服务关联来创建新群集。 所有购买的节点都在 AVS 服务下可见。 

## <a name="provisioned-nodes"></a>预配节点

预配的节点提供即用即付容量。 预配节点可帮助你根据需要快速扩展 VMware 群集。 你可以根据需要添加节点，或者删除预配的节点以缩减 VMware 群集。 预配的节点会按月计费，并按预配的订阅收取费用。

* 如果你通过信用卡为你的 Azure 订阅付费，则该卡会立即收费。
* 如果你按发票计费，则费用将显示在下一个发票上。

## <a name="vmware-solution-by-avs-nodes-sku"></a>使用 AVS 节点的 VMware 解决方案 SKU

以下类型的节点可用于预配或预留。

| SKU           | CS28-节点                 | CS36-节点                 | CS36m-节点                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 地区        | 美国东部、美国西部            | 美国东部、美国西部            | 西欧                 |
| CPU           | 2x 2.2 GHz，28个内核（56 HT） | 2x 2.3 GHz，36核心（72 HT） | 2x 2.3 GHz，36核心（72 HT） |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| 缓存磁盘    | 1.6-TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 容量磁盘 | 5.625 TB Raw                | 11.25 TB Raw                | 15.36 TB Raw                |
| 存储类型  | 所有闪存                   | 所有闪存                   | 所有闪存                   |

## <a name="limits"></a>限制

以下节点限制适用于 AVS 私有云。

| 资源 | 限制 |
|----------|-------|
| 创建 AVS 私有云的最小节点数 | 3 |
| AVS 私有云上群集中的最大节点数 | 16 |
| AVS 私有云中的最大节点数 | 64 |
| 新群集上的最小节点数 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[购买节点](create-nodes.md)
* 了解[AVS 私有云](cloudsimple-private-cloud.md)
* 了解如何[预配节点](create-nodes.md)
* 了解[私有云](cloudsimple-private-cloud.md)
