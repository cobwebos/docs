---
title: 排查 Azure AD SSPR 和多重身份验证（预览版）的组合注册问题-Azure Active Directory
description: 排查 Azure AD 多重身份验证和自助密码重置组合注册（预览）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e586105d8b2ec85e4ebd85046185ddc21112f0e0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167821"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>结合安全信息注册疑难解答（预览）

本文中的信息旨在指导管理员对合并注册体验的用户所报告的问题进行故障排除。

|     |
| --- |
| Azure 多重身份验证和 Azure Active Directory （Azure AD）自助服务密码重置的组合安全信息注册是 Azure AD 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="audit-logs"></a>审核日志

为组合注册记录的事件位于 Azure AD 审核日志的 "身份验证方法" 类别中。

![显示注册事件 Azure AD 审核日志界面](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

下表列出了通过组合注册生成的所有审核事件：

| 活动 | 状态 | 原因 | 说明 |
| --- | --- | --- | --- |
| 用户已注册所有必需的安全信息 | 成功 | 用户已注册所有必需的安全信息。 | 当用户成功完成注册时，将发生此事件。|
| 用户已注册所有必需的安全信息 | 失败 | 用户取消了安全信息注册。 | 当用户取消注册中断模式时发生此事件。|
| 用户已注册安全信息 | 成功 | 用户注册*方法*。 | 当用户注册单个方法时发生此事件。 *方法*可以是验证器应用、电话、电子邮件、安全问题、应用密码、备用电话等。| 
| 用户审阅的安全信息 | 成功 | 用户已成功查看安全信息。 | 当用户在 "安全信息审阅" 页上选择 "**良好**" 时，将发生此事件。|
| 用户审阅的安全信息 | 失败 | 用户无法查看安全信息。 | 当用户在 "安全信息审阅" 页上选择 "**良好**" 但在后端失败时，将发生此事件。|
| 用户删除的安全信息 | 成功 | 用户删除*方法*。 | 当用户删除单个方法时发生此事件。 *方法*可以是验证器应用、电话、电子邮件、安全问题、应用密码、备用电话等。|
| 用户删除的安全信息 | 失败 | 用户无法删除*方法*。 | 当用户尝试删除某方法，但出于某种原因尝试失败时，将发生此事件。 *方法*可以是验证器应用、电话、电子邮件、安全问题、应用密码、备用电话等。|
| 用户更改的默认安全信息 | 成功 | 用户更改了*方法*的默认安全信息。 | 当用户更改默认方法时发生此事件。 *方法*可以是验证器应用通知、来自我的验证器应用或令牌的代码、调用 + x XXXXXXXXXX、将代码文本编码为 + x XXXXXXXXX 等等。|
| 用户更改的默认安全信息 | 失败 | 用户无法更改*方法*的默认安全信息。 | 当用户尝试更改默认方法，但出于某种原因尝试失败时，将发生此事件。 *方法*可以是验证器应用通知、来自我的验证器应用或令牌的代码、调用 + x XXXXXXXXXX、将代码文本编码为 + x XXXXXXXXX 等等。|

## <a name="troubleshooting-interrupt-mode"></a>中断模式疑难解答

| 症状 | 故障排除步骤 |
| --- | --- |
| 我看不到我希望看到的方法。 | 1. 检查用户是否具有 Azure AD 管理员角色。 如果是，请查看 SSPR 管理员策略差异。 <br> 2. 确定用户是否由于多重身份验证注册强制或 SSPR 注册强制而被中断。 请参阅 "组合注册模式" 下的[流程图](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)，以确定应显示的方法。 <br> 3. 确定如何更改最近的多重身份验证或 SSPR 策略。 如果更改是最新的，则更新的策略可能需要一些时间才能传播。|

## <a name="troubleshooting-manage-mode"></a>管理模式疑难解答

| 症状 | 故障排除步骤 |
| --- | --- |
| 我没有添加特定方法的选项。 | 1. 确定是否为多因素身份验证或 SSPR 启用了方法。 <br> 2. 如果已启用此方法，请再次保存策略并等待1-2 小时，然后再进行测试。 <br> 3. 如果启用了此方法，请确保用户尚未设置允许其设置的该方法的最大数目。|

## <a name="disable-combined-registration"></a>禁用组合注册

当用户在新的组合体验中注册电话号码和/或移动应用程序时，我们的服务将为该用户的这些方法标记一组标志（StrongAuthenticationMethods）。 此功能允许用户在需要多重身份验证时，通过这些方法执行多重身份验证。

如果管理员启用了预览，用户将通过新体验注册，然后管理员禁用预览版，用户可能也会在不知情的情况下注册多重身份验证。

如果已完成组合注册的用户进入[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)上的 "当前自助服务密码重置（SSPR）" 注册页，则系统会提示用户执行多重身份验证，然后才能访问该页面。 此步骤应从技术角度来看，但对于之前仅注册了 SSPR 的用户来说，这是新的。 尽管这一额外步骤通过提供另一级别的安全性来改善用户的安全状况，但管理员可能希望回滚用户的用户，使他们不再能够执行多重身份验证。  

### <a name="how-to-roll-back-users"></a>如何回退用户

如果你作为管理员，想要重置用户的多重身份验证设置，则可以使用下一部分中提供的 PowerShell 脚本。 此脚本将清除用户的移动应用和/或电话号码的 StrongAuthenticationMethods 属性。 如果你为用户运行此脚本，则他们需要重新注册多因素身份验证（如果需要）。 建议在回滚所有受影响的用户之前，为一个或两个用户测试回滚。

接下来的步骤将帮助你回滚用户或用户组。

#### <a name="prerequisites"></a>先决条件

1. 安装适当的 Azure AD PowerShell 模块。 在 PowerShell 窗口中，运行以下命令以安装模块：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 将受影响的用户对象 Id 列表保存到你的计算机上，作为一个文本文件，每行有一个 ID。 记下该文件的位置。
1. 将以下脚本保存到计算机，并记下该脚本的位置：

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

在 PowerShell 窗口中运行以下命令，并提供脚本和用户文件位置。 出现提示时，输入全局管理员凭据。 该脚本将输出每个用户更新操作的结果。

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>禁用预览体验

若要禁用用户的预览体验，请完成以下步骤：

1. 以用户管理员身份登录到 Azure 门户。
2. 请参阅**Azure Active Directory** > **用户设置** > **管理访问面板预览功能的设置**。
3. 在 "**用户可以使用预览功能注册和管理安全信息**" 下，将选择器设置为 "**无**"，然后选择 "**保存**"。

系统将不再提示用户使用预览体验进行注册。

## <a name="next-steps"></a>后续步骤

* [详细了解用于自助服务密码重置和 Azure 多重身份验证的组合注册的公共预览版](concept-registration-mfa-sspr-combined.md)
