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
ms.date: 09/22/2019
ms.author: b-juche
ms.openlocfilehash: 818b3b59b1113875b6486ffe64bc8d2d30d613d3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325448"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp 文件的服务级别
服务级别是容量池的属性。 根据分配给卷的配额，为容量池中的卷允许的最大吞吐量定义和区分服务级别。

## <a name="supported-service-levels"></a>支持的服务级别

Azure NetApp 文件支持三个服务级别： *Ultra*、 *高级*和 *标准*。 

* <a name="Ultra"></a>超存储器

    超存储层提供了高达 128 MiB/秒的吞吐量，每 1 TiB 容量预配。 

* <a name="Premium"></a>高级存储

    高级存储层提供高达 64 MiB/秒的吞吐量，每 1 TiB 容量预配。 

* <a name="Standard"></a>标准存储

    标准存储层提供了高达每秒 16 MiB/秒的吞吐量，每 1 TiB 容量预配。

## <a name="throughput-limits"></a>吞吐量限制

卷的吞吐量限制由以下因素的组合确定：
* 卷所属的容量池的服务级别
* 分配给卷的配额  
* QoS 类型 (*自动* 或 *手动*) 容量池  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>吞吐量限制了自动 QoS 容量池中的卷的示例

下图显示了自动 QoS 容量池中卷的吞吐量限制示例：

![服务级别说明](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* 在上面的示例1中，将为自动 QoS 容量池中分配了 2 TiB 配额的自动 QoS 容量池分配一个卷，其吞吐量限制为 128 MiB/秒 (2 TiB * 64 MiB/秒) 。 无论容量池大小还是实际容量消耗，此方案都适用。

* 在上面的示例2中，将为自动 QoS 容量池中具有分配了 100 GiB 配额的高级存储层的卷分配 6.25 MiB/s 的吞吐量限制 (0.09765625 TiB * 64 MiB/秒) 。 无论容量池大小还是实际容量消耗，此方案都适用。

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>吞吐量限制手动 QoS 容量池中的卷的示例 

如果使用手动 QoS 容量池，则可以单独为卷分配容量和吞吐量。 在手动 QoS 容量池中创建卷时，可以 (MiB/秒) 值来指定吞吐量。 分配给手动 QoS 容量池中的卷的总吞吐量取决于池的大小和服务级别。 它在 TiB x 服务级别吞吐量/TiB) 中 (容量池大小的上限。 例如，具有 "超" 服务级别的 TiB 容量池具有 1280 MiB/s 的总吞吐量容量 (10 TiB x 128 MiB/s/TiB) 可用于卷。

对于 SAP HANA 系统，此容量池可用于创建以下卷。 每个卷都提供单个大小和吞吐量，以满足应用程序的要求：

* SAP HANA 数据量：大小为 4 TB，最多 704 MiB/秒
* SAP HANA 日志卷：大小为 0.5 TB，最高可达 256 MiB/秒
* SAP HANA 共享卷：大小为 1 TB，最多 64 MiB/秒
* 备份卷 SAP HANA：大小为 4.5 TB，最高可达 256 MiB/秒

下图演示了 SAP HANA 卷的方案：

![QoS SAP HANA 卷方案](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md) 
- [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp 文件服务级别协议 (SLA) ](https://azure.microsoft.com/support/legal/sla/netapp/)
- [动态更改卷的服务级别](dynamic-change-volume-service-level.md) 
- [跨区域复制的服务级别目标](cross-region-replication-introduction.md#service-level-objectives)