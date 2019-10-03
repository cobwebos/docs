---
title: 调用 Web API 的守护程序应用（移到生产环境）- Microsoft 标识平台
description: 了解如何构建调用 Web API 的守护程序应用（移到生产环境）
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
ms.openlocfilehash: c509e061c43c81f72682fb428529a8e72b34066a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056321"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>调用 Web API 的守护程序应用 - 移到生产环境

了解如何获取并使用用于服务到服务调用的令牌以后，即可学习如何将应用移到生产环境。

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>部署 - 多租户守护程序应用示例

如果你是 ISV，创建可以在多个租户中运行的守护程序应用程序，则需确保租户管理员执行以下操作：

- 为应用程序预配服务主体
- 授予对应用程序的许可

需向客户解释如何执行这些操作。 有关详细信息，请参阅[请求整个租户的许可](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

下面是一些介绍详细信息的链接：

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- 尝试[使用应用的标识获取令牌并从控制台应用中调用 Microsoft Graph API](./quickstart-v2-netcore-daemon.md)快速入门（如果尚未这样做）。
- 以下内容的参考文档：
  - 实例化 [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - 调用 [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- 其他示例/教程：
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) 提供一个简单的 .NET Core 守护程序控制台应用程序，该应用程序显示某个租户的用户在查询 Microsoft Graph。

    ![拓扑](media/scenario-daemon-app/daemon-app-sample.svg)

    同一示例还通过证书演示了变体。

    ![拓扑](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) 具有一个 ASP.NET MVC Web 应用程序，该应用程序使用应用程序的标识来同步 Microsoft Graph 的数据，而不是代表用户来同步。 此示例还演示了管理员许可过程。

    ![拓扑](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python 目前为公开预览版。
有关详细信息，请参阅[MSAL Python 的存储库示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample)。

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j （MSAL。Java）目前为公共预览版。 有关详细信息，请参阅 [MSAL Java 在存储库中的示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)。

---
