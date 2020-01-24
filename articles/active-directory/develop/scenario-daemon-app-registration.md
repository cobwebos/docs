---
title: 注册用于调用 web Api 的守护程序应用-Microsoft 标识平台 |Microsoft
description: 了解如何构建一个可调用 web Api-应用注册的守护程序应用
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
ms.openlocfilehash: 1ba22226c75358a9d08671e1665e15a33810298a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702278"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>用于调用 web Api 的守护程序应用-应用注册

对于后台应用程序，以下是注册应用程序时需要了解的内容。

## <a name="supported-account-types"></a>支持的帐户类型

如果后台应用程序仅在 Azure AD 租户中有效，则创建应用程序时，需要选择：

- **仅限此组织目录中的帐户**。 这是最常见的情况，因为后台应用程序通常由业务线（LOB）开发人员编写。
- 或**帐户**。 如果你是为客户提供实用工具工具的 ISV，则可以选择此选项。 你将需要客户的租户管理员批准它。

## <a name="authentication---no-reply-uri-needed"></a>身份验证-无需答复 URI

如果你的机密客户端应用程序**仅**使用客户端凭据流，则不需要注册答复 URI。 对于应用程序配置/构造，不需要此方法。 客户端凭据流不使用它。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 权限-应用权限和管理员同意

后台应用程序只能请求对 Api 的应用程序权限（而不是委托的权限）。 在应用程序注册的 " **API 权限**" 页中，选择 "**添加权限**" 并选择 API 系列后，选择 "**应用程序权限**"，然后选择你的权限

![应用权限和管理员同意](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 要调用的 Web API 需要定义**应用程序权限（应用角色）** ，而不是委托的权限。 有关如何公开此类 API 的详细信息，请参阅[受保护的 WEB api：应用注册-当你的 WEB API 被后台程序应用调用时](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)

后台应用程序要求将租户管理员预先同意调用 web API 的应用程序。 在同一个提供此许可 **API 的权限** 页上，通过租户管理员选择 **授予管理员同意 *我们的组织***

如果你是构建多租户应用程序的 ISV，则需要检查[多租户后台程序应用的部署案例](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps)。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [后台应用程序-应用程序代码配置](./scenario-daemon-app-configuration.md)
