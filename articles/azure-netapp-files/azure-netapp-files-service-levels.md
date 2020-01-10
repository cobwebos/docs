---
title: Azure NetApp 文件的服务级别 | Microsoft Docs
description: 介绍 Azure NetApp 文件的服务级别的吞吐量性能。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832586"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp 文件的服务级别
服务级别是容量池的属性。 根据分配给卷的配额，为容量池中的卷允许的最大吞吐量定义和区分服务级别。

## <a name="supported-service-levels"></a>支持的服务级别

Azure NetApp 文件支持三个服务级别： *Ultra*、*高级*和*标准*。 

* <a name="Ultra"></a>超存储器

    对于分配的卷配额，Ultra 存储层提供最多 128 MiB/秒的吞吐量。 

* <a name="Premium"></a>高级存储

    高级存储层提供了 64 MiB/秒的吞吐量，每 1 TiB 分配的卷配额。 

* <a name="Standard"></a>标准存储

    标准存储层提供的吞吐量最高可达每1个 TiB 的卷配额。

## <a name="throughput-limits"></a>吞吐量限制

卷的吞吐量限制由以下因素的组合确定：
* 卷所属的容量池的服务级别
* 分配给卷的配额  

下图演示了此概念：

![服务级别说明](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

在上面的示例1中，将为容量池中分配了 2 TiB 配额的容量池分配一个卷，吞吐量限制为 128 MiB/秒（2 TiB * 64 MiB/秒）。 无论容量池大小还是实际容量消耗，此方案都适用。

在上面的示例2中，将为容量池中具有分配了 100 GiB 配额的高级存储层的卷分配 6.25 MiB/秒的吞吐量限制（0.09765625 TiB * 64 MiB/秒）。 无论容量池大小还是实际容量消耗，此方案都适用。

## <a name="next-steps"></a>后续步骤

- 有关不同服务级别的价格，请参阅 [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
- 有关容量池中容量消耗的计算，请参阅[Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md) 
- [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
- 请参阅[Azure NetApp 文件服务级别协议（SLA）](https://azure.microsoft.com/support/legal/sla/netapp/)