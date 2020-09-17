---
title: 使用 Azure NetApp 文件跨区域复制来管理灾难恢复 |Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件跨区域复制来管理灾难恢复。
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708548"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>使用跨区域复制管理灾难恢复 

源卷和目标卷之间正在进行复制 (参阅 [创建复制对等互连](cross-region-replication-create-peering.md)) 为灾难恢复事件做好准备。 

发生此类事件时，可以 [故障转移到目标卷](#break-replication-peering-to-activate-the-destination-volume)，使客户端能够读取和写入目标卷。 

灾难恢复之后，你可以使用重新 [同步操作](#resync-replication-to-reactivate-the-source-volume) 来故障回复到源卷，并使用目标卷数据覆盖源卷数据。  然后，重新 [建立源到目标复制](#reestablish-source-to-destination-replication) 并重新装载源卷以供客户端访问。 

详细信息如下所述。 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>中断复制对等互连以激活目标卷

需要激活目标卷时 (例如，要故障转移到目标区域) 时，需要中断复制对等互连，并装载目标卷。  

1. 若要中断复制对等互连，请选择目标卷。 在 "存储服务" 下单击 " **复制** "。  

2.  继续之前，请检查以下字段：  
    * 确保镜像状态显示为 "已 ***镜像***"。   
        如果镜像状态显示为 *未初始化*，请勿尝试中断复制对等互连。
    * 确保关系状态显示为 " ***空闲***"。   
        如果关系状态显示 "正在 *传输*"，则不要尝试中断复制对等互连。   

    请参阅 [显示复制关系的运行状况状态](cross-region-replication-display-health-status.md)。 

3.  单击 " **中断对等互连**"。  

4.  在出现提示时键入 **Yes** ，然后单击 " **中断** " 按钮。 

    ![中断复制对等互连](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  按照为 [Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)中的步骤，装载目标卷。   
    此步骤使客户端能够访问目标卷。

## <a name="resync-replication-to-reactivate-the-source-volume"></a>重新同步复制以重新激活源卷   

灾难恢复之后，可以通过执行重新同步操作来重新激活源卷。  重新同步操作将反转复制过程，并将数据从目标卷同步到源卷。  

> [!IMPORTANT] 
> 重新同步操作用目标卷数据覆盖源卷数据。  UI 会提醒您可能会丢失数据。 在操作开始之前，系统将提示您确认重新同步操作。

1. 若要重新同步复制，请选择 *源* 卷。 在 "存储服务" 下单击 " **复制** "。 然后单击 "重新 **同步**"。  

2. 在出现提示时键入 **Yes** ，然后单击 "重新 **同步** " 按钮。 
 
    ![重新同步复制](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. 按照 " [复制关系的运行状况状态](cross-region-replication-display-health-status.md)" 中的步骤，监视源卷运行状况状态。   
    当 "源卷运行状况" 状态显示以下值时，将完成重新同步操作，并在源卷上捕获在目标卷上所做的更改：   

    * 镜像状态： *镜像*  
    * 传输状态： *空闲*  

## <a name="reestablish-source-to-destination-replication"></a>重新建立源到目标复制

在从目标到源的重新同步操作完成后，需要再次中断复制对等互连，以便重新建立源到目标复制。 还应重新装载源卷，以便客户端可以访问它。  

1. 中断复制对等互连：  
    a. 选择 *目标* 卷。 在 "存储服务" 下单击 " **复制** "。  
    b. 继续之前，请检查以下字段：   
    * 确保镜像状态显示为 "已 ***镜像***"。   
    如果镜像状态显示为 *未初始化*，请勿尝试中断复制对等互连。  
    * 确保关系状态显示为 " ***空闲***"。   
    如果关系状态显示 "正在 *传输*"，则不要尝试中断复制对等互连。    

        请参阅 [显示复制关系的运行状况状态](cross-region-replication-display-health-status.md)。 

    c. 单击 " **中断对等互连**"。   
    d. 在出现提示时键入 **Yes** ，然后单击 " **中断** " 按钮。  

2. 将源卷与目标卷重新同步：  
    a. 选择 *目标* 卷。 在 "存储服务" 下单击 " **复制** "。 然后单击 "重新 **同步**"。   
    b. 在出现提示时键入 **Yes** ，然后单击 "重新 **同步** " 按钮。

3. 按照为 [Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)中的步骤，重新装载源卷。  
    此步骤使客户端可以访问源卷。

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [显示复制关系的运行状况状态](cross-region-replication-display-health-status.md)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)

