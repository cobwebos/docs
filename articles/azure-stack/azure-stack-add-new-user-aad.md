---
title: "在 Azure Active Directory 中添加新的 Azure 堆栈租户帐户 |Microsoft 文档"
description: "部署 Microsoft Azure 堆栈开发工具包之后, 你将需要创建至少一个租户用户帐户，以便您可以查看租户门户。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 590426563936c66b1353f769be138759bb53f58c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>在 Azure Active Directory 中添加新的 Azure 堆栈租户帐户
后[部署 Azure 堆栈开发工具包](azure-stack-run-powershell-script.md)，你将需要租户用户帐户，因此你可以浏览租户门户和测试你的产品/服务和计划。 你可以创建的租户帐户通过[使用 Azure 门户](#create-an-azure-stack-tenant-account-using-the-azure-portal)或[使用 PowerShell](#create-an-azure-stack-tenant-account-using-powershell)。

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>创建 Azure 堆栈租户帐户使用 Azure 门户
你必须具有一个 Azure 订阅来使用 Azure 门户。

1. 登录到[Azure](https://portal.azure.com)。
2. 在 Microsoft Azure 左侧的导航栏中，单击**Active Directory**。
3. 在目录列表中，单击你想要用于 Azure 堆栈的目录或创建一个新。
4. 在此目录页上，单击**用户**。
5. 单击“添加用户”。
6. 在**添加用户**向导，在**用户类型**列表中，选择**你的组织中的新用户**。
7. 在**用户名**框中，键入用户的名称。
8. 在 **@** 框中，选择适当的条目。
9. 单击“下一步”箭头。
10. 在**用户配置文件**页的向导中，键入一个**名字**，**姓氏**，和**显示名称**。
11. 在**角色**列表中，选择**用户**。
12. 单击“下一步”箭头。
13. 上**获取临时密码**页上，单击**创建**。
14. 复制**新密码**。
15. 使用新的帐户登录到 Microsoft Azure。 更改出现提示时的密码。
16. 登录到`https://portal.local.azurestack.external`与新的帐户，以查看租户门户。

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>创建使用 PowerShell 对 Azure 堆栈租户帐户
如果你没有 Azure 订阅，你无法使用 Azure 门户添加租户用户帐户。 在这种情况下，你可以改为使用 Azure PowerShell 的 Active Directory 模块的 Windows。

> [!NOTE]
> 如果使用 Microsoft 帐户 (Live ID) 来部署 Azure 堆栈开发工具包，你无法使用 AAD PowerShell 创建的租户帐户。 
> 
> 

1. 安装[Microsoft Online Services 登录助手面向 IT 专业人员 RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950)。
2. 安装[Azure Active Directory 的 Windows PowerShell 模块 （64 位版本）](http://go.microsoft.com/fwlink/p/?linkid=236297)并将其打开。
3. 运行以下 cmdlet:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. 使用新的帐户登录到 Microsoft Azure。 更改出现提示时的密码。
2. 登录到`https://portal.local.azurestack.external`与新的帐户，以查看租户门户。

