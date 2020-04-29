---
title: 如何管理 Azure AD 中的非活动用户帐户 |Microsoft Docs
description: 了解如何检测和处理已过时 Azure AD 中的用户帐户
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886035"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>如何：管理 Azure AD 中的非活动用户帐户

在大型环境中，当员工离开组织时，不会始终删除用户帐户。 作为 IT 管理员，你希望检测并处理这些过时的用户帐户，因为它们会带来安全风险。

本文介绍了在 Azure AD 中处理过时用户帐户的方法。 

## <a name="what-are-inactive-user-accounts"></a>什么是非活动用户帐户？

非活动帐户是组织成员不再需要的用户帐户即可访问资源。 非活动帐户的一个密钥标识符是指在*一段*时间内登录到您的环境时没有使用这些帐户。 由于非活动帐户与登录活动相关联，因此你可以使用最近一次登录的时间戳来检测它们。 

此方法的难题是定义在环境情况下，*一段*时间内的内容。 例如，用户可能无法在*一段*时间内登录到环境，因为他们休假。 在为非活动用户帐户定义增量时，需要考虑并非登录到环境的所有合理原因。 在许多组织中，非活动用户帐户的增量为90到180天。 

最后一次成功登录可让用户更深入地了解访问资源的需求。  它有助于确定是否仍需要组成员身份或应用访问权限，或者是否可以将其删除。 对于外部用户管理，你可以了解外部用户在租户内是否仍处于活动状态，或是否应进行清理。 

    
## <a name="how-to-detect-inactive-user-accounts"></a>如何检测非活动用户帐户

通过评估**Microsoft Graph** API 的**signInActivity**资源类型公开的**lastSignInDateTime**属性来检测非活动帐户。 使用此属性，可以为以下方案实现解决方案：

- **按名称的用户**：在此方案中，按名称搜索特定的用户，这使你能够评估 lastSignInDate：`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **按日期**列出的用户：在此方案中，你请求的用户列表早于指定日期 lastSignInDateTime：`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>须知内容

本部分列出了需要了解的有关 lastSignInDateTime 属性的内容。

### <a name="how-can-i-access-this-property"></a>如何访问此属性？

**LastSignInDateTime**属性由[Microsoft Graph REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph)的[signInActivity 资源类型](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta)公开。   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>LastSignInDateTime 属性是否可通过 Get-azureaduser cmdlet 获取？

不能。

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>访问属性需要哪些版本的 Azure AD？

可以在 Azure AD 的所有版本中访问此属性。

### <a name="what-permission-do-i-need-to-read-the-property"></a>读取属性需要哪些权限？

若要读取此属性，需要授予以下权限： 

- AuditLogs
- 组织。全部  


### <a name="when-does-azure-ad-update-the-property"></a>何时 Azure AD 更新属性？

成功的每个交互式登录都会导致更新基础数据存储。 通常，在10分钟内，会在相关登录报告中显示成功的登录。
 

### <a name="what-does-a-blank-property-value-mean"></a>空属性值的含义是什么？

若要生成 lastSignInDateTime 时间戳，你需要成功登录。 由于 lastSignInDateTime 属性是一项新功能，因此，在以下情况下，lastSignInDateTime 属性的值可能为空：

- 用户的最后一次成功登录在此功能发布之前发生（12月 1 2019 日）。
- 受影响的用户帐户永远不会用于成功的登录。

## <a name="next-steps"></a>后续步骤

* [使用证书通过 Azure Active Directory 报告 API 获取数据](tutorial-access-api-with-certificates.md)
* [审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登录活动报告 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
