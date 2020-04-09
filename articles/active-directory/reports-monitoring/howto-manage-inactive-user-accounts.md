---
title: 如何管理 Azure AD 中的非活动用户帐户 |微软文档
description: 了解如何检测和处理 Azure AD 中已过时的用户帐户
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886035"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>如何：在 Azure AD 中管理非活动用户帐户

在大型环境中，当员工离开组织时，用户帐户并不总是被删除。 作为 IT 管理员，您希望检测和处理这些过时的用户帐户，因为它们存在安全风险。

本文介绍在 Azure AD 中处理过时的用户帐户的方法。 

## <a name="what-are-inactive-user-accounts"></a>什么是非活动用户帐户？

非活动帐户是组织成员不再需要的用户帐户来访问您的资源。 非活动帐户的一个关键标识符是，它们有*一段时间*没有用于登录到您的环境。 由于非活动帐户绑定到登录活动，因此您可以使用成功检测它们的最后一个登录的时间戳。 

此方法的挑战是定义在环境的情况下 *，一段时间*意味着什么。 例如，用户可能*一段时间*没有登录到环境，因为他们正在度假。 在定义非活动用户帐户的增量时，您需要考虑不登录到环境的所有合法原因。 在许多组织中，非活动用户帐户的增量在 90 到 180 天之间。 

上次成功登录提供了用户对资源访问的持续需求的潜在见解。  它可以帮助确定是否仍然需要组成员身份或应用访问权限或是否可以删除。 对于外部用户管理，您可以了解外部用户是否仍在租户中处于活动状态或应清理。 

    
## <a name="how-to-detect-inactive-user-accounts"></a>如何检测非活动用户帐户

通过评估**Microsoft 图形**API 的**符号 InActivity**资源类型公开的**上次SignInDateTime**属性，可以检测非活动帐户。 使用此属性，您可以为以下方案实现解决方案：

- **按名称搜索用户**：在此方案中，您可以按名称搜索特定用户，这使您能够评估最后一个 SignInDate：`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **按日期进行用户：** 在此方案中，您请求在指定日期之前使用最后一个 SignInDateTime 的用户列表：`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>需要了解的事项

本节列出了您需要了解的最后一个SignInDateTime属性的内容。

### <a name="how-can-i-access-this-property"></a>如何访问此属性？

**最后一个SignInDateTime**属性由[微软图形REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph)的[符号不活动资源类型](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta)公开。   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>最后一个SignInDateTime属性是否可以通过获取 AzureAdUser cmdlet 提供？

不是。

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>访问属性需要哪个版本的 Azure AD？

您可以在 Azure AD 的所有版本中访问此属性。

### <a name="what-permission-do-i-need-to-read-the-property"></a>读取属性需要获得什么权限？

要读取此属性，您需要授予以下权限： 

- 审核日志.阅读。所有
- 组织.阅读。所有  


### <a name="when-does-azure-ad-update-the-property"></a>Azure AD 何时更新属性？

成功的每个交互式登录都会导致基础数据存储的更新。 通常，成功的登录将在 10 分钟内显示在相关的登录报告中。
 

### <a name="what-does-a-blank-property-value-mean"></a>空白属性值是什么意思？

要生成最后一个 SignInDateTime 时间戳，您需要成功登录。 由于最后一个SignInDateTime属性是一个新功能，因此，如果出现以下情况，则最后一个SignInDateTime属性的值可以为空：

- 用户的最后一次成功登录是在此功能发布之前（2019 年 12 月 1 日）。
- 受影响的用户帐户从未用于成功登录。

## <a name="next-steps"></a>后续步骤

* [使用证书通过 Azure Active Directory 报告 API 获取数据](tutorial-access-api-with-certificates.md)
* [审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登录活动报告 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
