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
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7936f47007285e3f7fa1d3220efa022a6e3881ca
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>在无需最终用户注册的情况下部署密码重置

部署自助密码重置 (SSPR) 要求身份验证数据必须存在。 某些组织让其用户自己输入其身份验证数据，但多数组织倾向于与 Active Directory 中的现有数据进行同步。 如果本地目录中有正确设置格式的数据，并[使用快速设置配置了 Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)，则无需用户交互，就会向 Azure AD 和 SSPR 提供数据。

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

如果提供了“移动电话”或“备用电子邮件”的值，用户可以立即使用这些值重置密码，即使他们尚未注册该服务。 此外，用户在首次注册时会看到这些值，并可随意进行修改。 成功注册之后，这些值将分别保存在“身份验证电话”和“身份验证电子邮件”字段中。

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

* [如何完成 SSPR 成功推出？](active-directory-passwords-best-practices.md)
* [重置或更改密码](active-directory-passwords-update-your-own-password.md)。
* [注册自助服务密码重置](active-directory-passwords-reset-register.md)。
* [是否有许可问题？](active-directory-passwords-licensing.md)
* [哪些身份验证方法可供用户使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR 有哪些策略选项？](active-directory-passwords-policy.md)
* [什么是密码写回？我为什么关心它？](active-directory-passwords-writeback.md)
* [如何报告 SSPR 中的活动？](active-directory-passwords-reporting.md)
* [SSPR 中的所有选项是什么？它们有哪些含义？](active-directory-passwords-how-it-works.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)
