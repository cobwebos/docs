---
title: 预填充联系信息以获取自助服务密码重置-Azure Active Directory
description: 了解如何为 Azure Active Directory 自助服务密码重置 (SSPR) 的用户预填充联系信息，以便他们可以使用该功能，而无需完成注册过程。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22037468c47de45513351e1d6126d3f3e5fccadc
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717789"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>预先填充用户身份验证联系信息以 Azure Active Directory 自助服务密码重置 (SSPR) 

若要使用 Azure Active Directory (Azure AD) 自助服务密码重置 (SSPR) ，用户的身份验证联系信息必须存在。 某些组织让用户自行注册其身份验证数据。 其他组织倾向于从已存在于 Active Directory 域服务 (AD DS) 中的身份验证数据进行同步。 此同步数据可用于 Azure AD 和 SSPR，无需用户交互。 用户需要更改或重置其密码时，即使尚未注册其联系信息，也可以这样做。

如果满足以下要求，可以预先填充身份验证联系信息：

* 已正确格式化本地目录中的数据。
* 已为 Azure AD 租户配置 [Azure AD Connect](../hybrid/how-to-connect-install-express.md) 。

电话号码的格式必须为 *+ CountryCode PhoneNumber*，如 *+ 1 4251234567*。

> [!NOTE]
> 国家/地区代码和电话号码之间必须有一个空格。
>
> 密码重置不支持电话分机。 即使采用“1 4251234567X12345”格式，在拨出电话前也会删除分机。

## <a name="fields-populated"></a>填充的字段

如果在 Azure AD Connect 中使用默认设置，则会进行以下映射，以填充 SSPR 的身份验证联系信息：

| 本地 Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | 办公电话 |
| mobile                       | 移动电话 |

用户验证其移动电话号码后，Azure AD 中的“身份验证联系人信息”下的“电话”字段也将填充该号码。******

## <a name="authentication-contact-info"></a>身份验证联系人信息

在 Azure 门户中 Azure AD 用户的“身份验证方法”**** 页上，全局管理员可以手动设置身份验证联系人信息，如以下示例屏幕截图所示：

![Azure AD 中用户的身份验证联系信息][Contact]

以下注意事项适用于此身份验证联系人信息：

* 如果“电话”字段已填充且在 SSPR 策略中启用了“移动电话”，则用户会在密码重置注册页和密码重置工作流中看到该号码。****
* “备用电话”字段不用于密码重置。**
* 如果“电子邮件”字段已填充且在 SSPR 策略中启用了“电子邮件”，则用户会在密码重置注册页和密码重置工作流中看到该电子邮件。****
* 如果“电子邮件”字段已填充且在 SSPR 策略中启用了“电子邮件”，则用户不会在密码重置注册页中看到该电子邮件，但会在密码重置工作流中看到它。****

## <a name="security-questions-and-answers"></a>安全问题和答案

安全问题和答案安全地存储在 Azure AD 租户中，用户仅可通过 [SSPR 注册门户](https://aka.ms/ssprsetup)进行访问。 管理员无法查看、设置或修改其他用户问题和答案的内容。

## <a name="what-happens-when-a-user-registers"></a>当用户注册时会发生什么情况？

当用户注册时，注册页面设置以下字段：

* **身份验证电话**
* **身份验证电子邮件**
* **安全问答**

如果提供了 *移动电话* 或 *备用电子邮件*的值，用户可以立即使用这些值重置密码，即使他们尚未注册该服务。

用户在首次注册时会看到这些值，如果需要，还可以修改它们。 成功注册后，这些值将分别保存在 " *身份验证电话* " 和 " *身份验证电子邮件* " 字段中。

## <a name="set-and-read-the-authentication-data-through-powershell"></a>通过 PowerShell 设置和读取身份验证数据

可以通过 PowerShell 设置以下字段：

* *备用电子邮件*
* *移动电话*
* *办公电话*
    * 仅当未与本地目录同步时，才能设置。

> [!IMPORTANT]
> PowerShell v1 和 PowerShell v2 之间的命令功能缺少奇偶校验。 [身份验证方法的 Microsoft Graph REST API (beta) ](/graph/api/resources/authenticationmethods-overview)是当前工程设计的重点，旨在提供新式交互。

### <a name="use-powershell-version-1"></a>使用 PowerShell 版本 1

首先，[下载并安装 Azure AD PowerShell 模块](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule)。 安装完成后，使用以下步骤配置每个字段。

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 版本 1 设置身份验证数据

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 版本 1 读取身份验证数据

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>读取“身份验证电话”和“身份验证电子邮件”选项

若要在使用 PowerShell 版本 1 时读取**身份验证电话**和**身份验证电子邮件**，请使用以下命令：

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>使用 PowerShell 版本 2

若要开始，请 [下载并安装 Azure AD 版本 2 PowerShell 模块](/powershell/module/azuread/?view=azureadps-2.0)。

若要从支持的最新版本的 PowerShell 快速安装 `Install-Module` ，请运行以下命令。 第一行检查是否已安装该模块：

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

安装该模块后，请按照以下步骤配置每个字段。

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 版本 2 设置身份验证数据

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 版本 2 读取身份验证数据

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>后续步骤

为用户预先填充身份验证联系人信息后，请完成以下教程以启用自助密码重置：

> [!div class="nextstepaction"]
> [启用 Azure AD 自助式密码重置](tutorial-enable-sspr.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "全局管理员可以修改用户的身份验证联系人信息"