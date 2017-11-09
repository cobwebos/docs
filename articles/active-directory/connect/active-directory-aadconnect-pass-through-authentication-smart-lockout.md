---
title: "Azure AD Connect：传递身份验证 - 智能锁定 | Microsoft Docs"
description: "本文介绍 Azure Active Directory (Azure AD) 传递身份验证如何保护本地帐户免受云中的密码搜索攻击。"
services: active-directory
keywords: "Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 771741fd7da8c9b6932851851aaca148f9596643
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Azure Active Directory 传递身份验证：智能锁定

## <a name="overview"></a>概述

Azure AD 可防止密码搜索攻击，并防止在 Office 365 和 SaaS 应用程序中锁定正版用户。 此功能称为“智能锁定”，使用传递身份验证方法登录时支持此功能。 默认情况下，所有租户均已启用智能锁定功能，以便始终保护用户帐户；无需将其打开。

智能锁定的工作原理如下：跟踪失败的登录尝试，然后在某一特定“锁定阈值”后，启动“锁定持续时间”。 锁定持续时间内攻击者的任何登录尝试都将被拒。 如果攻击继续，则在锁定持续时间结束后发生的后续失败登录尝试将导致更长的锁定持续时间。

>[!NOTE]
>默认锁定阈值为 10 次失败尝试，默认锁定持续时间为 60 秒。

智能锁定还可区分正版用户与攻击者的登录，并在大多数情况下仅锁定攻击者。 此功能可防止攻击者恶意锁定正版用户。 我们通过过去的登录行为、用户的设备和浏览器和其他信号来区分正版用户和攻击者。 我们正在不断改进我们的算法。

由于传递身份验证会将密码验证请求转发到本地 Active Directory (AD)，因此需要防止攻击者锁定用户的 AD 帐户。 制定自己的 AD 帐户锁定策略（具体而言， [帐户锁定阈值](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx)和[在策略后重置帐户锁定计数器](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)）后，需要适当配置 Azure AD 的锁定阈值和锁定持续时间值，以在攻击者访问你的本地 AD 前将其从云中筛选出来。

>[!NOTE]
>智能锁定功能是免费的，默认情况下，对所有客户_启用_。 但是，使用图形 API 修改 Azure AD 的锁定阈值和锁定持续时间值需要租户具有至少一个 Azure AD Premium P2 许可证。 使用传递身份验证获取智能锁定功能，并不需要每个用户拥有一个 Azure AD Premium P2 许可证。

若要确保用户的本地 AD 帐户受到良好保护，需要确保：

1.  Azure AD 的锁定阈值“小于”AD 的帐户锁定阈值。 建议设置值，使 AD 的帐户锁定阈值至少是 Azure AD 的锁定阈值的二或三倍。
2.  Azure AD 的锁定持续时间（以秒表示）“长于”AD 的在此后重置帐户锁定计数器的时间（以分钟表示）。

>[!IMPORTANT]
>目前，如果用户的云帐户已被智能锁定功能锁定，管理员无法为其解锁。 他们必须等到锁定持续时间到期。

## <a name="verify-your-ad-account-lockout-policies"></a>验证 AD 帐户锁定策略

使用以下说明验证 AD 帐户锁定策略：

1.  打开“组策略管理”工具。
2.  编辑已应用到所有用户的组策略，例如“默认域策略”。
3.  导航到“计算机配置”\“策略”\“Windows 设置”\“安全设置”\“帐户策略”\“帐户锁定策略”。
4.  验证“帐户锁定阈值”和“在此后重置帐户锁定计数器”值。

![AD 帐户锁定策略](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-needs-premium-license"></a>使用图形 API 管理租户的智能锁定值（需要 Premium 许可证）

>[!IMPORTANT]
>使用图形 API 修改 Azure AD 的锁定阈值和锁定持续时间值是 Azure AD Premium P2 的一项功能。 它还要求你是你所在租户的全局管理员。

可以使用 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)读取、设置和更新 Azure AD 的智能锁定值。 还可以以编程方式执行这些操作。

### <a name="read-smart-lockout-values"></a>读取智能锁定值

请按照下列步骤读取租户的智能锁定值：

1. 以所在租户的全局管理员身份登录 Graph 浏览器。 出现提示时，授予访问所需权限的权限。
2. 单击“修改权限”并选择“Directory.ReadWrite.All”权限。
3. 配置图形 API 请求，如下所示：将版本设置为“BETA 版本”，将请求类型设置为“GET”并将 URL 设置为 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`。
4. 单击“运行查询”，查看租户的智能锁定值。 如果之前尚未设置租户的值，则将看到一个空集。

### <a name="set-smart-lockout-values"></a>设置智能锁定值

请按照下列步骤设置租户的智能锁定值（仅限首次设置）：

1. 以所在租户的全局管理员身份登录 Graph 浏览器。 出现提示时，授予访问所需权限的权限。
2. 单击“修改权限”并选择“Directory.ReadWrite.All”权限。
3. 配置图形 API 请求，如下所示：将版本设置为“BETA 版本”，将请求类型设置为“POST”并将 URL 设置为 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`。
4. 将以下 JSON 请求复制并粘贴到“请求正文”字段。
5. 单击“运行查询”，设置租户的智能锁定值。

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
>如果不使用它们，可将“BannedPasswordList”和“EnableBannedPasswordCheck”值分别保留为空 ("") 和 "false"。

确保已使用[这些步骤](#read-smart-lockout-values)正确设置了租户的智能锁定值。

### <a name="update-smart-lockout-values"></a>更新智能锁定值

请按照下列步骤更新租户的智能锁定值（如果之前已对其进行设置）：

1. 以所在租户的全局管理员身份登录 Graph 浏览器。 出现提示时，授予访问所需权限的权限。
2. 单击“修改权限”并选择“Directory.ReadWrite.All”权限。
3. [请按照下列步骤读取租户的智能锁定值](#read-smart-lockout-values)。 复制“displayName”为“PasswordRuleSettings”的项的 `id` 值 (GUID)。
4. 使用步骤 3 中 `<id>` 的 GUID 配置图形 API 请求，如下所示：将版本设置为“BETA 版本”，将请求类型设置为“PATCH”并将 URL 设置为 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`。
5. 将以下 JSON 请求复制并粘贴到“请求正文”字段。 适当更改智能锁定值。
6. 单击“运行查询”，更新租户的智能锁定值。

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

确保已使用[这些步骤](#read-smart-lockout-values)正确更新了租户的智能锁定值。

## <a name="next-steps"></a>后续步骤
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
