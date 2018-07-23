---
title: 了解 Azure NetApp 文件的存储层次结构 |Microsoft Docs
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 6f5ed4e7ede9a098d69b7a40f44dd60f9b400472
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010984"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>了解 Azure NetApp 文件的存储层次结构

在 Azure NetApp 文件中创建卷之前，必须为预配的容量购买和设置池。  若要设置容量池，必须具有 NetApp 帐户。 了解存储层次结构可以帮助你设置和管理 Azure NetApp 文件资源。

## <a name="azure_netapp_files_account"></a>NetApp 帐户

- NetApp 帐户用作成分容量池的管理组。  
- NetApp 帐户不同于常规的 Azure 存储帐户。 
- NetApp 帐户从作用域角度而言是区域性的。   
- 在一个区域中可以有多个 NetApp 帐户，但每个 NetApp 帐户仅绑定到单个区域。

## <a name="capacity_pools"></a>容量池

- 容量池是按其预配的容量度量的。  
- 该容量预配为你购买的固定 SKU 数（例如 4-TB 容量）。
- 容量池只能有一个服务级别。  
  目前，仅提供了高级服务级别。
- 每个容量池仅属于一个 NetApp 帐户。  
- 容量池无法在 NetApp 帐户之间移动。   
  例如，在下面的[存储层次结构的概念图](#conceptual_diagram_of_storage_hierarchy)中，无法将容量池 1 从 NetApp 帐户 US East 移动到 NetApp 帐户 US West 2。  

## <a name="volumes"></a>卷

- 卷是按逻辑容量消耗度量的，并且每个卷最大可以扩展到 100 TB。
- 卷的容量消耗是依据其池的预配容量计数的。
- 每个卷只属于一个池，但一个池可以包含多个卷。 
- 在同一 NetApp 帐户中，可以在池之间移动卷。    
  例如，在下面的[存储层次结构的概念图](#conceptual_diagram_of_storage_hierarchy)中，可以将卷从容量池 1 移动到容量池 2。

## <a name="conceptual_diagram_of_storage_hierarchy"></a>存储层次结构的概念图 
下面的示例显示了 Azure 订阅、NetApp 帐户、容量池和卷之间的关系。   

![存储层次结构的概念图](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>后续步骤

1. [创建 NetApp 帐户](azure-netapp-files-create-netapp-account.md)
2. [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
3. [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
4. [为卷配置导出策略（可选）](azure-netapp-files-configure-export-policy.md)