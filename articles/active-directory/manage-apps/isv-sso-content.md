---
title: 为多租户应用程序启用 SSO
description: 在与 Azure active Directory 集成的独立软件供应商的指南
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
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795176"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>启用多租户应用程序的单一登录  

时提供你的应用程序以供其他公司通过购买或订阅，则你的应用程序向客户提供他们自己的 Azure 租户中。 这被称为创建多租户应用程序。 这一概念的概述，请参阅[在 Azure 中的多租户应用程序](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)并[在 Azure Active Directory 中的租户](../develop/single-and-multi-tenant-apps.md)。

## <a name="what-is-single-sign-on"></a>单一登录是什么

单一登录 (SSO) 时，添加的安全和方便用户登录到应用程序通过使用 Azure Active Directory 和其他标识。 当应用程序启用 SSO，用户无需输入单独的凭据来访问该应用程序。 有关的单一登录的完整说明。 [请参阅 Azure Active Directory 中的应用程序的单一登录](what-is-single-sign-on.md)。

## <a name="why-enable-single-sign-on-in-your-application"></a>为什么需要启用单一登录应用程序中？

有很多好处多租户应用程序中启用 SSO。 当为应用程序启用 SSO:

* 可以在您的应用程序是可检测到数以百万的组织使用 Azure Active Directory 在 Azure Marketplace 中列出你的应用程序。
  * 使客户能够快速配置与 Azure AD 应用程序。

* 在 Office 365 应用程序库，Office 365 应用启动器中，在 Microsoft Office.com 上搜索可检测到你的应用程序

* 你的应用程序可以使用 Microsoft Graph REST API 来访问数据的驱动器可从 Microsoft Graph 的用户工作效率。

* 通过简化你的客户降低支持成本。
  * 与我们共同客户缓动采用 Azure AD 团队 coproduced 特定于应用程序的文档。
  * 如果一次单击 SSO 已启用，客户的 IT 管理员无需了解如何在其组织中配置为使用应用程序。

* 提供给客户完全管理其员工和来宾身份的身份验证和授权的能力。

  * 将使用这些标识 customer 所有者的所有帐户管理和合规性责任。

  * 提供启用或禁用特定的标识提供程序 SSO、 组或用户来满足其业务需要。

* 您提高适销和 adoptability。 许多大型组织需要的 （或致力于将） 其员工跨所有应用程序获得无缝 SSO 体验。 SSO 可以轻松很重要。

* 减少最终用户阻力，这可以提高最终用户使用情况，并增加您的收入。

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>如何启用单一登录中发布的应用程序

1. [选择你的多租户应用程序的正确的联合身份验证协议](isv-choose-multi-tenant-federation.md)。
1. 在应用程序中实现 SSO
   - 请参阅[身份验证模式的指南](../develop/v2-app-types.md)
   - 请参阅[Azure active Directory 代码示例](../develop/sample-v2-code.md)OIDC 和 OAuth 协议
1. [创建你的 Azure 租户](isv-tenant-multi-tenant-app.md)和测试应用程序
1. [创建和发布你的站点上的 SSO 文档](isv-create-sso-documentation.md)。
1. [提交你的应用程序列表](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)和与 Microsoft 的合作伙伴在 Microsoft 的网站上创建文档。
1. [加入 Microsoft 合作伙伴网络 （免费） 并创建你的走向市场计划](https://partner.microsoft.com/en-us/explore/commercial#gtm)。
