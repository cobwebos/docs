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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523112"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp 文件的服务级别
服务级别为容量池的属性。 服务级别定义，并通过基于分配给该卷的配额容量池中卷的允许最大吞吐量区分。

## <a name="supported-service-levels"></a>支持的服务级别

Azure 的 NetApp 文件支持三个服务级别：*超高*， *Premium*，和*标准*。 

* <a name="Ultra"></a>超高存储

    超高的存储层提供高达 128 MiB/秒的吞吐量为每个 1 TiB 的分配的卷配额。 

* <a name="Premium"></a>高级存储

    高级存储层提供高达 64 MiB/秒的吞吐量为每个 1 TiB 的分配的卷配额。 

* <a name="Standard"></a>标准存储

    标准存储层提供高达 16 MiB/秒的吞吐量为每个 1 TiB 的分配的卷配额。

## <a name="throughput-limits"></a>吞吐量限制

卷的吞吐量限制取决于以下因素的组合：
* 容量池卷所属的服务级别
* 分配给该卷配额  

下图说明了这一概念：

![服务级别图](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

示例 1 更高版本中，将卷从分配的配额的 2 个 TiB 高级存储层的容量池将分配的吞吐量限制为 128 MiB/秒 (2 个 TiB * 64 MiB/秒)。 此方案适用而不考虑容量池的大小或实际容量消耗。

在上面的示例 2，，将卷从分配的配额的 100 GiB 的高级存储层的容量池将分配的吞吐量限制为 6.25 MiB/秒 (0.09765625 TiB * 64 MiB/秒)。 此方案适用而不考虑容量池的大小或实际容量消耗。

## <a name="next-steps"></a>后续步骤

- 有关不同服务级别的价格，请参阅 [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
- 请参阅[Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)中容量池的容量消耗的计算 
- [设置容量池](azure-netapp-files-set-up-capacity-pool.md)