---
title: 为 Azure NetApp 文件创建复制对等互连 |Microsoft Docs
description: 介绍如何为 Azure NetApp 文件创建卷复制对等互连，以设置跨区域复制。
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
ms.openlocfilehash: df70f8a37a7223119068afd323583ea6126ca542
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708588"
---
# <a name="create-replication-peering-for-azure-netapp-files"></a>为 Azure NetApp 文件创建复制对等互连

> [!IMPORTANT]
> 跨区域复制功能目前为公共预览版。 需要提交候补请求，以便通过 [Azure NetApp 文件跨区域复制候补提交页](https://aka.ms/anfcrrpreviewsignup)访问该功能。 使用跨区域复制功能之前，请先从 Azure NetApp 文件团队等待官方确认电子邮件。

本文介绍如何通过创建复制对等互连来设置跨区域复制。 

设置复制对等互连使你能够以异步方式将数据从 Azure NetApp 文件卷 (源) 复制到另一 Azure NetApp 文件卷 (目标) 。 源卷和目标卷必须部署在不同的区域中。 目标容量池的服务级别可以与源容量池的服务级别匹配，也可以选择不同的服务级别。   

Azure NetApp 文件复制当前不支持多个订阅;所有复制都必须在单个订阅下执行。

在开始之前，请确保已查看 [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)。  

## <a name="locate-the-source-volume-resource-id"></a>找到源卷资源 ID  

需要获取要复制的源卷的资源 ID。 

1. 请前往源卷，然后选择 "设置" 下的 " **属性** " 以显示源卷资源 ID。   
    ![定位源卷资源 ID](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. 将资源 ID 复制到剪贴板。  稍后需要用到此值。

## <a name="create-the-data-replication-volume-the-destination-volume"></a>创建数据复制卷 (目标卷) 

需要创建要将源卷中的数据复制到其中的目标卷。  你需要在目标区域中具有一个 NetApp 帐户和容量池，然后才能创建目标卷。 

1. 目标帐户必须与源卷区域在不同的区域中。 如有必要，请按照 [创建 netapp 帐户](azure-netapp-files-create-netapp-account.md)中的步骤在 Azure 区域中创建一个用于复制的 NetApp 帐户。   
你还可以在不同的区域中选择现有的 NetApp 帐户。  

2. 如有必要，请按照 [设置容量池中](azure-netapp-files-set-up-capacity-pool.md)的步骤在新创建的 NetApp 帐户中创建容量池。   

    你还可以选择现有容量池来托管复制目标卷。  

    目标容量池的服务级别可以与源容量池的服务级别匹配，也可以选择不同的服务级别。

3. 按照将 [子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)中的步骤操作，在区域中委托要用于复制的子网。

4. 通过选择目标 NetApp 帐户中存储服务下的 **卷** 来创建数据复制卷。 然后单击 " **+ 添加数据复制** " 按钮。  

    ![添加数据复制](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. 在显示的 "创建卷" 页中，在 " **基本** 信息" 选项卡下填写以下字段：
    * 卷名
    * 容量池
    * 卷配额
        > [!NOTE] 
        > 建议使用源卷的卷配额大小镜像。
    * 虚拟网络 
    * 子网

    有关字段的详细信息，请参阅 [创建 NFS 卷](azure-netapp-files-create-volumes.md#create-an-nfs-volume)。 

6. 在 " **协议** " 选项卡下，选择与源卷相同的协议。  
对于 NFS 协议，请确保导出策略规则满足将访问导出的远程网络中的任何主机的要求。  

7. 在 " **标记** " 选项卡下，根据需要创建键/值对。  

8. 在 " **复制** " 选项卡下，粘贴在 ["查找源卷资源 id](#locate-the-source-volume-resource-id)" 中获取的源卷资源 id，然后选择所需的复制计划。 复制计划的选项包括：每10分钟、每小时、每日、每周和每月。  

    ![创建卷复制](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. 单击 " **查看 + 创建**"，然后单击 " **创建** " 以创建数据复制卷。   

    ![查看和创建复制](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>从源卷授权复制  

若要授权复制，需要获取复制目标卷的资源 ID 并将其粘贴到复制源卷的授权字段。 

1. 在 Azure 门户中，导航到 "Azure NetApp 文件"。

2. 中转到复制目标卷所在的目标 NetApp 帐户和目标容量池。

3. 选择复制目标卷，中转到 "设置" 下的 " **属性** "，然后找到目标卷的 " **资源 ID** "。 将目标卷资源 ID 复制到剪贴板。

    ![属性资源 ID](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. 在 Azure NetApp 文件中，请参阅复制源帐户和源容量池。 

5. 找到复制源卷并选择它。 在存储服务下，单击 " **复制** "，然后单击 " **授权**"。

    ![授权复制](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. 在 "授权" 字段中，粘贴你在步骤3中获取的目标复制卷资源 ID，然后单击 **"确定"**。

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)

