---
title: 对 Azure AD SSPR 和 MFA （预览版）-Azure Active Directory 进行组合的注册故障排除
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
ms.openlocfilehash: d926f7312b62e788289939dfd81c236a33503b43
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370459"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>故障排除结合安全信息注册 （预览版）

这篇文章中提供的信息可指导管理员解决报告的最终用户的组合的注册体验的问题。

|     |
| --- |
| 组合的安全信息注册 Azure 多重身份验证和 Azure AD 自助服务密码重置是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="audit-logs"></a>审核日志

在 Azure AD 中将"身份验证方法"类别下的组合的注册记录的事件审核日志。

![Azure AD 审核日志显示注册事件的接口](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

下面列出了生成的组合注册的所有审核事件：

| 活动 | 状态 | 原因 | 描述 |
| --- | --- | --- | --- |
| 用户已注册的所有所需的安全信息 | Success | 用户已注册的所有所需的安全信息。 | 当用户成功完成注册时发生此事件。|
| 用户已注册的所有所需的安全信息 | 失败 | 用户已取消安全信息注册。 | 当用户取消注册从中断模式时，将发生此事件。|
| 用户已注册的安全信息 | Success | 用户已注册"的方法"。 | 当用户注册的各个方法，将发生此事件。 "Method"可以是电话、 电子邮件，身份验证器应用程序安全问题、 应用程序密码、 备用电话，等等。| 
| 查看用户的安全信息 | Success | 用户成功审核安全信息。 | 用户安全信息检查页上单击"看起来不错"时发生此事件。|
| 查看用户的安全信息 | 失败 | 用户无法查看安全信息。 | 当用户单击"看起来不错"上的安全信息查看页，但在后端中发生故障，会发生此事件。|
| 已删除用户的安全信息 | Success | 已删除用户"方法"。 | 当用户删除各个方法时，将发生此事件。 "Method"可以是电话、 电子邮件，身份验证器应用程序安全问题、 应用程序密码、 备用电话，等等。|
| 已删除用户的安全信息 | 失败 | 用户无法删除"方法"。 | 当用户尝试删除某个方法，但出于某种原因失败时，将发生此事件。 "Method"可以是电话、 电子邮件，身份验证器应用程序安全问题、 应用程序密码、 备用电话，等等。|
| 用户更改默认的安全信息 | Success | 用户更改为"method"的默认安全信息。 | 当用户更改其默认方法，将发生此事件。 "Method"身份验证器应用内通知，从我的 authenticator 应用或令牌中，调用 + X XXXXXXXXXX，文本的代码的代码到 + X XXXXXXXXX，等等。|
| 用户更改默认的安全信息 | 失败 | 用户无法将默认的安全信息更改为"方法"。 | 当用户尝试更改其默认方法，但由于某种原因而失败时发生此事件。 身份验证器应用内通知，从我的 authenticator 应用或令牌中，调用代码 + X XXXXXXXXXX，文本到 + X XXXXXXXXX 等的代码，可以是"方法"。|

## <a name="troubleshooting-interrupt-mode"></a>中断模式下进行故障排除

| 症状 | 疑难解答步骤 |
| --- | --- |
| 我没有看到我觉得应该看到的方法。 | 1.检查用户是否具有 Azure AD 管理员角色。 如果是，查看 SSPR 管理员策略差异。 <br> 2.确定用户是否由于强制执行 MFA 注册或 SSPR 注册强制执行被中断。 查看下组合的注册模式来确定应显示哪些方法的流程图。 <br> 3.确定如何最近 MFA 或 SSPR 策略的更改。 如果更改是最新，可能需要一些时间让更新后的策略来传播。|

## <a name="troubleshooting-manage-mode"></a>故障排除管理模式

| 症状 | 疑难解答步骤 |
| --- | --- |
| 我没有添加特定方法的选项。 | 1.确定为 MFA 或 SSPR 是否启用该方法。 <br> 2.如果启用了该方法，将其重新保存策略，并等待 1-2 个小时后才能再次测试。 <br> 3.如果启用了该方法，请确保用户已尚未设置该方法允许他们要设置的最大数目。|

## <a name="disable-combined-registration"></a>禁用组合的注册

当用户在新注册其电话号码和/或移动应用程序组合的经验，我们的服务戳的一组标志 (StrongAuthenticationMethods) 为该用户在这些方法。 通过此功能，用户可在需要进行多重身份验证 (MFA) 时使用这些方法执行 MFA。

用户通过新体验注册的方法具有 StrongAuthenticationMethods 属性集。 如果管理员启用预览版，并且用户通过新体验注册，则在管理员禁用预览版后，用户也可能在不知情的情况下注册了 MFA。

如果用户已完成合并注册，则导航到当前的自助服务密码重置 (SSPR) 注册页，在[ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup)，将提示他们先执行 MFA，然后他们可以访问该页面。 从技术角度来说，此步骤是一种预期行为，但对之前仅注册 SSPR 的用户来说，此步骤是一种新行为。 虽然此额外步骤提供了更高级别的安全，从而提升了用户的安全状态，但管理员可能想要回退其用户，使其无法再执行 MFA。  

### <a name="how-to-roll-back-users"></a>如何回退用户

如果你作为管理员想要重置用户的 MFA 设置，我们已创建了 PowerShell 脚本，该脚本可清除用户的移动应用和/或电话号码的 StrongAuthenticationMethods 属性。 针对用户运行此脚本意味着他们需要根据需要重新注册 MFA。 我们建议在回退所有受影响的用户之前，先通过一个或两个用户测试回退。

下面的步骤将帮助回退一个或一组用户：

#### <a name="prerequisites"></a>必备组件

1. 需要安装相应的 Azure AD PowerShell 模块。 在 PowerShell 窗口中，运行以下命令以安装模块：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 在计算机中将受影响的用户对象 ID 列表另存为每行一个 ID 的文本文件。 记下该文件的位置。
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

在 PowerShell 窗口中，更新突出显示的位置后运行以下命令。 出现提示时，输入全局管理员凭据。 该脚本将输出每个用户更新操作的结果。

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>禁用预览体验

若要禁用用户的预览体验，请完成以下步骤：

1. 以全局管理员或用户管理员身份登录到 Azure 门户。
2. 依次浏览到“Azure Active Directory”、“用户设置”和“管理访问面板预览功能的设置”。
3. 在“用户可以使用预览功能来注册和管理安全信息”下，将选择器设置为“无”，然后单击“保存”。

将不再提示用户使用预览体验进行注册。

## <a name="next-steps"></a>后续步骤

* [了解有关组合注册自助服务密码重置和 Azure 多重身份验证的公共预览版的详细信息](concept-registration-mfa-sspr-combined.md)
