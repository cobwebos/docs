---
title: "Azure AD SSPR 数据要求 | Microsoft Docs"
description: "Azure AD 自助密码重置的数据要求，以及如何满足这些要求"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: f7ecb22ee46d83867453e035f8a639bc7f7f2d81
ms.contentlocale: zh-cn
ms.lasthandoff: 05/08/2017

---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>在无需最终用户注册的情况下部署密码重置

部署自助密码重置 (SSPR) 要求身份验证数据必须存在。 某些组织让其用户自己输入其身份验证数据，但多数组织倾向于与 Active Directory 中的现有数据进行同步。 如果你的本地目录中有正确设置格式的数据，并[使用快速设置配置了 Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)，则无需用户交互，就会向 Azure AD 和 SSPR 提供数据。

任何电话号码必须采用“+国家/地区代码 电话号码”的格式（示例：+1 4255551234），才能正常工作。

> [!NOTE]
> 密码重置不支持电话分机。 即使采用“+1 4255551234X12345”格式，在拨出电话前也会删除分机。

## <a name="fields-populated"></a>填充的字段

如果在 Azure AD Connect 中使用默认设置，将进行以下映射。

| 本地 AD | Azure AD | Azure AD 身份验证联系信息 |
| --- | --- | --- |
| telephoneNumber | 办公电话 | 备用号码 |
| mobile | 移动电话 | 电话 |


## <a name="security-questions-and-answers"></a>安全问题和答案

安全问题和答案安全地存储在 Azure AD 租户中，用户仅可通过 [SSPR 注册门户](https://aka.ms/ssprsetup)进行访问。 管理员无法查看或修改其他用户问题和答案的内容。

### <a name="what-happens-when-a-user-registers"></a>当用户注册时会发生什么情况？

当用户注册时，注册页面设置以下字段：

* 身份验证电话
* 身份验证电子邮件
* 安全问答

如果你提供了“移动电话”或“备用电子邮件”的值，用户可以立即使用这些值重置密码，即使他们尚未注册该服务。 此外，用户在首次注册时将看到这些值，并可随意进行修改。 成功注册之后，这些值将分别保存在“身份验证电话”和“身份验证电子邮件”字段中。

## <a name="set-and-read-authentication-data-using-powershell"></a>使用 PowerShell 设置和读取身份验证数据

可以使用 PowerShell 设置以下字段

* 备用电子邮件
* 移动电话
* 办公电话 - 仅当未与本地目录同步时，才能设置

### <a name="using-powershell-v1"></a>使用 PowerShell V1

若要开始，需要[下载并安装 Azure AD PowerShell 模块](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)。 安装后，可以按照以下步骤配置每个字段。

#### <a name="set-authentication-data-with-powershell-v1"></a>使用 PowerShell V1 设置身份验证数据

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>使用 PowerShell V1 读取身份验证数据

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>身份验证电话和身份验证电子邮件只能通过 Powershell V1 使用以下命令进行读取

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>使用 PowerShell V2

若要开始，需要[下载并安装 Azure AD V2 PowerShell 模块](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md)。 安装后，可以按照以下步骤配置每个字段。

若要通过支持 Install-Module 的最新版本的 PowerShell 快速安装，请运行以下命令（第一行只是检查是否已安装该模块）：

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>使用 PowerShell V2 设置身份验证数据

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>使用 PowerShell V2 读取身份验证数据

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>后续步骤

以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助服务密码管理 
* [**授权**](active-directory-passwords-licensing.md) - 配置 Azure AD 授权
* [**推出**](active-directory-passwords-best-practices.md) - 使用此处提供的指南计划 SSPR 并将其部署到用户
* [**自定义**](active-directory-passwords-customize.md) - 自定义公司的 SSPR 体验的外观。
* [**策略**](active-directory-passwords-policy.md) - 了解并设置 Azure AD 密码策略
* [**报告**](active-directory-passwords-reporting.md) - 了解用户是否访问 SSPR 功能，以及在何时何处进行访问
* [**深入技术探究**](active-directory-passwords-how-it-works.md) - 了解幕后的工作原理
* [**常见问题**](active-directory-passwords-faq.md) - 如何？ 为什么？ 什么？ 何处？ 谁？ 何时？ - 常见问题的答案
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题

