---
title: 为多租户应用程序启用 SSO
description: 独立软件供应商与 Azure active Directory 集成的指南
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fa2b82b89c7b9ce1e05062718f2c4d0c93197c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763272"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>为多租户应用程序启用单一登录  

当你通过购买或订阅向你的应用程序提供供其他公司使用的应用程序时，你可以让你的应用程序在其自己的 Azure 租户中提供给客户。 这称为创建多租户应用程序。 有关此概念的概述，请参阅[Azure 中的多租户应用程序](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)和 Azure Active Directory 中的[租户](../develop/single-and-multi-tenant-apps.md)。

## <a name="what-is-single-sign-on"></a>什么是单一登录

单一登录（SSO）在用户使用 Azure Active Directory 和其他标识登录到应用程序时，增加了安全性和便利性。 启用 SSO 后，用户无需输入单独的凭据即可访问该应用程序。 有关单一登录的完整说明。 [请参阅单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)。

## <a name="why-enable-single-sign-on-in-your-application"></a>为什么要在应用程序中启用单一登录？

在多租户应用程序中启用 SSO 有很多优点。 为应用程序启用 SSO 时：

* 应用程序可以在 Azure Marketplace 中列出，其中你的应用可通过使用 Azure Active Directory 的数百万组织发现。
  * 使客户能够快速配置 Azure AD 的应用程序。

* 你的应用程序可以在 Office 365 应用程序库、Office 365 应用程序启动器和 Office.com 上的 Microsoft 搜索中发现

* 应用程序可以使用 Microsoft Graph REST API 来访问用于驱动 Microsoft Graph 提供的用户工作效率的数据。

* 通过使客户更轻松，降低支持成本。
  * 特定于应用程序的文档与 Azure AD 团队 coproduced，让我们的共同客户更轻松地采用。
  * 如果启用一次单击 SSO，则客户的 IT 管理员无需了解如何配置应用程序以在其组织中使用。

* 为客户提供完全管理其员工和来宾身份身份验证和授权的功能。

  * 将所有帐户管理和合规性责任放入这些标识的客户所有者。

  * 为特定的标识提供者、组或用户提供启用或禁用 SSO 以满足其业务需求的能力。

* 增加 marketability 和 adoptability。 许多大型组织要求其员工在所有应用程序中都有无缝的 SSO 体验。 简化 SSO 非常重要。

* 减少最终用户的摩擦，这可能会增加最终用户的使用并增加收入。

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>如何在已发布的应用程序中启用单一登录

1. [为多租户应用程序选择适当的联合身份验证协议](isv-choose-multi-tenant-federation.md)。
1. 在应用程序中实现 SSO
   - 请参阅[身份验证模式指南](../develop/v2-app-types.md)
   - 请参阅适用于 OIDC 和 OAuth 协议的[Azure Active Directory 代码示例](../develop/sample-v2-code.md)
1. [创建 Azure 租户](isv-tenant-multi-tenant-app.md)并测试应用程序
1. [在站点上创建并发布 SSO 文档](isv-create-sso-documentation.md)。
1. [提交你的应用程序列表](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)并与 microsoft 合作，以便在 microsoft 网站上创建文档。
1. [加入 Microsoft 合作伙伴网络（免费）并创建你的走向市场计划](https://partner.microsoft.com/en-us/explore/commercial#gtm)。
