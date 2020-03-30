---
title: 为多租户应用程序启用自动用户预配 - Azure AD
description: 支持自动配置的独立软件供应商指南
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522387"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>为多租户应用程序启用自动用户预配

自动用户预配是自动创建、维护和删除目标系统中的用户标识（如软件即服务应用程序）的过程。

## <a name="why-enable-automatic-user-provisioning"></a>为什么要启用自动用户预配？

在用户首次登录需要用户预配之前，需要用户记录在应用程序中存在的应用程序。 作为服务提供商，您有优势，客户也受益匪浅。

### <a name="benefits-to-you-as-the-service-provider"></a>作为服务提供商，您受益匪浅

* 通过使用 Microsoft 标识平台提高应用程序的安全性。

* 减少实际和感知的客户努力来采用您的应用程序。

* 通过使用基于跨域身份管理系统 （SCIM） 的系统进行预配，降低与多个标识提供程序 （IdP） 集成以自动预配用户的成本。

* 通过提供丰富的日志来帮助客户解决用户预配问题，从而降低支持成本。

* 提高 Azure [AD 应用库中](https://azuremarketplace.microsoft.com/marketplace/apps)应用程序的可见性。

* 在应用教程页面中获取优先级列表。

### <a name="benefits-to-your-customers"></a>给客户带来的好处

* 通过自动删除更改角色或将组织留给应用程序的用户对应用程序的访问，提高安全性。

* 通过避免与手动预配相关的人为错误和重复工作，简化应用程序的用户管理。

* 降低托管和维护定制开发资源调配解决方案的成本。

## <a name="choose-a-provisioning-method"></a>选择预配方法

Azure AD 提供了多个集成路径，可为应用程序启用自动用户预配。

* [Azure AD 预配服务](../app-provisioning/user-provisioning.md)管理用户从 Azure AD 预配和从应用程序（出站预配）和从应用程序到 Azure AD（入站预配）的预配和取消预配。 该服务连接到应用程序提供的跨域标识管理系统 （SCIM） 用户管理 API 终结点。

* 使用 Microsoft 图形时，应用程序通过查询[Microsoft 图形](https://docs.microsoft.com/graph/)API 来管理从 Azure AD 到应用程序的用户和组入站和出站预配。

* 如果应用程序使用 SAML 进行联合，则可以启用安全断言标记语言（即时间（SAML JIT）用户预配。 它使用 SAML 令牌中发送的声明信息来预配用户。

为了帮助确定要用于应用程序的集成选项，请参阅高级比较表，然后查看有关每个选项的更多详细信息。

| 自动预配启用或增强的功能| Azure AD 预配服务 （SCIM 2.0）| 微软图形 API （OData v4.0）| SAML JIT |
|---|---|---|---|
| Azure AD 中的用户和组管理| √| √| 仅限用户 |
| 管理从本地活动目录同步的用户和组| √*| √*| 仅限用户* |
| 在预配访问 O365 数据（团队、SharePoint、电子邮件、日历、文档等）期间访问超出用户和组的数据| X+| √| X |
| 根据业务规则创建、读取和更新用户| √| √| √ |
| 根据业务规则删除用户| √| √| X |
| 管理 Azure 门户中所有应用程序的自动用户预配| √| X| √ |
| 支持多个标识提供程序| √| X| √ |
| 支持客户帐户 （B2B）| √| √| √ |
| 支持非企业帐户 （B2C）| X| √| √ |

<sup>*</sup>• 需要 Azure AD 连接设置才能将用户从 AD 同步到 Azure AD。  
<sup>+</sup >• 使用 SCIM 进行预配并不妨碍您将应用程序与 MIcrosoft 图形集成用于其他目的。

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 预配服务 （SCIM）

Azure AD 预配服务使用[SCIM，](https://aka.ms/SCIMOverview)这是许多标识提供程序 （IdP） 以及应用程序（例如 Slack、G Suite、Dropbox）支持的预配的行业标准。 如果要支持除 Azure AD 之外的 IdP，我们建议您使用 Azure AD 预配服务，因为任何符合 SCIM 的 IdP 都可以连接到 SCIM 终结点。 构建一个简单的 /User 终结点，可以启用预配，而无需维护自己的同步引擎。 

有关 Azure AD 预配服务用户 SCIM 的详细信息，请参阅： 

* [了解有关 SCIM 标准的更多](https://aka.ms/SCIMOverview)

* [使用跨域标识管理系统 (SCIM) 将用户和组从 Azure Active Directory 自动预配到应用程序](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [了解 Azure AD SCIM 实现](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>用于预配的微软图形

使用 Microsoft 图形进行预配时，可以访问图形中提供的所有富用户数据。 除了用户和组的详细信息外，您还可以获取其他信息，如用户的角色、经理和直接下属、拥有和注册的设备，以及[Microsoft 图形](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)中提供的数百个其他数据片段。 

超过 1500 万个组织和 90% 的财富 500 强公司使用 Azure AD，同时订阅 Microsoft 云服务，如 Office 365、Microsoft Azure、企业移动套件或 Microsoft 365。 您可以使用 Microsoft Graph 将应用与管理工作流（如员工入职（和终止）、配置文件维护等集成。 

了解有关使用 Microsoft 图形进行预配的更多详细信息：

* [微软图形主页](https://developer.microsoft.com/graph)

* [Microsoft Graph 概述](https://docs.microsoft.com/graph/overview)

* [微软图形概述](https://docs.microsoft.com/graph/auth/)

* [开始使用微软图形](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>使用 SAML JIT 进行预配

如果只想在首次登录到应用程序时预配用户，并且不需要自动取消预配用户，SAML JIT 是一个选项。 您的应用程序必须支持 SAML 2.0 作为联合协议才能使用 SAML JIT。

SAML JIT 使用 SAML 令牌中的声明信息在应用程序中创建和更新用户信息。 客户可以根据需要在 Azure AD 应用程序中配置这些必需的声明。 有时需要从应用程序端启用 JIT 预配，以便客户可以使用此功能。 SAML JIT 可用于创建和更新用户，但不能删除或停用应用程序中的用户。

## <a name="next-steps"></a>后续步骤

* [为应用程序启用单一登录](../manage-apps/isv-sso-content.md)

* [提交您的应用程序列表](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)，并与 Microsoft 合作，在 Microsoft 网站上创建文档。

* [加入微软合作伙伴网络（免费），并创建您的市场计划](https://partner.microsoft.com/en-us/explore/commercial)。
