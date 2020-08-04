---
title: 使用 Azure NetApp 文件管理快照 | Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件创建和管理快照。
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 271c3c9f63ee3f761826e214f3bf32a8df5f1cbe
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533285"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>使用 Azure NetApp 文件管理快照

Azure NetApp 文件支持创建按需快照，并使用快照策略来计划自动创建快照。  还可以将快照还原到新卷。  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>为卷创建按需快照

可以按需创建卷快照。 

1.  前往要为其创建快照的卷。 单击 "**快照**"。

    ![导航到快照](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  单击“+ 添加快照”，为卷创建按需快照****。

    ![添加快照](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  在“新建快照”窗口中，为要创建的新快照提供名称。   

    ![新建快照](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. 单击“确定”。 

## <a name="manage-snapshot-policies"></a>管理快照策略

你可以计划使用快照策略自动执行卷快照。 你还可以根据需要修改快照策略，或者删除不再需要的快照策略。  

### <a name="register-the-feature"></a>注册功能

**快照策略**功能目前处于预览阶段。 如果是首次使用此功能，则需要首先注册该功能。 

1. 注册功能： 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. 检查功能注册的状态： 

    > [!NOTE]
    > 在将更改为之前， **RegistrationState**可能处于 `Registering` 状态几分钟 `Registered` 。 等到状态**注册**后再继续。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

### <a name="create-a-snapshot-policy"></a>创建快照策略 

快照策略允许你按每小时、每天、每周或每月周期来指定快照创建频率。 还需要为卷指定要保留的最大快照数。  

1.  从 "NetApp 帐户" 视图中，单击 "**快照策略**"。

    ![快照策略导航](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  在 "快照策略" 窗口中，将 "策略状态" 设置为 "**已启用**"。 

3.  单击**每小时**、**每日**、**每周**或**每月**选项卡，创建每小时、每日、每周或每月快照策略。 指定**要保留的快照数**。  

    请参阅[Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)，了解卷允许的最大快照数。 

    以下示例显示每小时的快照策略配置。 

    ![每小时快照策略](../media/azure-netapp-files/snapshot-policy-hourly.png)

    以下示例显示了每日快照策略配置。

    ![每日快照策略](../media/azure-netapp-files/snapshot-policy-daily.png)

    以下示例显示了每周快照策略配置。

    ![每周快照策略](../media/azure-netapp-files/snapshot-policy-weekly.png)

    以下示例显示了每月快照策略配置。

    ![每月快照策略](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  单击“保存”  。  

如果需要创建其他快照策略，请重复步骤3。
创建的策略将显示在 "快照策略" 页中。

如果希望卷使用快照策略，需要[将策略应用到卷](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)。 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>将快照策略应用到卷

如果希望卷使用创建的快照策略，需要将策略应用到卷。 

1.  请在 "**卷**" 页上，右键单击要向其应用快照策略的卷，然后选择 "**编辑**"。

    ![卷右键单击菜单](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  在 "编辑" 窗口中的 "**快照策略**" 下，选择要用于卷的策略。  单击 **"确定"** 应用该策略。  

    ![快照策略编辑](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>修改快照策略 

您可以修改现有的快照策略，以更改策略状态、快照频率（每小时、每天、每周或每月）或要保留的快照数。  
 
1.  从 "NetApp 帐户" 视图中，单击 "**快照策略**"。

2.  右键单击要修改的快照策略，然后选择 "**编辑**"。

    ![快照策略右键单击菜单](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  在出现的 "快照策略" 窗口中进行更改，然后单击 "**保存**"。 

### <a name="delete-a-snapshot-policy"></a>删除快照策略 

你可以删除不再想要保留的快照策略。   

1.  从 "NetApp 帐户" 视图中，单击 "**快照策略**"。

2.  右键单击要修改的快照策略，然后选择 "**删除**"。

    ![快照策略右键单击菜单](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  单击 **"是"** 以确认要删除该快照策略。   

    ![快照策略删除确认](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>将快照还原到新卷

目前，只能将快照还原到新卷。 
1. 从 "卷" 边栏选项卡中选择 "**快照**" 以显示快照列表。 
2. 右键单击要还原的快照，并从菜单选项中选择 "**还原到新卷**"。  

    ![将快照还原到新卷](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. 在 "创建卷" 窗口中，为新卷提供以下信息：  
    * **名称**   
        指定要创建的卷的名称。  
        
        名称在资源组中必须唯一。 它的长度必须至少为三个字符。  它可以使用任何字母数字字符。

    * **配额**  
        指定要分配给卷的逻辑存储量。  

    ![还原到新卷](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. 单击 "**查看 + 创建**"。  单击“创建”。   
    新卷使用的协议与快照使用的协议相同。   
    快照还原到的新卷将显示在“卷”边栏选项卡中。

## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
