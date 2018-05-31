---
title: Azure AD SSPR 数据要求 | Microsoft Docs
description: Azure AD 自助密码重置的数据要求，以及如何满足这些要求
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5409bf198d0e3f6537619ef4698d9f2e31bd27c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257581"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>在无需最终用户注册的情况下部署密码重置

若要部署 Azure Active Directory (Azure AD) 自助密码重置 (SSPR)，身份验证数据必须存在。 某些组织让其用户输入其身份验证数据本身。 但是，许多组织倾向于与 Active Directory 中已存在的数据同步。 在以下情况下，无需用户交互已同步的数据就会提供给 Azure AD 和 SSPR：
   * 在本地目录中正确设置数据的格式。
   * [使用快速设置配置 Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md)。

若要正常工作，电话号码必须采用“+国家/地区代码 电话号码”格式，例如，+1 4255551234。

> [!NOTE]
> 在国家/地区代码和电话号码之间需要有一个空格。
>
> 密码重置不支持电话分机。 即使采用“+1 4255551234X12345”格式，在拨出电话前也会删除分机。

## <a name="fields-populated"></a>填充的字段

如果在 Azure AD Connect 中使用默认设置，将进行以下映射：

| 本地 Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | 办公电话 |
| mobile | 移动电话 |

用户验证其移动电话号码后，Azure AD 中身份验证联系人信息下的“电话”字段也将填充该号码。

## <a name="authentication-contact-info"></a>身份验证联系人信息

全局管理员可以手动为用户设置身份验证联系人信息，如以下屏幕截图中所示。

![联系人][Contact]

如果“电话”字段已填充且在 SSPR 策略中启用了“移动电话”，则用户将在密码重置注册页和密码重置工作流中看到该号码。 

“备用电话”字段不用于密码重置。

如果“电子邮件”字段已填充且在 SSPR 策略中启用了“电子邮件”，则用户将在密码重置注册页和密码重置工作流中看到该电子邮件。

如果“备用电子邮件”字段已填充且在 SSPR 策略中启用了“电子邮件”，则用户将**不**会在密码重置注册页上看到该电子邮件，但会将在密码重置工作流中看到该电子邮件。 


## <a name="security-questions-and-answers"></a>安全问题和答案

安全问题和答案安全地存储在 Azure AD 租户中，用户仅可通过 [SSPR 注册门户](https://aka.ms/ssprsetup)进行访问。 管理员无法查看或修改其他用户问题和答案的内容。

### <a name="what-happens-when-a-user-registers"></a>当用户注册时会发生什么情况？

当用户注册时，注册页面设置以下字段：

* **身份验证电话**
* **身份验证电子邮件**
* **安全问题和答案**

如果提供了“移动电话”或“备用电子邮件”的值，用户可以立即使用这些值重置密码，即使他们尚未注册该服务。 此外，用户在首次注册时会看到这些值，并可随意进行修改。 成功注册之后，这些值将分别保存在“身份验证电话”和“身份验证电子邮件”字段中。

## <a name="set-and-read-the-authentication-data-through-powershell"></a>通过 PowerShell 设置和读取身份验证数据

可以通过 PowerShell 设置以下字段：

* **备用电子邮件**
* **移动电话**
* **办公电话**：仅当未与本地目录同步时，才能设置

### <a name="use-powershell-version-1"></a>使用 PowerShell 版本 1

若要开始，需要[下载并安装 Azure AD PowerShell 模块](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)。 安装后，可以按照以下步骤配置每个字段。

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 版本 1 设置身份验证数据

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 版本 1 读取身份验证数据

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>读取“身份验证电话”和“身份验证电子邮件”选项

若要在使用 PowerShell 版本 1 时读取**身份验证电话**和**身份验证电子邮件**，请使用以下命令：

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>使用 PowerShell 版本 2

若要开始，需要[下载并安装 Azure AD 版本 2 PowerShell 模块](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0)。 安装后，可以按照以下步骤配置每个字段。

若要从支持安装模块的最新版本 PowerShell 快速安装，请运行以下命令。 （第一行检查是否已安装该模块。）

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 版本 2 设置身份验证数据

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 版本 2 读取身份验证数据

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>后续步骤

* [如何成功推出 SSPR？](howto-sspr-deployment.md)
* [重置或更改密码](../active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](../active-directory-passwords-reset-register.md)
* [是否有许可问题？](concept-sspr-licensing.md)
* [哪些身份验证方法可供用户使用？](concept-sspr-howitworks.md#authentication-methods)
* [SSPR 有哪些策略选项？](concept-sspr-policy.md)
* [什么是密码写回？我为什么关心它？](howto-sspr-writeback.md)
* [如何报告 SSPR 中的活动？](howto-sspr-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](concept-sspr-howitworks.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "全局管理员可以修改用户的身份验证联系人信息"
