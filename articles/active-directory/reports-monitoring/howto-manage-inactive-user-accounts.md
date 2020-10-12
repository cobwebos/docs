---
title: 如何管理 Azure AD 中的非活动用户帐户 | Microsoft Docs
description: 了解如何检测和处理 Azure AD 中已过时的用户帐户
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b48a84bb69a356815cccd1e33c555eeb667699f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89244715"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>如何：管理 Azure AD 中的非活动用户帐户

在大型环境中，当员工离开组织时，并不总是会删除用户帐户。 作为 IT 管理员，你希望检测和处理这些过时的用户帐户，因为它们会带来安全风险。

本文介绍了在 Azure AD 中处理过时用户帐户的方法。 

## <a name="what-are-inactive-user-accounts"></a>什么是非活动用户帐户？

非活动帐户是组织成员获取对资源的访问权限时不再需要的用户帐户。 非活动帐户的一个重要标志是，在一段时间内未使用这些帐户登录到你的环境。 由于非活动帐户与登录活动相关联，因此可以使用上次成功登录的时间戳来检测它们。 

此方法的难点是定义“一段时间”在你的环境中的含义。 例如，用户可能由于度假而在一段时间内未登录到环境。 在定义非活动用户帐户的增量时，需要考虑未登录到环境的所有合理原因。 在许多组织中，非活动用户帐户的增量为 90 到 180 天。 

通过上次成功登录可窥见用户对持续访问资源的需要。  它有助于确定是否仍需要组成员身份或应用访问权限，或者是否可以将其删除。 对于外部用户管理，你可以了解外部用户在租户内是否仍处于活动状态，或者是否应将其清除。 

    
## <a name="how-to-detect-inactive-user-accounts"></a>如何检测非活动用户帐户

通过评估由 Microsoft Graph API 的 signInActivity 资源类型公开的 lastSignInDateTime 属性来检测非活动帐户  。 使用此属性，可以为以下场景实现解决方案：

- 用户（按名称）：在此场景中，按名称搜索特定用户，这样可以评估 lastSignInDateTime：`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- 用户（按日期）：在此场景中，使用指定日期之前的 lastSignInDateTime 请求用户列表：`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>需要了解的事项

本部分列出了需要了解的有关 lastSignInDateTime 属性的内容。

### <a name="how-can-i-access-this-property"></a>如何访问此属性？

lastSignInDateTime 属性由 [Microsoft Graph REST API](/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph) 的 [signInActivity 资源类型](/graph/api/resources/signinactivity?view=graph-rest-beta)公开。   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>lastSignInDateTime 属性是否可通过 Get-AzureAdUser cmdlet 获取？

不是。

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>访问属性需要哪些版本的 Azure AD？

可以在 Azure AD 的所有版本中访问此属性。

### <a name="what-permission-do-i-need-to-read-the-property"></a>读取属性需要哪些权限？

若要读取此属性，需要授予以下权限： 

- AuditLogs.Read.All
- Organisation.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Azure AD 何时更新此属性？

每个成功的交互式登录都会使基础数据存储更新。 通常，成功的登录会在 10 分钟内显示在相关登录报表中。
 

### <a name="what-does-a-blank-property-value-mean"></a>空属性值的含义是什么？

若要生成 lastSignInDateTime 时间戳，你需要成功登录。 由于 lastSignInDateTime 属性是一项新功能，因此，在以下情况下，lastSignInDateTime 属性的值可能为空：

- 用户的上次成功登录在此功能发布（2019 年 12 月 1 日）之前发生。
- 受影响的用户帐户从未进行成功登录。

## <a name="next-steps"></a>后续步骤

* [使用具有证书的 Azure Active Directory 报告 API 获取数据](tutorial-access-api-with-certificates.md)
* [审核 API 参考](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [登录活动报告 API 参考](/graph/api/resources/signin?view=graph-rest-beta)