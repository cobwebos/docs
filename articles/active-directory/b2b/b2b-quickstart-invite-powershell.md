---
title: 使用 PowerShell 添加来宾用户的快速入门 - Azure Active Directory | Microsoft Docs
description: 本快速入门介绍如何使用 PowerShell 向外部 Azure AD B2B 协作用户发送邀请。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5325529acb66735db0c27c0f5bdfca819e8ec4d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812304"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>快速入门：使用 PowerShell 添加来宾用户

有很多种方法可用于邀请外部合作伙伴通过 Azure Active Directory B2B 协作访问你的应用和服务。 在上个快速入门中，你已了解如何在 Azure Active Directory 管理门户中直接添加来宾用户。 此外还可以使用 PowerShell 添加来宾用户，可以选择一次添加一个，也可以选择批量添加。 在本快速入门中，你将使用 New-AzureADMSInvitation 命令将一个来宾用户添加到 Azure 租户。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>先决条件

### <a name="install-the-latest-azureadpreview-module"></a>安装最新的 AzureADPreview 模块
确保已安装最新版本的 Azure AD PowerShell for Graph 模块 (AzureADPreview)。 

首先，检查已安装了哪些模块。 以已提升的用户身份打开 Windows PowerShell（以管理员身份运行），然后运行以下命令：
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

如果显示了 AzureADPreview 模块，但没有任何指示还有更高版本的消息，请进行设置。 否则，请根据输出，执行以下操作之一：

- 如果未返回任何结果，请运行以下命令来安装 AzureADPreview 模块：
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- 如果结果中仅显示了 AzureAD 模块，请运行以下命令来安装 AzureADPreview 模块： 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- 如果结果中仅显示了 AzureADPreview 模块，但收到消息指出存在更新的版本，请运行以下命令来更新此模块： 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

你可能会收到正在从不受信任的存储库安装模块的提示。 如果之前未将 PSGallery 存储库设置为受信任的存储库，则可能出现此情况。 按 Y 以安装模块。

### <a name="get-a-test-email-account"></a>获取测试电子邮件帐户

需要可向其发送邀请的测试电子邮件帐户。 这些帐户必须来自组织外部。 可使用任意类型的帐户，包括 gmail.com 或 outlook.com 地址等社交帐户。

## <a name="sign-in-to-your-tenant"></a>登录租户

运行以下命令以连接到租户域：

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
例如，`Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`。

在系统提示时输入凭据。

## <a name="send-an-invitation"></a>发送邀请

1. 要向测试电子邮件帐户发送邀请，请运行以下 PowerShell 命令（将“Sanda”和“sanda\@fabrikam.com”替换为你的测试电子邮件帐户名和电子邮件地址）： 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.azure.com -SendInvitationMessage $true
   ```
2. 该命令向指定的电子邮件地址发送邀请。 查看输出，输出应如下所示：

   ![显示“等待用户接受”的 PowerShell 输出](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>验证用户是否存在于目录中

1. 要验证邀请的用户是否已添加到 Azure AD，请运行以下命令：
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. 查看输出，确保已列出受邀用户，其中用户主体名称 (UPN) 采用 emailaddress#EXT#\@domain 的格式。 例如，sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com，其中 contoso.onmicrosoft.com 是你从其发送邀请的组织。

   ![显示已添加的来宾用户的 PowerShell 输出](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>清理资源

目录中不再需要测试用户帐户时，请将其删除。 运行以下命令来删除用户帐户：

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
例如： `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>后续步骤
在本快速入门中，你已邀请一个来宾用户并使用 PowerShell 将其添加到目录。 接下来，了解如何使用 PowerShell 批量邀请来宾用户。

> [!div class="nextstepaction"]
> [教程：批量邀请 Azure AD B2B 协作用户](tutorial-bulk-invite.md)
