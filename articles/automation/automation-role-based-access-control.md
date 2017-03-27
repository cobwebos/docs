---
title: "Azure 自动化中基于角色的访问控制 | Microsoft Docs"
description: "基于角色的访问控制 (RBAC) 可用于对 Azure 资源进行访问管理。 本文介绍如何设置 Azure 自动化中的 RBAC。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: "自动化 rbac, 基于角色的访问控制, azure rbac"
ms.assetid: 04b5625e-0ee8-4b5b-85cd-7734c1b3d4a3
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;sngun
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 17c7e410a9c5b69ab450eb3affd192f1e3cb6e76


---
# <a name="role-based-access-control-in-azure-automation"></a>Azure 自动化中基于角色的访问控制
## <a name="role-based-access-control"></a>基于角色的访问控制
基于角色的访问控制 (RBAC) 可用于对 Azure 资源进行访问管理。 使用 [RBAC](../active-directory/role-based-access-control-configure.md)，可在团队中对职责进行分配，仅授予执行作业所需的对用户、组和应用程序的适当访问权限。 可以使用 Azure 门户、Azure 命令行工具或 Azure 管理 API 将基于角色的访问权限授予用户。

## <a name="rbac-in-automation-accounts"></a>自动化帐户中的 RBAC
在 Azure 自动化中，访问权限是通过将相应的 RBAC 角色分配给自动化帐户作用域的用户、组和应用程序来授予的。 以下是自动化帐户所支持的内置角色：  

| **角色** | **说明** |
|:--- |:--- |
| 所有者 |“所有者”角色允许访问自动化帐户中的所有资源和操作，包括访问其他用户、组和应用程序以管理自动化帐户。 |
| 参与者 |“参与者”角色允许你管理所有事项，修改其他用户对自动化帐户的访问权限除外。 |
| 读取器 |“读者”角色允许你查看自动化帐户中的所有资源，但不能进行任何更改。 |
| 自动化运算符 |“自动化操作员”角色允许你执行作业的启动、停止、暂停、恢复和计划等操作任务。 如果你想要防止他人查看或修改你的自动化帐户资源（例如凭据资产和 Runbook），但仍允许所在组织的成员执行这些 Runbook，则可使用此角色。 |
| 用户访问管理员 |“用户访问管理员”角色允许你管理用户对 Azure 自动化帐户的访问。 |

> [!NOTE]
> 你不能授予对特定 Runbook 的访问权限，而只能授予对自动化帐户中的资源和操作的访问权限。  
> 
> 

在本文中，我们将指导你在 Azure 自动化中设置 RBAC。 不过，首先让我们仔细地看一下授予参与者、读者、自动化操作员和用户访问管理员的各个权限，以便我们在授予任何人对自动化帐户的权限之前更好地理解这些权限。  否则，它可能导致意外或不良后果。     

## <a name="contributor-role-permissions"></a>“参与者”角色权限
下表列出了可以由自动化中的“参与者”角色执行的特定操作。

| **资源类型** | **读取** | **写入** | **删除** | **其他操作** |
|:--- |:--- |:--- |:--- |:--- |
| Azure 自动化帐户 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| 自动化证书资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| 自动化连接资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| 自动化连接类型资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| 自动化凭据资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| 自动化计划资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| 自动化变量资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| 自动化所需状态配置 | | | |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |
| 混合 Runbook 辅助角色资源类型 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure 自动化作业 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |
| 自动化作业流 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化作业计划 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| 自动化模块 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure 自动化 Runbook |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |
| 自动化 Runbook 草稿 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |
| 自动化 Runbook 草稿测试作业 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |
| 自动化 Webhook |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |

## <a name="reader-role-permissions"></a>“读者”角色权限
下表列出了可以由自动化中的“读者”角色执行的特定操作。

| **资源类型** | **读取** | **写入** | **删除** | **其他操作** |
|:--- |:--- |:--- |:--- |:--- |
| 经典订阅管理员 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 管理锁 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 权限 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 提供程序操作 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 角色分配 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 角色定义 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="automation-operator-role-permissions"></a>“自动化操作员”角色权限
下表列出了可以由自动化中的“自动化操作员”角色执行的特定操作。

| **资源类型** | **读取** | **写入** | **删除** | **其他操作** |
|:--- |:--- |:--- |:--- |:--- |
| Azure 自动化帐户 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化证书资产 | | | | |
| 自动化连接资产 | | | | |
| 自动化连接类型资产 | | | | |
| 自动化凭据资产 | | | | |
| 自动化计划资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | |
| 自动化变量资产 | | | | |
| 自动化所需状态配置 | | | | |
| 混合 Runbook 辅助角色资源类型 | | | | |
| Azure 自动化作业 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |
| 自动化作业流 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化作业计划 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | |
| 自动化模块 | | | | |
| Azure 自动化 Runbook |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化 Runbook 草稿 | | | | |
| 自动化 Runbook 草稿测试作业 | | | | |
| 自动化 Webhook | | | | |

有关更多详细信息， [自动化操作员操作](../active-directory/role-based-access-built-in-roles.md#automation-operator) 列出了自动化操作员角色所支持的针对自动化帐户及其资源的操作。

## <a name="user-access-administrator-role-permissions"></a>“用户访问管理员”角色权限
下表列出了可以由自动化中的“用户访问管理员”角色执行的特定操作。

| **资源类型** | **读取** | **写入** | **删除** | **其他操作** |
|:--- |:--- |:--- |:--- |:--- |
| Azure 自动化帐户 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化证书资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化连接资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化连接类型资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化凭据资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化计划资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化变量资产 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化所需状态配置 | | | | |
| 混合 Runbook 辅助角色资源类型 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure 自动化作业 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化作业流 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化作业计划 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化模块 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure 自动化 Runbook |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化 Runbook 草稿 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化 Runbook 草稿测试作业 |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 自动化 Webhook |![绿色状态](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>使用 Azure 门户为自动化帐户配置 RBAC
1. 登录到 [Azure 门户](https://portal.azure.com/) ，然后从“自动化帐户”边栏选项卡中打开你的自动化帐户。  
2. 单击右上角的“访问”控件  。 此时会打开“用户”边栏选项卡，你可以在其中添加新的用户、组和应用程序，以便管理你的自动化帐户并查看可以为自动化帐户配置的现有角色  。  
   
   ![访问按钮](media/automation-role-based-access-control/automation-01-access-button.png)  

> [!NOTE]
> **订阅管理员** 已作为默认用户存在。 订阅管理员 Active Directory 组包括 Azure 订阅的服务管理员和共同管理员。 服务管理员是 Azure 订阅及其资源的所有者，并且还会让自动化帐户的所有者角色继承下去。 这意味着，访问权限对于订阅的**服务管理员和共同管理员**是**继承的**，而对于所有其他用户是**分配的**。 单击“订阅管理员”可查看有关其权限的更多详细信息  。  
> 
> 

### <a name="add-a-new-user-and-assign-a-role"></a>添加新用户并分配角色
1. 在“用户”边栏选项卡中，单击“添加”打开“添加访问权限”边栏选项卡，以便添加用户、组或应用程序并向其分配角色。  
   
   ![添加用户](media/automation-role-based-access-control/automation-02-add-user.png)  
2. 从可用角色列表中选择一个角色。 我们将选择“读者”角色，但你可以选择自动化帐户所支持的任何可用的内置角色，或者你所定义的任何自定义角色  。  
   
   ![选择角色](media/automation-role-based-access-control/automation-03-select-role.png)  
3. 单击“添加用户”打开“添加用户”边栏选项卡。 如果你已经添加过管理订阅的用户、组或应用程序，系统会列出这些用户，你可以选择他们来添加访问权限。 如果没有列出任何用户，或者没有列出你想要添加的用户，请单击“邀请”打开“邀请来宾”边栏选项卡，以便邀请具有有效 Microsoft 帐户电子邮件地址（例如 Outlook.com、OneDrive 或 Xbox Live ID）的用户。 输入用户的电子邮件地址以后，请单击“选择”以添加用户，然后单击“确定”。 
   
   ![添加用户](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   现在，你会看到添加到“用户”边栏选项卡且被分配了“读者”角色的用户。  
   
   ![列出用户](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   你也可以通过“角色”边栏选项卡向用户分配角色  。 
4. 单击“用户”边栏选项卡中的“角色”打开“角色”边栏选项卡。 在该边栏选项卡中，你可以查看角色的名称以及分配给该角色的用户和组的数目。
   
    ![从用户边栏选项卡分配角色](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > 基于角色的访问控制只能在自动帐户级别设置，不能在自动帐户下的任何资源上设置。
   > 
   > 
   
    可以将多个角色分配给用户、组或应用程序。 例如，如果将“自动化操作员”角色和“读者”角色一起添加到用户，用户就可以查看所有自动化资源并执行 Runbook 作业。 你可以展开下拉列表，以便查看分配给用户的角色的列表。  
   
    ![查看多个角色](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  

### <a name="remove-a-user"></a>删除用户
你可以删除不管理自动化帐户或不再为组织工作的用户的访问权限。 下面是删除用户的步骤： 

1. 在“用户”边栏选项卡中，选择要删除的角色分配  。
2. 单击“分配详细信息”边栏选项卡中的“删除”按钮  。
3. 单击“是”以确认删除  。 
   
   ![删除用户](media/automation-role-based-access-control/automation-08-remove-users.png)  

## <a name="role-assigned-user"></a>被分配了角色的用户
已分配角色的用户登录到自动化帐户时，他们现在可以查看“默认目录” 列表中列出的所有者的帐户。 为了查看被添加到的自动化帐户，该用户必须将默认目录切换成所有者的默认目录。  

![默认目录](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### <a name="user-experience-for-automation-operator-role"></a>自动化操作员角色的用户体验
已分配“自动化操作员”角色的用户在查看被分配到的自动化帐户时，只能查看在自动化帐户中创建的 Runbook、Runbook 作业和计划的列表，而不能查看其定义。 该用户可以启动、停止、暂停、恢复或计划 Runbook 作业。 该用户将无法访问其他自动化资源，例如配置、混合辅助角色组或 DSC 节点。  

![不能访问资源](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

当用户单击 Runbook 时，系统不会提供查看源或编辑 Runbook 所需的命令，因为自动化操作员角色不允许访问它们。  

![无权编辑 runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

用户将具有查看和创建计划的访问权限，但没有任何其他资产类型的访问权限。  

![不能访问资产](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

该用户也无权查看与 Runbook 关联的 webhook

![不能访问 webhook](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>使用 Azure PowerShell 为自动化帐户配置 RBAC
还可以使用以下 [Azure PowerShell cmdlet](../active-directory/role-based-access-control-manage-access-powershell.md)为自动化帐户配置基于角色的访问权限。

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) 列出 Azure Active Directory 中提供的所有 RBAC 角色。 你可以使用此命令和 **Name** 属性来列出特定角色可以执行的所有操作。  
    **示例：**  
    ![获取角色定义](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) 列出指定作用域的 Azure AD RBAC 角色分配。 在没有任何参数的情况下，此命令返回在订阅下进行的所有角色分配。 使用 **ExpandPrincipalGroups** 参数可列出针对指定用户和该用户所在组的访问权限分配。  
    **示例：**使用以下命令列出自动化帐户中的所有用户及其角色。

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![获取角色分配](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) 为特定作用域的用户、组和应用程序分配访问权限。  
    **示例：**使用以下命令为“自动化帐户”作用域中的用户分配“自动化操作员”角色。

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![新建角色分配](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• 使用 [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) 从特定作用域中删除指定用户、组或应用程序的访问权限。  
    **示例：**使用以下命令从“自动化帐户”作用域的“自动化操作员”角色中删除用户。

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

在上述示例中，请将**登录 ID**、**订阅 ID**、**资源组名称**和**自动化帐户名称**替换为你的帐户详细信息。 出现提示时选择“是”  以在继续删除用户角色分配前确认。   

## <a name="next-steps"></a>后续步骤
* 有关为 Azure 自动化配置 RBAC 的不同方式的信息，请参阅 [使用 Azure PowerShell 管理 RBAC](../active-directory/role-based-access-control-manage-access-powershell.md)。
* 有关以不同方式启动 Runbook 的详细信息，请参阅 [启动 Runbook](automation-starting-a-runbook.md)
* 有关不同 Runbook 类型的信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)




<!--HONumber=Dec16_HO2-->


