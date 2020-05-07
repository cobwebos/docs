---
title: 为多租户应用程序启用自动用户预配-Azure AD
description: 用于启用自动预配的独立软件供应商指南
services: active-directory
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.openlocfilehash: 3713b7a191c5c7c90e0e76bde25d1b1b3bf3700b
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593584"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>为多租户应用程序启用自动用户预配

自动用户预配是在目标系统中自动创建、维护和删除用户标识的过程，如软件即服务应用程序。

## <a name="why-enable-automatic-user-provisioning"></a>为什么要启用自动用户预配？

需要用户记录的应用程序在用户首次登录之前，应用程序需要用户预配。 作为服务提供商，你可以受益于你的客户。

### <a name="benefits-to-you-as-the-service-provider"></a>作为服务提供商的权益

* 使用 Microsoft 标识平台提高应用程序的安全性。

* 减少实际和感知的客户工作量以采用你的应用程序。

* 通过使用系统进行跨域标识管理（SCIM）的预配，降低与多个标识提供者（Idp）集成的成本以实现自动用户预配。

* 通过提供丰富的日志来帮助客户解决用户预配问题，降低支持成本。

* 提高应用程序在[Azure AD 应用库](https://azuremarketplace.microsoft.com/marketplace/apps)中的可见性。

* 获取应用教程页面中的优先顺序列表。

### <a name="benefits-to-your-customers"></a>向你的客户带来的好处

* 通过为更改角色或将组织留给应用程序的用户自动删除对应用程序的访问权限，提高安全性。

* 通过避免人为错误和与手动预配相关的重复工作，简化应用程序的用户管理。

* 降低托管和维护定制开发的预配解决方案的成本。

## <a name="choose-a-provisioning-method"></a>选择预配方法

Azure AD 提供了多个集成路径，以便为应用程序启用自动用户预配。

* [Azure AD 预配服务](../app-provisioning/user-provisioning.md)可管理用户对应用程序（出站预配）和从应用程序到 Azure AD （入站预配）的 Azure AD 的设置和取消预配。 服务将连接到应用程序提供的跨域标识管理（SCIM）用户管理 API 终结点的系统。

* 使用[Microsoft Graph](https://docs.microsoft.com/graph/)时，应用程序通过查询 Microsoft Graph API 来管理用户和组对 Azure AD 到应用程序的入站和出站预配。

* 如果你的应用程序使用 SAML 进行联合身份验证，则可以启用安全断言标记语言实时（SAML JIT）用户预配。 它使用在 SAML 令牌中发送的声明信息来预配用户。

若要帮助确定要用于应用程序的集成选项，请参阅高级比较表，然后查看每个选项的详细信息。

| 自动预配启用或增强的功能| Azure AD 预配服务（SCIM 2.0）| Microsoft Graph API （OData 4.0）| SAML JIT |
|---|---|---|---|
| Azure AD 中的用户和组管理| √| √| 仅用户 |
| 管理从本地 Active Directory 同步的用户和组| √*| √*| 仅用户 * |
| 在设置访问 O365 数据（团队、SharePoint、电子邮件、日历、文档等）期间访问用户和组之外的数据| X+| √| X |
| 基于业务规则创建、读取和更新用户| √| √| √ |
| 基于业务规则删除用户| √| √| X |
| 从 Azure 门户管理所有应用程序的自动用户预配| √| X| √ |
| 支持多个标识提供程序| √| X| √ |
| 支持来宾帐户（B2B）| √| √| √ |
| 支持非企业帐户（B2C）| X| √| √ |

<sup>*</sup>–要求安装程序将用户从 AD 同步到 Azure AD Azure AD Connect。  
<sup>+</sup >–使用 SCIM 进行设置不会阻止你将应用程序与 MIcrosoft Graph 集成以实现其他目的。

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 预配服务（SCIM）

Azure AD 预配服务使用[SCIM](https://aka.ms/SCIMOverview)，这是许多标识提供者（idp）和应用程序（例如，可宽比、g Suite、Dropbox）支持的预配的行业标准。 如果除了 Azure AD 之外还需要支持 Idp，则建议使用 Azure AD 预配服务，因为任何符合 SCIM 的 IdP 都可以连接到 SCIM 终结点。 生成简单的/User 终结点，你可以启用预配，而不必维护自己的同步引擎。 

有关 Azure AD 预配服务用户 SCIM 的详细信息，请参阅： 

* [了解有关 SCIM 标准的详细信息](https://aka.ms/SCIMOverview)

* [使用跨域标识管理系统 (SCIM) 将用户和组从 Azure Active Directory 自动预配到应用程序](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [了解 Azure AD SCIM 实现](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>用于预配的 Microsoft Graph

使用用于预配的 Microsoft Graph 时，可以访问图形中提供的所有丰富的用户数据。 除了用户和组的详细信息外，还可以获取其他信息，如用户的角色、经理和直接下属、拥有和注册的设备，以及[Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)中提供的数百个其他数据片段。 

15000000多家组织和90% 的财富500公司使用 Azure AD，同时订阅 Office 365、Microsoft Azure、企业移动性套件或 Microsoft 365 等 Microsoft 云服务。 你可以使用 Microsoft Graph 将你的应用与管理工作流（如员工加入（和终止）、配置文件维护等）集成。 

详细了解如何使用 Microsoft Graph 进行预配：

* [Microsoft Graph 主页](https://developer.microsoft.com/graph)

* [Microsoft Graph 概述](https://docs.microsoft.com/graph/overview)

* [Microsoft Graph 身份验证概述](https://docs.microsoft.com/graph/auth/)

* [Microsoft Graph 入门](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>使用 SAML JIT 进行预配

如果只想在首次登录应用程序时预配用户，而不需要自动取消对用户的设置，则 SAML JIT 是一个选项。 应用程序必须支持 SAML 2.0 作为联合身份验证协议，才能使用 SAML JIT。

SAML JIT 使用 SAML 令牌中的声明信息来创建和更新应用程序中的用户信息。 客户可根据需要在 Azure AD 应用程序中配置这些必需的声明。 有时需要从应用程序端启用 JIT 预配，以便客户可以使用此功能。 SAML JIT 对创建和更新用户很有用，但它不能删除或停用应用程序中的用户。

## <a name="next-steps"></a>后续步骤

* [为应用程序启用单一登录](../manage-apps/isv-sso-content.md)

* [提交你的应用程序列表](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)并与 microsoft 合作，以便在 microsoft 网站上创建文档。

* [加入 Microsoft 合作伙伴网络（免费）并创建你的走向市场计划](https://partner.microsoft.com/en-us/explore/commercial)。
