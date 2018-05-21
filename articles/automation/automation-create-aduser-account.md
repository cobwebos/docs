---
title: 创建 Azure AD 用户帐户
description: 本文介绍如何为 Azure 自动化中的 Runbook 创建 Azure AD 用户帐户凭据，以便在 Azure 中进行身份验证。
keywords: azure active directory 用户, azure 服务管理, azure ad 用户帐户
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 105dcf5564ec5c1d0b3528e9b5667d89d98c6cab
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>使用 Azure 经典部署和 Resource Manager 部署对 Runbook 进行身份验证
本文介绍在为针对 Azure 经典部署模型或 Azure 资源管理器资源运行的 Azure 自动化 Runbook 配置 Azure AD 用户帐户时所要执行的步骤。 尽管这仍是基于 Azure 资源管理器的 Runbook 支持的身份验证标识，但建议的方法是使用 Azure 运行方式帐户。       

## <a name="create-a-new-azure-active-directory-user"></a>创建新的 Azure Active Directory 用户
1. 以要管理的 Azure 订阅的服务管理员身份登录到 Azure 门户。
2. 选择“Azure Active Directory” > “用户和组” > “所有用户” > “新建用户”。
3. 输入用户的详细信息，如**名称**和**用户名**。  
4. 记下该用户的完整名称和临时密码。
5. 选择“目录角色”。
6. 分配“全局管理员”或“受限管理员”角色。
7. 从 Azure 注销，并使用刚创建的帐户重新登录。系统会提示更改用户密码。

## <a name="create-an-automation-account-in-the-azure-portal"></a>在 Azure 门户中创建自动化帐户
在本部分中，将执行以下步骤以在 Azure 门户中创建一个 Azure 自动化帐户，该帐户用于在 Azure 资源管理器模式下管理资源的 Runbook。  

1. 以要管理的 Azure 订阅的服务管理员身份登录到 Azure 门户。
2. 选择“自动化帐户”。
3. 选择 **添加** 。<br><br>![添加自动化帐户](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. 在“添加自动化帐户”边栏选项卡的“名称”框中，键入新自动化帐户的名称。
5. 如果有多个订阅，请为新帐户指定一个订阅，并指定一个新的或现有的**资源组**以及 Azure 数据中心**位置**。
6. 针对“创建 Azure 运行方式帐户”选项选择“是”值，并单击“创建”按钮。  
   
    > [!NOTE]
    > 如果通过选择“否”选项来选择不创建运行方式帐户，则“添加自动化帐户”边栏选项卡中会出现一条警告消息。 尽管这会创建帐户并将其分配到订阅中的“参与者”角色，但该帐户在订阅目录服务中没有相应的身份验证标识，因此无法访问订阅中的资源。 这会导致引用此帐户的任何 Runbook 无法进行身份验证并针对 Azure 资源管理器资源执行任务。
    > 
    >

    <br>![添加自动化帐户警报](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. 在 Azure 创建自动化帐户时，可以在菜单的“通知”下面跟踪进度。

完成创建凭据后，需要创建一个凭据资产，以便会自动化帐户与前面创建的 AD 用户帐户相关联。 请记住，只创建了自动化帐户，但它并未与身份验证标识相关联。 执行 [Credential assets in Azure Automation](automation-credentials.md#creating-a-new-credential-asset)（Azure 自动化中的凭据资产）一文中所述的步骤，并以**域\用户**格式输入“用户名”的值。

## <a name="use-the-credential-in-a-runbook"></a>在 Runbook 中使用凭据
可以使用 [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) 活动检索 Runbook 中的凭据，然后将它与 [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) 结合使用以连接到 Azure 订阅。 如果该凭据是多个 Azure 订阅的管理员，还应使用 [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) 指定正确的订阅。 此命令显示在以下 PowerShell 示例中（通常会出现在大多数 Azure 自动化 runbook 的顶部）。

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

在 Runbook 中任何[检查点](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints)的后面重复这些行。 如果 Runbook 已暂停，然后在另一个工作线程上恢复，则它需要再次执行身份验证。

## <a name="next-steps"></a>后续步骤
* 查看 [Azure Automation runbook types](automation-runbook-types.md)（Azure 自动化 Runbook 类型）一文中所述的不同 Runbook 类型和创建自己的 Runbook 所需的步骤

