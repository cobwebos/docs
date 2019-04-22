---
title: 对 Azure AD SSPR 和多重身份验证 （预览版）-Azure Active Directory 进行组合的注册故障排除
description: Azure AD 多重身份验证和自助服务密码重置结合使用注册 （预览版） 进行故障排除
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40918493071fe0dd694c43e2b087a2bf7eb197d8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489187"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>故障排除结合安全信息注册 （预览版）

在本文中的信息旨在指导解答疑难问题报告的组合的注册体验的用户的管理员。

|     |
| --- |
| 组合的安全信息注册 Azure 多重身份验证和 Azure Active Directory (Azure AD) 自助服务密码重置是 Azure AD 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="audit-logs"></a>审核日志

为组合的注册记录的事件在 Azure AD 中都在身份验证方法类别审核日志。

![Azure AD 审核日志显示注册事件的接口](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

下表列出了生成的组合注册的所有审核事件：

| 活动 | 状态 | 原因 | 描述 |
| --- | --- | --- | --- |
| 用户已注册的所有所需的安全信息 | Success | 用户已注册的所有所需的安全信息。 | 当用户成功完成注册时发生此事件。|
| 用户已注册的所有所需的安全信息 | 失败 | 用户已取消安全信息注册。 | 当用户取消注册从中断模式时，将发生此事件。|
| 用户已注册的安全信息 | Success | 用户注册*方法*。 | 当用户注册的各个方法，将发生此事件。 *方法*可以是身份验证器应用程序、 电话、 电子邮件、 安全问题，应用密码、 备用电话等。| 
| 查看用户的安全信息 | Success | 用户成功审核安全信息。 | 当用户选择时发生此事件**看起来不错**安全信息检查页上。|
| 查看用户的安全信息 | 失败 | 用户无法查看安全信息。 | 当用户选择时发生此事件**看起来不错**上的安全信息查看页，但在后端发生故障。|
| 已删除用户的安全信息 | Success | 已删除用户*方法*。 | 当用户删除各个方法时，将发生此事件。 *方法*可以是身份验证器应用程序、 电话、 电子邮件、 安全问题，应用密码、 备用电话等。|
| 已删除用户的安全信息 | 失败 | 用户删除失败*方法*。 | 当用户尝试删除某个方法，但由于某种原因该尝试失败时发生此事件。 *方法*可以是身份验证器应用程序、 电话、 电子邮件、 安全问题，应用密码、 备用电话等。|
| 用户更改默认的安全信息 | Success | 用户已更改的默认安全信息*方法*。 | 当用户更改默认方法，将发生此事件。 *方法*可以为身份验证器应用内通知，从我的 authenticator 应用或令牌中，调用 + X XXXXXXXXXX，文本的代码，代码 + X XXXXXXXXX，依次类推。|
| 用户更改默认的安全信息 | 失败 | 用户无法更改的默认安全信息*方法*。 | 当用户尝试更改默认方法，但由于某种原因该尝试失败时发生此事件。 *方法*可以为身份验证器应用内通知，从我的 authenticator 应用或令牌中，调用 + X XXXXXXXXXX，文本的代码，代码 + X XXXXXXXXX，依次类推。|

## <a name="troubleshooting-interrupt-mode"></a>中断模式下进行故障排除

| 症状 | 疑难解答步骤 |
| --- | --- |
| 我没有看到我觉得应该看到的方法。 | 1.检查用户是否具有 Azure AD 管理员角色。 如果是，查看 SSPR 管理员策略差异。 <br> 2.确定用户是否由于强制实施多重身份验证注册或 SSPR 注册强制执行被中断。 请参阅[流程图](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)下"组合注册模式"来确定应显示哪些方法。 <br> 3.确定多重身份验证或 SSPR 策略最近的更改。 如果更改是最新，可能需要一些时间让更新后的策略来传播。|

## <a name="troubleshooting-manage-mode"></a>故障排除管理模式

| 症状 | 疑难解答步骤 |
| --- | --- |
| 我没有添加特定方法的选项。 | 1.确定为多重身份验证或 SSPR 是否启用该方法。 <br> 2.如果启用该方法，则重新保存策略，并等待 1-2 个小时之后再次测试。 <br> 3.如果启用了该方法，请确保用户已尚未设置该方法允许他们要设置的最大数目。|

## <a name="disable-combined-registration"></a>禁用组合的注册

当用户在新注册的电话号码和/或移动应用程序组合的经验，我们的服务戳的一组标志 (StrongAuthenticationMethods) 为该用户在这些方法。 此功能允许用户需要多重身份验证时使用这些方法执行多重身份验证。

如果管理员启用预览、 通过新的体验，注册用户和管理员然后禁用预览版，用户可能不知情的情况下可针对注册多重身份验证还。

如果已完成组合的注册的用户转到当前自助服务密码重置 (SSPR) 注册页在[ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup)，将提示用户执行多重身份验证才能访问该页面。 此步骤应该从技术角度看，但它是新的用户之前已注册 sspr 仅。 尽管此额外步骤不仅通过提供另一个级别的安全改进用户的安全状况，管理员可能想要回滚其用户，以便它们不再能够执行多重身份验证。  

### <a name="how-to-roll-back-users"></a>如何回退用户

如果你，作为管理员，想要重置用户的多重身份验证设置，可以使用下一节中提供的 PowerShell 脚本。 该脚本将清除用户的移动应用和/或电话号码的 StrongAuthenticationMethods 属性。 如果为你的用户运行此脚本，它们将需要在需要时重新注册多重身份验证。 我们在之前回滚所有受影响的用户与一个或两个用户建议测试回滚。

请按照步骤将帮助你回滚用户组。

#### <a name="prerequisites"></a>必备组件

1. 安装相应的 Azure AD PowerShell 模块。 在 PowerShell 窗口中，运行以下命令以安装模块：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 为每行一个 ID 的文本文件保存到计算机的受影响的用户对象 Id 列表。 记下该文件的位置。
1. 将以下脚本保存到您的计算机，并记下脚本的位置：

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>回退

在 PowerShell 窗口中，运行以下命令，提供脚本和用户文件的位置。 出现提示时，输入全局管理员凭据。 该脚本将输出每个用户更新操作的结果。

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>禁用了预览体验

若要禁用你的用户的预览体验，请完成以下步骤：

1. 以用户管理员身份登录到 Azure 门户。
2. 转到**Azure Active Directory** > **用户设置** > **管理的访问面板中预览功能设置**。
3. 下**用户可以使用预览功能来注册和管理的安全信息**，将选择器设置为**None**，然后选择**保存**。

不能再将提示用户注册使用预览版体验。

## <a name="next-steps"></a>后续步骤

* [了解有关组合注册自助服务密码重置和 Azure 多重身份验证的公共预览版的详细信息](concept-registration-mfa-sspr-combined.md)
