---
title: 为多租户应用程序创建 Azure 租户
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812615"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>为多租户应用程序创建 Azure 租户  

要提供对多租户应用程序的访问权限，必须创建 Azure 活动目录租户来注册应用程序并启用客户标识的联合。 请参阅[为多租户应用程序选择正确的联合身份验证协议](isv-choose-multi-tenant-federation.md)。 此租户将允许您在类似于客户 Azure AD 环境的环境中测试应用程序和联合体。

## <a name="costs-of-hosting-a-multi-tenant-application"></a>托管多租户应用程序的成本

Azure 活动目录在多个版本中可用。 [请参阅详细的特征比较](https://azure.microsoft.com/pricing/details/active-directory/)。

您可以免费创建 Azure 订阅和 Azure 活动目录，并使用基本功能。

## <a name="create-your-tenant"></a>创建租户

1. 创建租户。 请参阅[设置开发环境](../develop/quickstart-create-new-tenant.md)。

2. 启用和测试对应用程序的单一登录访问权限，

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **对于 OIDC 或 Oath 应用程序**，[将应用程序注册](../develop/quickstart-register-app.md)为多租户应用程序。 在"支持帐户类型"中选择任何组织目录中的帐户和个人 Microsoft 帐户选项

   b.保留“数据库类型”设置，即设置为“共享”。 **对于基于 SAML 和 WS-Fed 的应用程序**，使用 Azure AD 中的通用 SAML 模板[配置基于 SAML 的单一登录](configure-single-sign-on-non-gallery-applications.md)应用程序。

如有必要，还可以[将单租户应用程序转换为多租户](../develop/howto-convert-app-to-be-multi-tenant.md)。

## <a name="next-steps"></a>后续步骤

[将 SSO 集成到应用程序中](isv-sso-content.md)
