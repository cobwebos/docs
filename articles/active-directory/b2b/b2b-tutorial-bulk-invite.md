---
title: 有关批量邀请 B2B 协作用户的教程 - Azure Active Directory | Microsoft Docs
description: 在本教程中，你将学习如何使用 PowerShell 和 CSV 文件向外部 Azure AD B2B 协作用户批量发送邀请。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.openlocfilehash: e40eb5c1727317b375d45da83cd712cf0b7f963c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097769"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>教程：批量邀请 Azure AD B2B 协作用户

如果使用 Azure Active Directory (Azure AD) B2B 协作功能与外部合作伙伴进行协作，可同时邀请多名来宾用户加入你的组织。 在本教程中，你将学习如何使用 PowerShell 向外部用户批量发送邀请。 具体操作如下：

> [!div class="checklist"]
> * 准备一个具有用户信息的逗号分隔值 (.csv) 文件
> * 运行 PowerShell 脚本以发送邀请
> * 验证是否已将用户添加到目录中

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>先决条件

### <a name="install-the-latest-azureadpreview-module"></a>安装最新的 AzureADPreview 模块
确保已安装最新版本的 Azure AD PowerShell for Graph 模块 (AzureADPreview)。 

首先，检查已安装了哪些模块。 以已提升的用户身份打开 Windows PowerShell（以管理员身份运行），然后运行以下命令：
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

根据输出，执行以下操作之一：

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

可能会收到提示，其要求你从不受信任的存储库安装模块。 如果之前未将 PSGallery 存储库设置为受信任的存储库，则可能出现此情况。 按 Y 以安装模块。

### <a name="get-test-email-accounts"></a>获取测试电子邮件帐户

需要两个或更多可向其发送邀请的测试电子邮件帐户。 这些帐户必须来自组织外部。 可使用任意类型的帐户，包括 gmail.com 或 outlook.com 地址等社交帐户。

## <a name="prepare-the-csv-file"></a>准备 CSV 文件

在 Microsoft Excel 中，使用受邀用户的姓名和电子邮件地址列表创建一个 CSV 文件。 请确保包含“Name”和“InvitedUserEmailAddress”列标题。 

例如，按以下格式创建一个工作表：


![显示“等待用户接受”的 PowerShell 输出](media/tutorial-bulk-invite/AddUsersExcel.png)

将此文件保存为 C:\BulkInvite\Invitations.csv。 

如果没有 Excel，可在任意文本编辑器（例如记事本）中创建 CSV 文件。 以逗号分隔每个值，以换行符分隔每一行。 

## <a name="sign-in-to-your-tenant"></a>登录租户

运行以下命令以连接到租户域：

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
例如，`Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`。

在系统提示时输入凭据。

## <a name="send-bulk-invitations"></a>批量发送邀请

要发送邀请，请运行以下 PowerShell 脚本（其中 c:\bulkinvite\invitations.csv 是 CSV 文件的路径）： 

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv
   
$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   
$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."
   
foreach ($email in $invitations) 
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.azure.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```
此脚本会向 Invitations.csv 文件中的电子邮件地址发送邀请。 对于每位用户，应会看到与下面类似的输出：

![显示“等待用户接受”的 PowerShell 输出](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>验证目录中是否存在用户

要验证是否已向 Azure AD 添加受邀用户，请运行以下命令：
```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```
应会看到已列出受邀用户，其中用户主体名称 (UPN) 采用 emailaddress#EXT#@domain 的格式。 例如，lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com，其中 contoso.onmicrosoft.com 是从中发送邀请的组织。

## <a name="clean-up-resources"></a>清理资源

目录中不再需要测试用户帐户时，请将其删除。 运行以下命令来删除用户帐户：

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
例如： `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>后续步骤
在本教程中，你向组织外部的来宾用户批量发送了邀请。 接下来，将学习如何执行邀请兑换过程。

> [!div class="nextstepaction"]
> [了解 Azure AD B2B 协作邀请兑换过程](redemption-experience.md)

