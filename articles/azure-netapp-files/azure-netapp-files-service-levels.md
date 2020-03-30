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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832586"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp 文件的服务级别
服务级别是容量池的属性。 服务级别根据分配给卷的配额，根据容量池中允许的最大吞吐量定义和区分。

## <a name="supported-service-levels"></a>支持的服务级别

Azure NetApp 文件支持三个服务级别：*超*、*高级*和*标准*。 

* <a name="Ultra"></a>超存储

    Ultra 存储层提供高达 128 MiB/s 的吞吐量每 1 TiB 分配的容量配额。 

* <a name="Premium"></a>高级存储

    高级存储层提供最多 64 MiB/s 的吞吐量每 1 TiB 分配的卷配额。 

* <a name="Standard"></a>标准存储

    标准存储层提供最多 16 MiB/s 的吞吐量每 1 TiB 分配的卷配额。

## <a name="throughput-limits"></a>吞吐量限制

卷的吞吐量限制由以下因素的组合决定：
* 卷所属的容量池的服务级别
* 分配给卷的配额  

这一概念如下图所示：

![服务级别图](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

在上面的示例 1 中，分配了 2 TiB 配额的高级存储层的容量池中的卷将被分配为 128 MiB/s （2 TiB = 64 MiB/s）。 无论容量池大小或实际体积消耗量如何，此方案都适用。

在上面的示例 2 中，分配了 100 GiB 配额的高级存储层的容量池中的卷将分配 6.25 MiB/s （0.09765625 TiB = 64 MiB/s） 的吞吐量限制。 无论容量池大小或实际体积消耗量如何，此方案都适用。

## <a name="next-steps"></a>后续步骤

- 有关不同服务级别的价格，请参阅 [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
- 有关计算容量池中的容量消耗，请参阅[Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md) 
- [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
- 请参阅[Azure NetApp 文件的服务级别协议 （SLA）](https://azure.microsoft.com/support/legal/sla/netapp/)