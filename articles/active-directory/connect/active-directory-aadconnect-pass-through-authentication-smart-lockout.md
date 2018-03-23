---
title: Azure AD Connect：传递身份验证 - 智能锁定 | Microsoft Docs
description: 本文介绍 Azure Active Directory (Azure AD) 传递身份验证如何保护本地帐户免受云中的暴力密码搜索攻击
services: active-directory
keywords: Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: 9477d47824213d7ea15bcf6c6b615a220bae2e48
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Azure Active Directory 传递身份验证：智能锁定

## <a name="overview"></a>概述

Azure Active Directory (Azure AD) 可防止暴力密码搜索攻击，并防止在 Office 365 和 SaaS 应用程序中锁定正版用户。 此功能称为“智能锁定”，使用传递身份验证方法登录时支持此功能。 默认为所有租户启用“智能锁定”，而不仅是使用传递身份验证的租户，该功能可持续保护用户帐户。

智能锁定会持续跟踪失败的登录尝试。 在达到某个锁定阈值后，它将启动“锁定持续时间”。 在锁定持续时间内，智能锁定将拒绝来自攻击者的任何登录尝试。 如果攻击继续，则在锁定持续时间结束后发生的后续失败登录尝试将导致更长的锁定持续时间。

>[!NOTE]
>默认锁定阈值是 10 次失败尝试。 默认锁定持续时间为 60 秒。

智能锁定还可区分正版用户的登录与攻击者的登录。 在大多数情况下，它将仅锁定攻击者。 此功能可防止攻击者恶意锁定正版用户。 智能锁定使用过去的登录行为、用户的设备和浏览器以及其他信号来区分正版用户和攻击者。 算法正在不断得到改进。

传递身份验证会将密码验证请求转发到本地 Active Directory，因此，需要防止攻击者锁定用户的 Active Directory 帐户。 Active Directory 具有其自己帐户锁定策略，具体而言是[帐户锁定阈值](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx)和[在此后重置帐户锁定计数器](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)策略。 请适当配置 Azure AD 锁定阈值和锁定持续时间值，以在云中的攻击到达本地 Active Directory 之前将其筛选出来。

>[!NOTE]
>>智能锁定功能是免费的，默认情况下，对所有客户_启用_。 但是，使用图形 API 修改 Azure AD 的锁定阈值和锁定持续时间值需要租户已激活 Azure AD Premium P2。 

若要确保用户的本地 Active Directory 帐户受到良好保护，需要确保：

   * Azure AD 的锁定阈值小于 Active Directory 的帐户锁定阈值。 请设置此值，以便使 Active Directory 的帐户锁定阈值至少长于 Azure AD 锁定阈值的二倍或三倍。
   * Azure AD 的锁定持续时间（以秒计）长于 Active Directory 的“在此后重置帐户锁定计数器”的持续时间（以分钟计）。

>[!IMPORTANT]
>目前，如果用户的云帐户已被智能锁定功能锁定，管理员将无法为其解锁。 管理员必须等到锁定持续时间到期。

## <a name="verify-your-active-directory-account-lockout-policies"></a>验证 Active Directory 帐户锁定策略

使用以下说明验证 Active Directory 帐户锁定策略：

1.  打开“组策略管理”工具。
2.  编辑已应用到所有用户的组策略，例如“默认域策略”。
3.  浏览到“计算机配置” > “策略” > “Windows 设置” > “安全设置” > “帐户策略” > “帐户锁定策略”。
4.  验证“帐户锁定阈值”和“在此后重置帐户锁定计数器”值。

![Active Directory 帐户锁定策略](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>使用图形 API 管理租户的智能锁定值（需要 Premium 许可证）

>[!IMPORTANT]
>使用图形 API 修改 Azure AD 的锁定阈值和锁定持续时间值是 Azure AD Premium P2 的一项功能。 此外，它还要求你是你所在租户的全局管理员。

你可以使用 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)读取、设置和更新 Azure AD 的智能锁定值， 也可以用编程方式执行这些操作。

### <a name="view-smart-lockout-values"></a>查看智能锁定值

请按照下列步骤查看租户的智能锁定值：

1. 以所在租户的全局管理员身份登录 Graph 浏览器。 出现提示时，授予访问所需权限的权限。
2. 选择“修改权限”，然后选择 Directory.ReadWrite.All 权限。
3. 配置图形 API 请求，如下所示：将版本设置为 BETA，将请求类型设置为 GET，并将 URL 设置为 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`。
4. 选择“运行查询”，查看租户的智能锁定值。 如果之前尚未设置租户的值，则将看到一个空集。

### <a name="set-smart-lockout-values"></a>设置智能锁定值

请按照下列步骤设置租户的智能锁定值（仅限首次设置）：

1. 以所在租户的全局管理员身份登录 Graph 浏览器。 出现提示时，授予访问所需权限的权限。
2. 选择“修改权限”，然后选择 Directory.ReadWrite.All 权限。
3. 配置图形 API 请求，如下所示：将版本设置为 BETA，将请求类型设置为 POST，并将 URL 设置为 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`。
4. 将以下 JSON 请求复制并粘贴到“请求正文”字段。
5. 选择“运行查询”，设置租户的智能锁定值。

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>如果不使用它们，可将 BannedPasswordList 和 EnableBannedPasswordCheck 的值分别保留为空 (**""**) 和 false。

通过使用[查看智能锁定值](#view-smart-lockout-values)中的步骤，验证你是否正确设置了租户的智能锁定值。

### <a name="update-smart-lockout-values"></a>更新智能锁定值

请按照下列步骤更新租户的智能锁定值（如果之前已对其进行设置）：

1. 以所在租户的全局管理员身份登录 Graph 浏览器。 出现提示时，授予访问所需权限的权限。
2. 选择“修改权限”，然后选择 Directory.ReadWrite.All 权限。
3. [请按照下列步骤查看租户的智能锁定值](#view-smart-lockout-values)。 将“displayName”的项的 `id` 值 (GUID) 作为 PasswordRuleSettings 进行复制。
4. 配置图形 API 请求，如下所示：将版本设置为 BETA，将请求类型设置为 PATCH，并将 URL 设置为 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`。 将步骤 3 中的 GUID 用于 `<id>`。
5. 将以下 JSON 请求复制并粘贴到“请求正文”字段。 适当更改智能锁定值。
6. 选择“运行查询”，更新租户的智能锁定值。

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

通过使用[查看智能锁定值](#view-smart-lockout-values)中的步骤，验证你是否正确更新了租户的智能锁定值。

## <a name="next-steps"></a>后续步骤
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 论坛来提交新的功能请求。
