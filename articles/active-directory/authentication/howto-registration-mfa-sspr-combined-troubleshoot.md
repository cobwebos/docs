---
title: 故障排除组合注册 - Azure 活动目录
description: 排除 Azure AD 多重身份验证和自助服务密码重置组合注册
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c840df2c53554519f62a3d1d7a7d8b305187ffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450932"
---
# <a name="troubleshooting-combined-security-information-registration"></a>故障排除组合安全信息注册

本文中的信息旨在指导正在排除用户报告的合并注册体验用户报告的问题的管理员。

## <a name="audit-logs"></a>审核日志

为合并注册而记录的事件位于 Azure AD 审核日志中的"身份验证方法"类别中。

![Azure AD 审核日志界面，显示注册事件](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

下表列出了合并注册生成的所有审核事件：

| 活动 | 状态 | 原因 | 说明 |
| --- | --- | --- | --- |
| 用户注册了所有必需的安全信息 | Success | 用户注册了所有必需的安全信息。 | 当用户成功完成注册时，将发生此事件。|
| 用户注册了所有必需的安全信息 | 失败 | 用户已取消安全信息注册。 | 当用户取消中断模式的注册时，将发生此事件。|
| 用户注册的安全信息 | Success | 用户注册*方法*. | 当用户注册单个方法时，将发生此事件。 *方法*可以是身份验证器应用、电话、电子邮件、安全问题、应用密码、备用电话等。| 
| 用户查看的安全信息 | Success | 用户成功查看了安全信息。 | 当用户在安全信息审核页上选择 **"看起来不错"** 时，将发生此事件。|
| 用户查看的安全信息 | 失败 | 用户无法查看安全信息。 | 当用户在安全信息审核页上选择 **"看起来不错"，** 但后端出现问题时，将发生此事件。|
| 用户删除的安全信息 | Success | 用户删除*的方法*。 | 当用户删除单个方法时，将发生此事件。 *方法*可以是身份验证器应用、电话、电子邮件、安全问题、应用密码、备用电话等。|
| 用户删除的安全信息 | 失败 | 用户未能删除*方法*。 | 当用户尝试删除方法但尝试由于某种原因失败时，将发生此事件。 *方法*可以是身份验证器应用、电话、电子邮件、安全问题、应用密码、备用电话等。|
| 用户更改的默认安全信息 | Success | 用户更改了*方法*的默认安全信息。 | 当用户更改默认方法时，将发生此事件。 *方法*可以是身份验证器应用通知、来自验证器应用或令牌的代码、调用 #X XXXXXXXXXX、将代码文本到 #X XXXXXXXXX 等。|
| 用户更改的默认安全信息 | 失败 | 用户未能更改*方法*的默认安全信息。 | 当用户尝试更改默认方法，但尝试由于某种原因失败时，将发生此事件。 *方法*可以是身份验证器应用通知、来自验证器应用或令牌的代码、调用 #X XXXXXXXXXX、将代码文本到 #X XXXXXXXXX 等。|

## <a name="troubleshooting-interrupt-mode"></a>故障排除中断模式

| 症状 | 疑难解答步骤 |
| --- | --- |
| 我没有看到我期望看到的方法。 | 1. 检查用户是否具有 Azure AD 管理员角色。 如果是，请查看 SSPR 管理策略差异。 <br> 2. 确定用户是否因为多重身份验证注册实施或 SSPR 注册强制而中断。 请参阅"合并注册模式"下的[流程图](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)，以确定应显示哪些方法。 <br> 3. 确定多因素身份验证或 SSPR 策略更改最近的方式。 如果更改是最近更改，则更新的策略可能需要一些时间才能传播。|

## <a name="troubleshooting-manage-mode"></a>故障排除管理模式

| 症状 | 疑难解答步骤 |
| --- | --- |
| 我没有添加特定方法的选项。 | 1. 确定该方法是启用的，用于多重身份验证还是 SSPR。 <br> 2. 如果方法已启用，请再次保存策略，然后等待 1-2 小时再进行测试。 <br> 3. 如果启用了该方法，请确保用户尚未设置允许其设置的最大方法数。|

## <a name="disable-combined-registration"></a>禁用合并注册

当用户在新的组合体验中注册电话号码和/或移动应用时，我们的服务会为该用户上这些方法标记一组标志（强身份验证方法）。 此功能允许用户在需要多重身份验证时使用这些方法执行多重身份验证。

如果管理员启用预览，用户通过新体验注册，然后管理员禁用预览，则用户也可能在不知不觉中注册为多重身份验证。

如果已完成合并注册的用户转到 当前自助服务密码重置 （SSPR） 注册页，[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)系统将提示用户执行多重身份验证，然后才能访问该页面。 从技术角度来看，此步骤是预期的，但对于以前只为 SSPR 注册的用户来说，这是新的。 尽管此额外步骤通过提供另一级别的安全性来改善用户的安全状态，但管理员可能希望回滚其用户，以便他们不再能够执行多重身份验证。  

### <a name="how-to-roll-back-users"></a>如何回退用户

如果您作为管理员想要重置用户的多重身份验证设置，则可以使用下一节中提供的 PowerShell 脚本。 该脚本将清除用户的移动应用和/或电话号码的"强身份验证方法"属性。 如果为用户运行此脚本，则如果需要，则需要重新注册多重身份验证。 我们建议在回滚所有受影响的用户之前，使用一个或两个用户测试回滚。

以下步骤将帮助您回滚用户或用户组。

#### <a name="prerequisites"></a>先决条件

1. 安装相应的 Azure AD PowerShell 模块。 在 PowerShell 窗口中，运行以下命令以安装模块：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 将受影响的用户对象 ID 列表作为文本文件保存到您的计算机，每行包含一个 ID。 记下该文件的位置。
1. 将以下脚本保存到计算机并记下该脚本的位置：

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

在 PowerShell 窗口中，运行以下命令，提供脚本和用户文件位置。 出现提示时，输入全局管理员凭据。 该脚本将输出每个用户更新操作的结果。

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>禁用更新的体验

要禁用用户的更新体验，请完成以下步骤：

1. 以用户管理员身份登录到 Azure 门户。
2. 转到**Azure 活动目录** > **用户设置** > **管理访问面板预览功能的设置**。
3. **在"用户"下可以使用预览功能注册和管理安全信息**，将选择器设置为 **"无**"，然后选择"**保存**"。

将不再提示用户使用更新的体验进行注册。

## <a name="next-steps"></a>后续步骤

* [了解有关自助服务密码重置和 Azure 多重身份验证的组合注册数](concept-registration-mfa-sspr-combined.md)
