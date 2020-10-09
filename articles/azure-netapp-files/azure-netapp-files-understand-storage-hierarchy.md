---
title: Azure NetApp 文件的存储层次结构 | Microsoft Docs
description: 介绍了存储层次结构，包括 Azure NetApp 文件帐户、容量池和卷。
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
ms.topic: overview
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 435d74e771a9d887c87c9d10e6b525ac77cf97e8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91278322"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Azure NetApp 文件的存储层次结构

在 Azure NetApp 文件中创建卷之前，必须为预配的容量购买和设置池。  若要设置容量池，必须具有 NetApp 帐户。 了解存储层次结构可以帮助你设置和管理 Azure NetApp 文件资源。

> [!IMPORTANT] 
> Azure NetApp 文件目前不支持订阅间的资源迁移。

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp 帐户

- NetApp 帐户用作成分容量池的管理组。  
- NetApp 帐户不同于常规的 Azure 存储帐户。 
- NetApp 帐户从作用域角度而言是区域性的。   
- 在一个区域中可以有多个 NetApp 帐户，但每个 NetApp 帐户仅绑定到单个区域。

## <a name="capacity-pools"></a><a name="capacity_pools"></a>容量池

了解容量池的工作原理有助于选择符合存储需求的容量池类型。 

### <a name="general-rules-of-capacity-pools"></a>容量池的一般规则

- 容量池是按其预配的容量度量的。   
    有关其他信息，请参阅 [QoS 类型](#qos_types)。  
- 该容量预配为你购买的固定 SKU 数（例如 4-TiB 容量）。
- 容量池只能有一个服务级别。  
- 每个容量池可以仅属于一个 NetApp 帐户。 但是，你可以在一个 NetApp 帐户中拥有多个容量池。  
- 容量池无法在 NetApp 帐户之间移动。   
  例如，在下面的[存储层次结构的概念图](#conceptual_diagram_of_storage_hierarchy)中，无法将容量池 1 从 NetApp 帐户 US East 移动到 NetApp 帐户 US West 2。  
- 在容量池中的所有卷都被删除之前，无法删除容量池。

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>容量池的服务质量 (QoS) 类型

QoS 类型是容量池的一项属性。 Azure NetApp 文件提供两种 QoS 类型的容量池。 

- *自动* QoS 类型  

    创建容量池时，默认 QoS 类型为自动。

    在自动 QoS 容量池中，吞吐量会自动分配到池中的卷，并与分配给这些卷的大小配额成正比。 

    分配给卷的最大吞吐量取决于容量池的服务级别和卷的大小配额。 有关示例计算，请参阅 [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)。

- <a name="manual_qos_type"></a>*手动* QoS 类型  

     > [!IMPORTANT] 
     > 为容量池使用手动 QoS 类型时需要进行注册。  请参阅[管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)。  

    可以选择为容量池使用手动 QoS 类型。

    在手动 QoS 容量池中，可以单独为卷分配容量和吞吐量。 使用手动 QoS 容量池创建的所有卷的总吞吐量受池总吞吐量的限制。  它由池大小和服务级别吞吐量共同决定。 

    例如，具有“超级”服务级别的 4-TiB 容量池可为卷提供 512 MiB/s (4 TiB x 128 MiB/s/TiB) 的总吞吐容量。


## <a name="volumes"></a><a name="volumes"></a>卷

- 卷按逻辑容量消耗进行度量，并且是可缩放的。 
- 卷的容量消耗是依据其池的预配容量计数的。
- 卷的吞吐量消耗计入其池的可用吞吐量。 请参阅[手动 QoS 类型](#manual_qos_type)。
- 每个卷只属于一个池，但一个池可以包含多个卷。 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>存储层次结构的概念图 
下面的示例显示了 Azure 订阅、NetApp 帐户、容量池和卷之间的关系。   

![存储层次结构的概念图](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
- [注册 Azure NetApp 文件](azure-netapp-files-register.md)
- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Azure NetApp 文件的性能考虑因素](azure-netapp-files-performance-considerations.md)
- [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
- [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
