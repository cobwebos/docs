---
title: 调用 Web API 的守护程序应用（应用注册）- Microsoft 标识平台
description: 了解如何构建调用 Web API 的守护程序应用 - 应用注册
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f590010a655fb01529a4a59b5540cc03068f2b8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056478"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>调用 Web API 的守护程序应用 - 应用注册

对于守护程序应用程序，在注册该应用时需了解下面这些内容。

## <a name="supported-account-types"></a>受支持的帐户类型

由于守护程序应用程序只能用在 Azure AD 租户中，因此在创建该应用程序时，需选择：

- **仅此组织目录中的帐户**。 此选择是最常见的，因为守护程序应用程序通常是由业务线 (LOB) 开发人员编写的。
- 或者**任何组织目录中的帐户**。 如果你是 ISV，需向客户提供实用程序工具，则可进行此选择。 需要获得客户的租户管理员批准。

## <a name="authentication---no-reply-uri-needed"></a>身份验证 - 不需回复 URI

如果机密客户端应用程序**仅**使用客户端凭据流，则不需注册回复 URL。 应用程序配置/构造也不需要它。 客户端凭据流不使用它。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 权限 - 应用权限和管理员许可

守护程序应用程序只能请求针对 API 的应用程序权限（非委托权限）。 在应用程序注册的“API 权限”页中，在选择“添加权限”并选择 API 系列以后，请在选择“应用程序权限”后再选择权限

![应用权限和管理员许可](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

守护程序应用程序需要获得租户管理员的预先许可，然后该应用程序才能调用 Web API。 在同一个提供此许可 **API 的权限** 页上，通过租户管理员选择 **授予管理员同意 *我们的组织***

如果你是构建多租户应用程序的 ISV，则需查看[部署 - 多租户守护程序应用的示例](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps)段落。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [守护程序应用 - 应用代码配置](./scenario-daemon-app-configuration.md)
