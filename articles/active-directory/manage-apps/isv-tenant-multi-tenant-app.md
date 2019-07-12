---
title: 创建多租户应用程序的 Azure 租户
description: 在与 Azure Active Directory 集成的独立软件供应商的指南
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
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659575"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>创建多租户应用程序的 Azure 租户  

若要提供对多租户应用程序的访问必须创建一个 Azure Active Directory 租户注册应用程序和启用的客户的标识联合身份验证。 请参阅[选择你的多租户应用程序的正确的联合身份验证协议](isv-choose-multi-tenant-federation.md)。 此租户将允许您以类似于你的客户的 Azure AD 环境的环境中测试你的应用程序和联合身份验证。

## <a name="costs-of-hosting-a-multi-tenant-application"></a>托管多租户应用程序的成本

Azure Active Directory 是在三个 Sku、 免费、 基本和高级版中可用。 [请参阅详细的功能比较](https://azure.microsoft.com/pricing/details/active-directory/)。

您可以创建你的 Azure 订阅和 Azure active directory 免费，并使用基本功能。

## <a name="create-your-tenant"></a>创建你的租户

1. 创建你的租户。 请参阅[设置开发环境](../develop/quickstart-create-new-tenant.md)。

2. 启用和测试应用程序中，对单一登录访问

   a. **OIDC 或 Oath 的应用程序**，[注册应用程序](../develop/quickstart-register-app.md)作为多租户应用程序。 任何组织的目录和支持帐户类型中的个人 Microsoft 帐户选项中选择的帐户

   b. **SAML 和 WS-次进纸-基于应用程序**，则[配置基于 SAML 的单一登录的登录](configure-single-sign-on-non-gallery-applications.md)通用 SAML 模板使用 Azure AD 中的应用程序。

此外可以[单租户应用程序转换为多租户](../develop/howto-convert-app-to-be-multi-tenant.md)如有必要。

## <a name="next-steps"></a>后续步骤

[将 SSO 集成应用程序中](isv-sso-content.md)
