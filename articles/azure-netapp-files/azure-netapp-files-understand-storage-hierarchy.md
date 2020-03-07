---
title: Azure NetApp 文件的存储层次结构是怎样的 |Microsoft Docs
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
ms.date: 02/27/2020
ms.author: b-juche
ms.openlocfilehash: 70d3a2a501952a5e20b1ff8e99f48f4d7aefce8d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163958"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Azure NetApp 文件的存储层次结构是怎样的

在 Azure NetApp 文件中创建卷之前，必须为预配的容量购买和设置池。  若要设置容量池，必须具有 NetApp 帐户。 了解存储层次结构可以帮助你设置和管理 Azure NetApp 文件资源。

> [!IMPORTANT] 
> Azure NetApp 文件目前不支持订阅间的资源迁移。

## <a name="azure_netapp_files_account"></a>NetApp 帐户

- NetApp 帐户用作成分容量池的管理组。  
- NetApp 帐户不同于常规的 Azure 存储帐户。 
- NetApp 帐户从作用域角度而言是区域性的。   
- 在一个区域中可以有多个 NetApp 帐户，但每个 NetApp 帐户仅绑定到单个区域。

## <a name="capacity_pools"></a>容量池

- 容量池是按其预配的容量度量的。  
- 该容量预配为你购买的固定 SKU 数（例如 4-TiB 容量）。
- 容量池只能有一个服务级别。  
- 每个容量池可以仅属于一个 NetApp 帐户。 但是，你可以在一个 NetApp 帐户中拥有多个容量池。  
- 容量池无法在 NetApp 帐户之间移动。   
  例如，在下面的[存储层次结构的概念图](#conceptual_diagram_of_storage_hierarchy)中，无法将容量池 1 从 NetApp 帐户 US East 移动到 NetApp 帐户 US West 2。  
- 在容量池中的所有卷都被删除之前，无法删除容量池。

## <a name="volumes"></a>卷

- 卷按逻辑容量消耗进行度量，并且是可缩放的。 
- 卷的容量消耗是依据其池的预配容量计数的。
- 每个卷只属于一个池，但一个池可以包含多个卷。 
- 不能在容量池之间移动卷。 <!--Within the same NetApp account, you can move a volume across pools.  -->   
  例如，在下面的[存储层次结构的概念图](#conceptual_diagram_of_storage_hierarchy)中，无法将卷从容量池 1 移动到容量池 2。
- 在卷的所有快照都被删除之前，无法删除卷。

## <a name="conceptual_diagram_of_storage_hierarchy"></a>存储层次结构的概念图 
下面的示例显示了 Azure 订阅、NetApp 帐户、容量池和卷之间的关系。   

![存储层次结构的概念图](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
- [注册 Azure NetApp 文件](azure-netapp-files-register.md)
