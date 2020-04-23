---
title: 使用 SCIM、Microsoft 图形和 Azure AD 预配服务来预配用户并使用所需的数据来丰富应用程序 |微软文档
description: 使用 SCIM 和 Microsoft 图形一起为用户提供和丰富应用程序所需的数据。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087616"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>将 SCIM 和 Microsoft 图形一起使用，为用户提供所需的数据并丰富应用程序

**目标受众：** 本文档面向构建与 Azure AD 集成的应用程序的开发人员。 对于希望集成现有应用程序（如 Zoom、ServiceNow 和 DropBox）的其他应用程序，您可以跳过此内容并查看应用程序特定的[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)。 

**常见方案**

> [!div class="checklist"]
> * 在我的应用程序中自动创建用户
> * 当用户不再具有访问权限时，自动从我的应用程序中删除他们
> * 将我的应用程序与多个标识提供程序集成以进行预配
> * 使用来自 Microsoft 服务（如 Sharepoint、Outlook 和 Office）的数据丰富应用程序。
> * 在 Azure AD 和活动目录中自动创建、更新和删除用户和组

![SCIM 图形决策树](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>方案 1：在我的应用中自动创建用户
如今，每当有人需要访问或定期上传 CSV 文件时，IT 管理员都会在我的应用程序中手动创建用户帐户。 这个过程对于客户来说非常耗时，并减慢了我的应用程序的采用速度。 我只需要基本[用户](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)信息，如姓名、电子邮件和用户原则名称，以创建用户。 此外，我的客户使用各种 IdP，我没有资源来维护与每个 IdP 的同步引擎和自定义集成。 

**建议**： 支持符合 SCIM[的 /用户](https://aka.ms/scimreferencecode)终结点。 您的客户将能够轻松地使用此终结点与 Azure AD 预配服务集成，并在需要访问时自动创建用户帐户。 您可以构建终结点一次，它将与所有 IdP 兼容，而无需维护同步引擎。 请查看下面的示例请求，了解如何创建用户。

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
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>方案 2：自动从我的应用中删除用户
使用我的应用程序的客户以安全性为中心，并且具有在员工不再需要帐户时删除帐户的治理要求。 如何自动从应用程序取消预配？

**建议：** 支持符合 SCIM/用户终结点。 Azure AD 预配服务将发送请求以禁用和删除用户不应再具有访问权限。 我们建议支持禁用和删除用户。 有关禁用和删除请求的外观，请参阅下面的示例。 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>方案 3：自动管理应用中的组成员身份
我的应用程序依赖于组来访问各种资源，客户希望重用他们在 Azure AD 中具有的组。 如何从 Azure AD 导入组，并在成员资格更改时更新它们？  

**建议：** 支持符合 SCIM 的 /组[终结点](https://aka.ms/scimreferencecode)。 Azure AD 预配服务将负责在应用程序中创建组和管理成员身份更新。 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>方案 4：使用来自 Microsoft 服务（如团队、Outlook 和 OneDrive）的数据丰富我的应用。
我的应用程序内置于 Microsoft Teams 中，并且依赖于消息数据。 此外，我们还在 OneDrive 中存储用户文件。 如何使用这些服务和整个 Microsoft 的数据来丰富应用程序？

**建议：**[Microsoft 图形](https://docs.microsoft.com/graph/)是访问 Microsoft 数据的切入点。 每个工作负载都使用所需的数据公开 API。 Microsoft 图形可以与上述方案的[SCIM 预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)一起使用。 您可以使用 SCIM 将基本用户属性预配到应用程序中，同时调用图形以获取所需的任何其他数据。 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>方案 5：跟踪 Microsoft 服务中的更改，如团队、Outlook 和 Azure AD。
我需要能够跟踪对团队和 Outlook 消息的更改，并实时响应它们。 如何将这些更改推送到我的应用程序中？

**建议：** Microsoft 图形为各种资源提供[更改通知](https://docs.microsoft.com/graph/webhooks)和更改跟踪。 请注意更改通知的以下限制：
- 如果事件接收器确认事件，但由于任何原因未能对事件执行操作，则事件可能会丢失
- 如果事件接收器确认事件，但由于任何原因未能对事件执行操作，则事件可能会丢失
- 更改通知并不总是包含[资源数据](https://docs.microsoft.com/graph/webhooks-with-resource-data)原因，开发人员通常使用更改通知以及同步方案的更改跟踪。 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>方案 6：在 Azure AD 中预配用户和组。
我的应用程序创建有关客户在 Azure AD 中需要的用户的信息。 这可能是一个 HR 应用程序，而不是管理招聘、为用户创建电话号码的通信应用，或者生成在 Azure AD 中有价值的数据的其他应用。 如何使用该数据填充 Azure AD 中的用户记录？ 

**推荐**Microsoft 图形公开 /用户和 /组终结点，您可以与今天集成以预配用户到 Azure AD 中。 请注意，Azure 活动目录不支持将这些用户重新写入活动目录。 

> [!NOTE]
> Microsoft 具有一种调配服务，可从 HR 应用程序（如工作日和成功因素）中提取数据。 这些集成由 Microsoft 构建和管理。 对于将新的 HR 应用程序载入我们的服务，您可以在[UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests)上申请。 

## <a name="related-articles"></a>相关文章

- [查看同步 Microsoft 图形文档](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [将自定义 SCIM 应用与 Azure AD 集成](use-scim-to-provision-users-and-groups.md)
