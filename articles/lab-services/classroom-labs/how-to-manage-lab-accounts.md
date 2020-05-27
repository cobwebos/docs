---
title: 管理 Azure 实验室服务中的实验室帐户 | Microsoft Docs
description: 了解如何在 Azure 订阅中创建实验室帐户、查看所有实验室帐户，或者删除实验室帐户。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: a67ba18b70f6b5b9eebb473e6cc2915bc937ce6b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588184"
---
# <a name="create-and-manage-lab-accounts"></a>创建和管理实验室帐户
在 Azure 实验室服务中，实验室帐户是托管实验室类型（如课堂实验室）的容器。 管理员可以设置一个具有 Azure 实验室服务的实验室帐户，并为能够在帐户中创建实验室的实验室所有者提供访问权限。 本文介绍如何创建实验室帐户、查看所有实验室帐户，或者删除实验室帐户。

## <a name="create-a-lab-account"></a>创建实验室帐户
以下步骤演示了如何使用 Azure 门户通过 Azure 实验室服务创建实验室。 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”。 在“DevOps”部分中，选择“实验室帐户” 。 如果选择“实验室帐户”旁边的星星 (`*`)，则会将其添加到左侧菜单上的“收藏夹”部分。  从下次开始，请在“收藏夹”下选择“实验室帐户”。 

    ![“所有服务”->“实验室帐户”](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 在“实验室帐户”页上，选择工具栏上的“添加”或页面上的“创建实验室帐户”。 

    ![在“实验室帐户”页上选择“添加”](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. 在“创建实验室帐户”页面的“基本信息” 选项卡上，执行以下操作： 
    1. 在“实验室帐户名称”中输入名称。 
    2. 选择要在其中创建实验室帐户的“Azure 订阅”。
    3. 在“资源组”中选择“新建”，然后输入资源组的名称 。
    4. 为“位置”选择要在其中创建实验室帐户的位置/区域。
    5. 对于“允许实验室创建者选择实验室位置”字段，请指定是否希望实验室创建者能够选择实验室位置。 默认情况下，此选项已禁用。 此选项禁用时，实验室创建者无法为他们正在创建的实验室指定位置。 实验室在距离实验室帐户最近的地理位置进行创建。 此选项启用时，实验室创建者可以在创建实验室时选择位置。 有关详细信息，请参阅[允许实验室创建者选取实验室的位置](allow-lab-creator-pick-lab-location.md)。 

        ![创建实验室帐户 -> 基本信息](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. 在页面底部选择“下一步:高级”，导航到“高级”选项卡，然后执行以下步骤： 
    1. 选择一个现有**共享映像库**或者创建一个。 你可以将模板 VM 保存在共享映像库中，以便供其他人重复使用。 有关共享映像库的详细信息，请参阅[使用 Azure 实验室服务中的共享映像库](how-to-use-shared-image-gallery.md)。
    2. 指定是否要在用户与 Windows 虚拟机断开连接时自动关闭 Windows 虚拟机。 指定虚拟机在自动关闭之前应等待用户重新连接多长时间。 
    3. 对于“对等虚拟网络”，请选择实验室网络的对等虚拟网络 (VNet)。 在此帐号中创建的实验室连接到所选 VNet 并在所选 VNet 中具有对资源的访问权限。 有关详细信息，请参阅[将实验室的虚拟网络连接到对等虚拟网络](how-to-connect-peer-virtual-network.md)。    
    8. 为实验室中的 VM 指定**地址范围**。 此地址范围应当采用无类别域际路由 (CIDR) 表示法（示例：10.20.0.0/23）。 将在此地址范围内创建实验室中的虚拟机。 有关详细信息，请参阅[为实验室中的 VM 指定地址范围](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > 仅在为实验室启用了“对等虚拟网络”时，“地址范围”属性才适用。

        ![创建实验室帐户 -> 高级](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. 在页面底部选择“下一步:标记”，以切换到“标记”选项卡。添加要与实验室帐户关联的任何标记。 标记是名称/值对，通过将相同的标记应用到多个资源和资源组，可以对资源进行分类并查看合并的账单。 有关详细信息，请参阅[使用标记来组织 Azure 资源](../../azure-resource-manager/management/tag-resources.md)。

    ![创建实验室帐户 -> 标记](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. 选择此页底部的“查看 + 创建”，切换到“查看 + 创建”选项卡。 
4. 查看此页上的摘要信息，并选择“创建”。 

    ![创建实验室帐户 -> 标记](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. 等到部署完成，展开“后续步骤”，然后选择“转到资源”，如下图所示： 

    还可选择工具栏上的钟形图标（“通知”），确认部署已成功，然后选择“转到资源”。 

    也可在“实验室帐户”页上选择“刷新”，然后选择已创建的实验室帐户 。 

    ![创建实验室帐户窗口](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 会看到以下“实验室帐户”页面：

    ![“实验室帐户”页面](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>查看实验室帐户
1. 登录 [Azure 门户](https://portal.azure.com)。
2. 从菜单中选择“所有资源”。 
3. 对于“类型”，选择“实验室帐户”。  
    也可按订阅、资源组、位置和标记进行筛选。 

    ![“所有资源”->“实验室帐户”](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>删除实验室帐户
按照上一节中的说明来显示列表中的实验室帐户。 根据以下说明删除实验室帐户： 

1. 选择要删除的**实验室帐户**。 
2. 从工具栏选择“删除”。 

    ![“实验室帐户”->“删除”按钮](../media/how-to-manage-lab-accounts/delete-button.png)
1. 键入“是”进行确认。
1. 选择“删除”。 

    ![删除实验室帐户 - 确认](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> 还可使用 Az.LabServices PowerShell 模块（预览版）来管理实验室帐户。 有关详细信息，请参阅 [GitHub 上的 Az.LabServices 主页](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

## <a name="next-steps"></a>后续步骤
请参阅目录 (TOC) 的“操作指南” -> “创建和配置实验室帐户(实验室帐户所有者)”部分中的其他文章。 