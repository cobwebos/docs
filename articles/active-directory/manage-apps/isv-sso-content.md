---
title: 为多租户应用程序启用 SSO
description: 独立软件供应商与 Azure 活动目录集成指南
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795176"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>为多租户应用程序启用单一登录  

当您通过购买或订阅提供应用程序供其他公司使用时，可以将应用程序提供给其自己的 Azure 租户中的客户。 这称为创建多租户应用程序。 有关此概念的概述，请参阅[Azure 中的多租户应用程序](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)和[Azure 活动目录中的租户](../develop/single-and-multi-tenant-apps.md)。

## <a name="what-is-single-sign-on"></a>什么是单一登录

当用户使用 Azure 活动目录和其他标识登录到应用程序时，单一登录 （SSO） 增加了安全性和便利性。 启用 SSO 时，用户无需输入单独的凭据即可访问该应用程序。 有关单一登录的完整说明。 [请参阅 Azure 活动目录中的应用程序单一登录](what-is-single-sign-on.md)。

## <a name="why-enable-single-sign-on-in-your-application"></a>为什么在应用程序中启用单一登录？

在多租户应用程序中启用 SSO 有许多优点。 当您为应用程序启用 SSO 时：

* 您的应用程序可以列在 Azure 应用商店中，其中数百万使用 Azure 活动目录的组织可以发现你的应用。
  * 使客户能够使用 Azure AD 快速配置应用程序。

* 您的应用程序可以在 Office 365 应用库、Office 365 应用启动器和 Microsoft 搜索中Office.com

* 您的应用程序可以使用 Microsoft 图形 REST API 访问可从 Microsoft 图形获取的数据，从而提高用户工作效率。

* 通过让客户更容易地降低支持成本。
  * 与 Azure AD 团队为我们共同客户共同制作的特定于应用程序的文档，可简化采用。
  * 如果启用了一键式 SSO，则客户的 IT 管理员不必了解如何配置应用程序以便在其组织中使用。

* 您可以为客户提供完全管理其员工和来宾身份的身份验证和授权的能力。

  * 将所有客户管理和合规责任交给这些身份的客户所有者。

  * 为特定标识提供商、组或用户启用或禁用 SSO 以满足其业务需求。

* 提高您的可上市性和可采用性。 许多大型组织要求（或渴望）其员工在所有应用程序中拥有无缝的 SSO 体验。 使 SSO 变得简单非常重要。

* 您可以减少最终用户的摩擦，这可能会增加最终用户的使用并增加您的收入。

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>如何在已发布的应用程序中启用单一登录

1. [为多租户应用程序选择正确的联合身份验证协议](isv-choose-multi-tenant-federation.md)。
1. 在应用程序中实现 SSO
   - 有关[身份验证模式的指导](../develop/v2-app-types.md)
   - 有关 OIDC 和 OAuth 协议的[Azure 活动目录代码示例](../develop/sample-v2-code.md)
1. [创建 Azure 租户](isv-tenant-multi-tenant-app.md)并测试应用程序
1. [在您的网站上创建和发布 SSO 文档](isv-create-sso-documentation.md)。
1. [提交您的应用程序列表](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)，并与 Microsoft 合作，在 Microsoft 网站上创建文档。
1. [加入微软合作伙伴网络（免费），并创建您的市场计划](https://partner.microsoft.com/en-us/explore/commercial#gtm)。
