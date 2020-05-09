---
title: 使用 SCIM、Microsoft Graph 和 Azure AD 预配用户，并使用数据丰富应用
description: 同时使用 SCIM 和 Microsoft Graph 来预配用户，并使用所需的数据来丰富应用程序。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: mimart
ms.reviewer: arvinh, celested
ms.openlocfilehash: 0b2c8e6bb71e00ccb6eda33ecb1b087d09ce5de7
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626184"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>同时使用 SCIM 和 Microsoft Graph 来预配用户，并使用所需的数据来丰富应用程序

**目标受众：** 本文面向的是开发人员构建与 Azure Active Directory （Azure AD）相集成的应用程序的目标。 如果你想要使用已与 Azure AD 集成的应用程序（例如缩放、ServiceNow 和 DropBox），则可以跳过此文章并查看应用程序特定[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)，或查看[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)。

**常见方案**

Azure AD 提供了一种现成的服务来进行预配，并提供一个可扩展的平台，用于生成应用程序。 决策树概述了开发人员如何使用[SCIM](https://aka.ms/scimoverview)和[Microsoft Graph](https://docs.microsoft.com/graph/overview)自动执行预配。 

> [!div class="checklist"]
> * 在我的应用程序中自动创建用户
> * 当用户不再需要访问时，自动从我的应用程序中删除用户
> * 集成应用程序与多个标识提供程序以进行预配
> * 利用来自 Microsoft 服务（如团队、Outlook 和 Office）的数据丰富应用程序。
> * 在 Azure AD 和 Active Directory 中自动创建、更新和删除用户和组

![SCIM 图决策树](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>方案1：在我的应用程序中自动创建用户
如今，IT 管理员通过手动创建用户帐户或定期将 CSV 文件上传到我的应用程序来设置用户。 对于客户来说，此过程非常耗时，并使应用程序的应用速度变慢。 我只需要基本用户信息，如名称、电子邮件和 userPrincipalName 来创建用户。 

**建议**： 
* 如果你的客户使用各种 Idp，并且你不希望维护同步引擎以与每个同步引擎集成，则支持 SCIM 相容[/Users](https://aka.ms/scimreferencecode)终结点。 你的客户将能够轻松地使用此终结点与 Azure AD 预配服务集成，并在用户需要访问时自动创建用户帐户。 你可以一次生成终结点，并且它将与所有 Idp 兼容。 查看下面的示例请求，了解如何使用 SCIM 创建用户。
* 如果需要在用户对象上找到用户数据，在 Azure AD 中找到用户数据，并在 Microsoft 的其他数据中发现用户数据，请考虑生成用于用户预配的 SCIM 终结点，并调用 Microsoft Graph 以获取其余数据。 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>方案2：自动从我的应用删除用户
使用 "我的应用程序" 的客户具有安全重点，并在员工不再需要帐户时，可以使用这些帐户来删除帐户。 如何从我的应用程序自动取消预配？

**建议：** 支持符合 SCIM 标准的/Users 终结点。 如果用户不应再访问，Azure AD 预配服务会将请求发送到 "禁用" 和 "删除"。 建议同时支持禁用和删除用户。 请参阅下面的示例，了解禁用和删除请求的外观。 

禁用用户
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
删除用户
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>方案3：在我的应用中自动管理组成员身份
我的应用程序依赖于组来访问各种资源，客户希望重复使用他们在 Azure AD 的组。 如何从 Azure AD 导入组，并在成员身份改变时使其保持更新？  

**建议：** 支持符合 SCIM 标准的/Groups[终结点](https://aka.ms/scimreferencecode)。 Azure AD 预配服务将负责在你的应用程序中创建组和管理成员身份更新。 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>方案4：利用 Microsoft 服务（如团队、Outlook 和 OneDrive）中的数据丰富应用
我的应用程序内置在 Microsoft 团队中，并依赖于消息数据。 此外，我们还会在 OneDrive 中存储用户的文件。 如何利用这些服务和 Microsoft 提供的数据来丰富应用程序？

**建议：**[Microsoft Graph](https://docs.microsoft.com/graph/)是访问 Microsoft 数据的入口点。 每个工作负荷都向 Api 公开您需要的数据。 Microsoft graph 可与上述方案一起用于[SCIM 预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)。 可以使用 SCIM 将基本用户属性预配到应用程序中，同时调用 graph 以获取所需的任何其他数据。 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>方案5：跟踪 Microsoft 服务（如团队、Outlook 和 Azure AD）中的更改
我需要能够跟踪对团队和 Outlook 消息所做的更改，并实时对其做出反应。 如何将这些更改推送到我的应用程序？

**建议：** Microsoft Graph 提供各种资源的[更改通知](https://docs.microsoft.com/graph/webhooks)和[更改跟踪](https://docs.microsoft.com/graph/delta-query-overview)。 请注意更改通知的以下限制：
- 如果事件接收者确认了某个事件，但由于任何原因而无法采取措施，则可能会丢失该事件。
- 不保证按时间顺序接收更改的顺序。
- 出于上述原因，更改通知并不始终包含[资源数据](https://docs.microsoft.com/graph/webhooks-with-resource-data)，开发人员通常会将更改通知和更改跟踪与同步方案一起使用。 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>方案6：在 Azure AD 中设置用户和组
我的应用程序在 Azure AD 中创建客户需要的用户的相关信息。 这可能是一个 HR 应用程序，而不是管理聘用、为用户创建电话号码的通信应用程序或一些其他应用程序，用于生成在 Azure AD 中有价值的数据。 如何实现用该数据填充 Azure AD 中的用户记录？ 

**建议**Microsoft graph 公开了/Users 和/Groups 终结点，你可以将它们与今天集成，以便将用户预配到 Azure AD。 请注意，Azure Active Directory 不支持将这些用户写回 Active Directory。 

> [!NOTE]
> Microsoft 提供一项预配服务，用于从 HR 应用程序（如 Workday 和 SuccessFactors）提取数据。 这些集成由 Microsoft 构建和管理。 若要向我们的服务加入新的 HR 应用程序，你可以在[UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests)上请求它。 

## <a name="related-articles"></a>相关文章

- [查看同步 Microsoft Graph 文档](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [将自定义 SCIM 应用与 Azure AD 集成](use-scim-to-provision-users-and-groups.md)
