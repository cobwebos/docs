---
title: 注册调用 Web API 的守护程序应用 - Microsoft 标识平台 | Azure
description: 了解如何构建调用 Web API 的守护程序应用 - 应用注册
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885474"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>调用 Web API 的守护程序应用 - 应用注册

对于守护程序应用程序，在注册该应用时需了解下面这些内容。

## <a name="supported-account-types"></a>支持的帐户类型

守护程序应用程序只能在 Azure AD 租户中使用。 因此，在创建应用程序时，需要选择以下选项之一：

- **仅此组织目录中的帐户**。 此选择是最常见的，因为守护程序应用程序通常是由业务线 (LOB) 开发人员编写的。
- **任何组织目录中的帐户**。 如果你是 ISV，需向客户提供实用程序工具，则可进行此选择。 需要获得客户的租户管理员批准。

## <a name="authentication---no-reply-uri-needed"></a>身份验证 - 不需回复 URI

如果机密客户端应用程序仅  使用客户端凭据流，则不需注册回复 URI。 应用程序配置或构造不需要它。 客户端凭据流不使用它。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 权限 - 应用权限和管理员许可

守护程序应用程序只能请求针对 API 的应用程序权限（非委托权限）。 在应用程序注册的“API 权限”页上，  在选择“添加权限”并选择  API 系列以后，请在选择“应用程序权限”后再选择权限  。

![应用权限和管理员许可](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 要调用的 Web API 需要定义*应用程序权限（应用角色）* ，而不是委托的权限。 有关如何公开此类 API 的详细信息，请参阅[受保护的 Web API：应用注册 - 当守护程序应用调用 Web API 时](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)。

守护程序应用程序需要获得租户管理员的预先许可，然后该应用程序才能调用 Web API。 租户管理员在同一“API 权限”页中提供此许可，  只需选择“向我们的组织授予管理员许可”即可 

如果你是构建多租户应用程序的 ISV，则应阅读[部署 - 多租户守护程序应用的示例](scenario-daemon-production.md#deployment---multitenant-daemon-apps)部分。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [守护程序应用 - 应用代码配置](./scenario-daemon-app-configuration.md)
