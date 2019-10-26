---
title: Azure VMware 解决方案（按 CloudSimple）-节点概述
description: 了解有关 CloudSimple 节点和概念的信息。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899023"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 节点概述

节点是私有云的构建基块。 节点是：

* 安装了 VMware ESXi 虚拟机监控程序的专用裸机计算主机  
* 可以预配或保留以创建私有云的计算单元
* 可在 CloudSimple 服务可用的区域中设置或保留

从预配的节点创建私有云。 若要创建私有云，你需要至少三个相同 SKU 的节点。 若要扩展私有云，请添加其他节点。  你可以向现有群集添加节点或通过在 Azure 门户中预配节点并将其与 CloudSimple 服务关联来创建新群集。  所有预配的节点都在 CloudSimple 服务下可见。  

## <a name="provisioned-nodes"></a>预配节点

预配的节点提供即用即付容量。 预配节点可帮助你根据需要快速扩展 VMware 群集。 你可以根据需要添加节点，或者删除预配的节点以缩减 VMware 群集。 预配的节点会按月计费，并按预配的订阅收取费用。

* 如果你通过信用卡为你的 Azure 订阅付费，则该卡会立即收费。
* 如果你按发票计费，则费用将显示在下一个发票上。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>通过 CloudSimple 节点的 VMware 解决方案 SKU

以下类型的节点可用于预配或预留。

| SKU           | CS28-节点                 | CS36-节点                 | CS36m-节点                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 地区        | 美国东部、美国西部            | 美国东部、美国西部            | 欧洲西部                 |
| CPU           | 2x 2.2 GHz，28个内核（56 HT） | 2x 2.3 GHz，36核心（72 HT） | 2x 2.3 GHz，36核心（72 HT） |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| 缓存磁盘    | 1.6-TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 容量磁盘 | 5.625 TB Raw                | 11.25 TB Raw                | 15.36 TB Raw                |
| 存储类型  | 所有闪存                   | 所有闪存                   | 所有闪存                   |

## <a name="limits"></a>Limits

以下节点限制适用于私有云。

| 资源 | Limit |
|----------|-------|
| 要创建私有云的节点的最小数量 | 3 |
| 私有云群集中节点的最大数目 | 16 |
| 私有云中节点的最大数目 | 64 |
| 新群集上的最小节点数 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[预配节点](create-nodes.md)
* 了解[私有云](cloudsimple-private-cloud.md)
