---
title: "在 Azure Active Directory 中设置密码过期策略 | Microsoft 文档"
description: "了解如何检查 Azure Active Directory 密码的过期策略，以及如何逐个或批量更改用户密码过期策略"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 6887250c-15d4-4b59-a161-f0380c0f0acb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 4bab9f44d1c91f05618ea510b83beb06540429f2
ms.openlocfilehash: f744a29e12fb693dd422f359d0faacaae004351b


---
# <a name="set-password-expiration-policies-in-azure-active-directory"></a>在 Azure Active Directory 中设置密码过期策略
> [!IMPORTANT]
> **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md)。
>
>

作为 Microsoft 云服务的全局管理员，你可以使用适用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块将用户密码设置为永不过期。 你还可以使用 Windows PowerShell cmdlet 删除永不过期配置，或者查看已将哪些用户密码设置为永不过期。 本文为依赖于 Microsoft Azure Active Directory 提供标识和目录服务的云服务（如 Microsoft Intune 和 Office 365）提供帮助。

> [!NOTE]
> 只能将未通过目录同步进行同步的用户帐户的密码配置为永不过期。 有关目录同步的详细信息，请参阅[目录同步路线图](https://msdn.microsoft.com/library/azure/hh967642.aspx)中的主题列表。
>
>

若要使用 Windows PowerShell cmdlet，首先必须安装它们。

## <a name="what-do-you-want-to-do"></a>要执行什么操作？
* [如何检查密码过期策略](#how-to-check-expiration-policy-for-a-password)
* [设置密码过期](#set-a-password-to-expire)
* [将密码设置为永不过期](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>如何检查密码过期策略
1. 使用公司管理员凭据连接到 Windows PowerShell。
2. 执行下列操作之一：

   * 若要查看单个用户的密码是否已设置为永不过期，请使用用户主体名称 (UPN)（例如 aprilr@contoso.onmicrosoft.com) 或要查看的用户的用户 ID 运行以下 cmdlet：`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * 若要查看所有用户的“密码永不过期”设置，请运行以下 cmdlet：`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>设置密码过期
1. 使用公司管理员凭据连接到 Windows PowerShell。
2. 执行下列操作之一：

   * 若要将某一个用户的密码设置为会过期，请使用用户主体名称 (UPN) 或该用户的用户 ID 运行以下 cmdlet：`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * 若要将组织中所有用户的密码设置为会过期，请使用以下 cmdlet：`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>将密码设置为永不过期
1. 使用公司管理员凭据连接到 Windows PowerShell。
2. 执行下列操作之一：

   * 若要将某一个用户的密码设置为永不过期，请使用用户主体名称 (UPN) 或该用户的用户 ID 运行以下 cmdlet：`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * 若要将组织中所有用户的密码设置为永不过期，请运行以下 cmdlet：`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>后续步骤
* **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md)。



<!--HONumber=Feb17_HO2-->


