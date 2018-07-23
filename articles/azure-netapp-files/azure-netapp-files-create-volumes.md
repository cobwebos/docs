---
title: 为 Azure NetApp 文件创建卷 | Microsoft Docs
description: 介绍了如何为 Azure NetApp 文件创建卷。
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
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011086"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>为 Azure NetApp 文件创建卷

卷的容量消耗是依据其池的预配容量计数的。  可以在一个容量池中创建多个卷，但卷的总容量消耗不能超过池大小。 

## <a name="before-you-begin"></a>开始之前 
必须已设置容量池。  

[设置容量池](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>步骤 
1.  从“管理容量池”边栏选项卡中单击“卷”边栏选项卡。 
2.  单击“+ 添加卷”以创建卷。  
    此时将显示“新建卷”窗口。

3.  在“新建卷”窗口中，单击“创建”，并提供以下字段的信息：   
    * **名称**      
        指定要创建的卷的名称。   
        名称在资源组中必须唯一。 它的长度必须至少为 3 个字符。  它可以使用任何字母数字字符。

    * **文件路径**  
        指定将用来创建新卷的导出路径的文件路径。 导出路径用来装载并访问卷。   
        装载目标是 NFS 服务 IP 地址的终结点。 它是自动生成的。    
        文件路径名只能包含字母、数字和连字符 ("-")。 它的长度必须介于 16 到 40 个字符之间。  

    * **配额**  
        指定分配给卷的逻辑存储量。  
        “可用配额”字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (Vnet)。 你指定的 Vnet 必须已配置了 Azure NetApp 文件。 只能从与卷位于同一位置的 Vnet 访问 Azure NetApp 文件服务。   

    ![新建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  单击“确定”。 
 
卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="next-steps"></a>后续步骤  
[为卷配置导出策略（可选）](azure-netapp-files-configure-export-policy.md)

