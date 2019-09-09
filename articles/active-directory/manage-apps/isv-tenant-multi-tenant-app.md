---
title: 为多租户应用程序创建 Azure 租户
description: 独立软件供应商与 Azure Active Directory 集成的指南
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812615"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>为多租户应用程序创建 Azure 租户  

若要提供对多租户应用程序的访问权限，必须创建 Azure Active Directory 租户来注册应用程序，并启用客户标识的联合身份验证。 请参阅[为多租户应用程序选择适当的联合身份验证协议](isv-choose-multi-tenant-federation.md)。 此租户可让你在与客户 Azure AD 环境相似的环境中测试应用程序和联合身份验证。

## <a name="costs-of-hosting-a-multi-tenant-application"></a>托管多租户应用程序的成本

Azure Active Directory 在多个版本中可用。 [请参阅详细功能比较](https://azure.microsoft.com/pricing/details/active-directory/)。

可以免费创建 Azure 订阅和 Azure active directory，并使用基本功能。

## <a name="create-your-tenant"></a>创建租户

1. 创建租户。 请参阅[设置开发环境](../develop/quickstart-create-new-tenant.md)。

2. 启用和测试对应用程序的单一登录访问，

   a. **对于 OIDC 或 Oath 应用**程序，请将[应用程序注册](../develop/quickstart-register-app.md)为多租户应用程序。 在 "受支持的帐户类型" 中选择 "任何组织目录和个人 Microsoft 帐户中的帐户" 选项

   b. **对于基于 saml 和 ws-federation 的应用程序**，可以使用 Azure AD 中的通用 SAML 模板来[配置基于 saml 的单一登录](configure-single-sign-on-non-gallery-applications.md)应用程序。

如果需要，还可以[将单租户应用程序转换为多租户应用程序](../develop/howto-convert-app-to-be-multi-tenant.md)。

## <a name="next-steps"></a>后续步骤

[在应用程序中集成 SSO](isv-sso-content.md)
