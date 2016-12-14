---
title: "在 Azure 开发测试实验室中添加所有者和用户 | Microsoft Docs"
description: "使用 Azure 门户或 PowerShell 在 Azure 开发测试实验室中添加所有者和用户"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc804ad952e35b7441ea3699bc8e43895f146d53


---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中添加所有者和用户
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

[Azure 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 控制 Azure 开发测试实验室中的访问。 使用RBAC，可以将团队中的职责分配给*角色*，仅授予用户执行其作业所需的访问权限。 RBAC 的三个角色分别是所有者、开发实验室用户和参与者。 在本文中，将了解这三个主要 RBAC 角色的每个角色可以执行的操作。 在这里，将了解如何通过门户和 PowerShell 脚本将用户添加到实验室，以及如何在订阅级别添加用户。

## <a name="actions-that-can-be-performed-in-each-role"></a>每个角色可以执行的操作
可以为用户分配一下三种主要角色：

* 所有者
* DevTest 实验室用户
* 参与者

下表说明了每个角色的用户可以执行的操作：

| **此角色的用户可以执行的操作** | **实验室用户** | **所有者** | **参与者** |
| --- | --- | --- | --- |
| **实验室任务** | | | |
| 将用户添加到实验室 |否 |是 |否 |
| 更新成本设置 |否 |是 |是 |
| **VM 基本任务** | | | |
| 添加和删除自定义映像 |否 |是 |是 |
| 添加、更新和删除公式 |是 |是 |是 |
| Azure 应用商店映像白名单 |否 |是 |是 |
| **VM 任务** | | | |
| 创建 VM |是 |是 |是 |
| 启动、停止和删除 VM |用户仅创建 VM |是 |是 |
| 更新 VM 策略 |否 |是 |是 |
| 将数据磁盘添加到 VM 或从中删除 |用户仅创建 VM |是 |是 |
| **项目任务** | | | |
| 添加和删除项目存储库 |否 |是 |是 |
| 应用项目 |是 |是 |是 |

> [!NOTE]
> 用户创建 VM 时，将自动为该用户分配创建该 VM 的**所有者**角色。
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>在实验室级别添加所有者或用户
可以通过 Azure 门户在实验室级别添加所有者和用户。 这包括具有有效的 [Microsoft 帐户 (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account) 的外部用户。
以下步骤介绍在 Azure 开发测试实验室中将所有者或用户添加到实验室的过程：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表中，选择所需的实验室。
4. 在实验室的边栏选项卡上，选择“配置”。 
5. 在“配置”边栏选项卡上，选择“用户”。
6. 在“用户”边栏选项卡上选择“+ 添加”。
   
    ![添加用户](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
7. 在“选择角色”边栏选项卡上，选择所需的角色。 [每个角色可以执行的操作](#actions-that-can-be-performed-in-each-role)部分列出了所有者、开发测试用户和参与者三种角色的用户可以执行的各种操作。
8. 在“添加用户”边栏选项卡上，输入要添加到指定角色中的用户的电子邮件地址或名称。 如果找不到用户，则会显示一条错误消息来解释此问题。 如果找到该用户，则会列出并选择该用户。 
9. 选择“选择”。
10. 选择“确定”关闭“添加访问”边栏选项卡。
11. 返回到“用户”边栏选项卡时，已添加该用户。  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>使用 PowerShell 将外部用户添加到实验室
除了添加 Azure 门户中的用户，还可以使用 PowerShell 脚本将外部用户添加到实验室。 在下面的示例中，只需修改“要更改的值”注释下的参数值便可。
可以从 Azure 门户的实验室边栏选项卡中检索 `subscriptionId`、`labResourceGroup` 和 `labName` 值。

> [!NOTE]
> 示例脚本假定指定的用户已作为来宾添加到 Active Directory，如果不是这样，则会失败。 若要将不在 Active Directory 中的用户添加到实验室，请使用 Azure 门户将用户分配给角色，如[在实验室级别添加所有者或用户](#add-an-owner-or-user-at-the-lab-level)一节中所示。   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>在订阅级别添加所有者或用户
Azure 权限在 Azure 中从父范围传播到子范围。 因此，包含实验室的 Azure 订阅的所有者将自动成为这些实验室的所有者。 他们还拥有实验室用户创建的 VM 和其他资源以及 Azure 开发测试实验室服务。 

可以通过 [Azure门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)中的实验室边栏选项卡将其他所有者添加到实验室。 但是，添加的所有者的管理范围比订阅所有者的范围更窄。 例如，添加的所有者无法完全访问测试开发实验室服务在订阅中创建的某些资源。 

若要将所有者添加到 Azure 订阅，请按照下列步骤操作：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“订阅”。
3. 选择所需的订阅。
4. 选择“访问”图标。 
   
    ![访问用户](./media/devtest-lab-add-devtest-user/access-users.png)
5. 在“用户”边栏选项卡上选择“添加”。
   
    ![添加用户](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. 在“选择角色”边栏选项卡上，选择“所有者”。
7. 在“添加用户”边栏选项卡上，输入要作为所有者添加的用户的电子邮件地址或名称。 如果找不到用户，则会显示一条错误消息来解释此问题。 如果找到该用户，则会在“用户”文本框中列出该用户。
8. 选择找到的用户名。
9. 选择“选择”。
10. 选择“确定”关闭“添加访问”边栏选项卡。
11. 返回到“用户”边栏选项卡时，已添加该用户为所有者。 现在，该用户是此订阅下创建的所有实验室的所有者，因此能够执行所有者任务。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]




<!--HONumber=Nov16_HO3-->


