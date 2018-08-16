---
title: 禁用 Azure AD SSPR 和 MFA 的聚合注册（公共预览版）
description: 禁用 Azure AD 多重身份验证和自助密码重置注册（公共预览版）
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 3ce08f67f001a7c43602627b9eeda3ad60f867c1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623215"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>禁用 Azure AD 聚合注册（公共预览版）

当用户在新聚合体验中注册其电话号码和/或移动应用时，我们的服务将为该用户贴上一组这些方法的标记 (StrongAuthenticationMethods)。 通过此功能，用户可在需要进行多重身份验证 (MFA) 时使用这些方法执行 MFA。

用户通过新体验注册的方法具有 StrongAuthenticationMethods 属性集。 公共预览可用后，也会发生此行为。 如果管理员启用预览版，并且用户通过新体验注册，则在管理员禁用预览版后，用户也可能在不知情的情况下注册了 MFA。

如果已完成聚合注册的用户导航到当前的 SSPR 注册页 ([https://aka.ms/ssprsetup](https://aka.ms/ssprsetup))，系统将提示这些用户先执行 MFA，然后才可以访问该页。 从技术角度来说，此步骤是一种预期行为，但对之前仅注册 SSPR 的用户来说，此步骤是一种新行为。 虽然此额外步骤提供了更高级别的安全，从而提升了用户的安全状态，但管理员可能想要回退其用户，使其无法再执行 MFA。  

## <a name="how-to-roll-back-users"></a>如何回退用户

如果你作为管理员想要重置用户的 MFA 设置，我们已创建了 PowerShell 脚本，该脚本可清除用户的移动应用和/或电话号码的 StrongAuthenticationMethods 属性。 针对用户运行此脚本意味着他们需要根据需要重新注册 MFA。 我们建议在回退所有受影响的用户之前，先通过一个或两个用户测试回退。

下面的步骤将帮助回退一个或一组用户：

### <a name="pre-requisites"></a>先决条件

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

### <a name="rollback"></a>回退

在 PowerShell 窗口中，更新突出显示的位置后运行以下命令。 出现提示时，输入全局管理员凭据。 该脚本将输出每个用户更新操作的结果。

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>禁用预览体验

若要禁用用户的预览体验，请完成以下步骤：

1. 以全局管理员或用户管理员身份登录到 Azure 门户。
2. 依次浏览到“Azure Active Directory”、“用户设置”和“管理访问面板预览功能的设置”。
3. 在“用户可以使用预览功能来注册和管理安全信息”下，将选择器设置为“无”，然后单击“保存”。

将不再提示用户使用预览体验进行注册。

## <a name="next-steps"></a>后续步骤

[深入了解有关自助密码重置和 Azure 多重身份验证的聚合注册公共预览版](concept-registration-mfa-sspr-converged.md)