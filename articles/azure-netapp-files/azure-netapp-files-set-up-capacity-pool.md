---
title: 为 Azure NetApp 文件设置容量池 |Microsoft Docs
description: 介绍了如何设置容量池，以便可以在其中创建卷。
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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325398"
---
# <a name="set-up-a-capacity-pool"></a>设置容量池

设置容量池可以在其中创建卷。  

## <a name="before-you-begin"></a>在开始之前 

必须已经创建了一个 NetApp 帐户。   

[创建 NetApp 帐户](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>步骤 

1. 转到 NetApp 帐户的管理边栏选项卡，从导航窗格中单击“容量池”。****  
    
    ![导航到容量池](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. 单击“+ 添加池”**** 以创建一个新容量池。   
    此时将显示“新建容量池”窗口。

3. 提供新容量池的以下信息：  
   * **Name**  
     指定容量池的名称。  
     容量池名称对于每个 NetApp 帐户必须是唯一的。

   * **服务级别**   
     此字段显示容量池的目标性能。  
     指定容量池的服务级别： " [**Ultra**](azure-netapp-files-service-levels.md#Ultra)"、" [**高级**](azure-netapp-files-service-levels.md#Premium)" 或 " [**标准**](azure-netapp-files-service-levels.md#Standard)"。

    * **规格**     
     指定你购买的容量池的大小。        
     最小容量池大小是 4 TiB。 可以创建大小为 4 TiB 的倍数的池。   

   * **QoS**   
     指定容量池是否应使用 **手动** 或 **自动** QoS 类型。  

     请参阅 [存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 和 [性能注意事项](azure-netapp-files-performance-considerations.md) ，了解 QoS 类型。  

     > [!IMPORTANT] 
     > 将 **QoS 类型** 设置为 " **手动** " 是永久性的。 不能转换手动 QoS 容量池以使用自动 QoS。 但是，可以转换自动 QoS 容量池以使用手动 QoS。 请参阅 [更改容量池以使用手动 QoS](manage-manual-qos-capacity-pool.md#change-to-qos)。   
     > 为容量池使用手动 QoS 类型时需要进行注册。 请参阅[管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md#register-the-feature)。 

    ![新建容量池](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. 单击“创建”。

## <a name="next-steps"></a>后续步骤 

- [存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 
- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
- [将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)
