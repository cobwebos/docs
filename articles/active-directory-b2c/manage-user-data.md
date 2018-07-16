---
title: 在 Azure Active Directory B2C 中管理用户数据 | Microsoft Docs
description: 了解如何在 Azure AD B2C 中删除或导出用户数据。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 202ee9da94fc93e7301c29b62dc61ad443685807
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448905"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中管理用户数据

 本文介绍如何使用 [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) 提供的操作在 Azure Active Directory (Azure AD) B2C 中管理用户数据。 管理用户数据包括从审核日志中删除或导出数据。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>删除用户数据

用户数据存储在 Azure AD B2C 目录，且位于审核日志中。 所有用户审核数据都会在 Azure AD B2C 中保留 30 天。 如果在该 30 天期限内想要删除用户数据，可以使用[删除用户](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser)操作。 对于数据可能驻留的每个 Azure AD B2C 租户，删除操作是必需的。 

Azure AD B2C 中的每个用户都分配有一个对象 ID。 对象 ID 可为你提供明确标识符，以用于删除 Azure AD B2C 中的用户数据。 对象 ID 可以是其他服务（如财务、营销和客户关系管理数据库）中有用的相关标识符，具体取决于你的体系结构。 

在对 Azure AD B2C 进行身份验证的过程中获取用户的对象 ID，这是最准确的方式。 如果使用其他方法从用户收到有效的数据请求，可能需要使用脱机处理（例如通过客户服务支持代理进行搜索）查找用户并记下关联的对象 ID。 

下面的示例演示了可能的数据删除流程：

1. 用户进行登录，然后选择“删除我的数据”。
2. 从应用程序中选择删除应用程序管理部分中的数据。
3. 应用程序会强制对 Azure AD B2C 进行身份验证。 Azure AD B2C 反过来向应用程序提供具有用户对象 ID 的令牌。 
4. 应用程序接收该令牌并使用对象 ID 通过调用 Azure AD Graph API 删除用户数据。 Azure AD Graph API 删除用户数据，并返回状态代码“200 正常”。
5. 根据需要，应用程序使用对象 ID 或其他标识符在其他组织系统中安排删除用户数据。
6. 应用程序确认数据删除，并向用户提供后续步骤。

## <a name="export-customer-data"></a>导出客户数据

从 Azure AD B2C 导出客户数据的过程类似于删除过程。

Azure AD B2C 用户数据仅限于：

- **存储在 Azure Active Directory 中的数据**：可使用对象 ID 或任何登录名（如电子邮件地址或用户名）在 Azure AD B2C 身份验证用户旅程中检索数据。 
- **特定于用户的审核事件报告** - 可使用对象 ID 对数据编制索引。

在下面的导出数据流示例中，由应用程序执行的所述步骤也能由目录中的后端进程或具有管理员角色的用户执行：

1. 用户登录到应用程序。 如果需要，Azure AD B2C 将使用 Azure 多重身份验证强制执行身份验证。
2. 应用程序使用用户凭据来调用 Azure AD Graph API 操作，以检索用户属性。 Azure AD Graph API 采用 JSON 格式提供属性数据。 可以将 ID 令牌内容设置为包括用户的所有个人数据，具体取决于架构。
3. 应用程序检索用户审核活动。 Azure AD Graph API 向应用程序提供事件数据。
4. 应用程序聚合数据，并使其可供用户使用。

## <a name="next-steps"></a>后续步骤

- 如需了解用户如何访问应用程序，请参阅[管理用户访问权限](manage-user-access.md)。




