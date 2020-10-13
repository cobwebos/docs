---
title: 将调用 Web API 的守护程序应用移到生产环境 - Microsoft 标识平台 | Azure
description: 了解如何将调用 Web API 的守护程序应用移到生产环境
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 72e6d0a8677f7a8175223b80541c99026a9eb16f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119158"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>调用 Web API 的守护程序应用 - 移到生产环境

了解如何获取并使用用于服务到服务调用的令牌以后，即可学习如何将应用移到生产环境。

## <a name="deployment---multitenant-daemon-apps"></a>部署 - 多租户守护程序应用

如果你是 ISV，创建可以在多个租户中运行的守护程序应用程序，则需确保租户管理员执行以下操作：

- 为应用程序预配服务主体。
- 授予对应用程序的许可。

需向客户解释如何执行这些操作。 有关详细信息，请参阅[请求整个租户的许可](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

下面是一些介绍详细信息的链接：

# <a name="net"></a>[.NET](#tab/dotnet)

- 快速入门：[使用应用的标识获取令牌并从控制台应用中调用 Microsoft Graph API](./quickstart-v2-netcore-daemon.md)。
- 以下内容的参考文档：
  - 实例化 [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)。
  - 调用 [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)。
- 其他示例/教程：
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) 提供一个简单的 .NET Core 守护程序控制台应用程序，该应用程序显示某个租户的用户在查询 Microsoft Graph。

    ![示例守护程序应用拓扑](media/scenario-daemon-app/daemon-app-sample.svg)

    同一示例还通过证书演示了变体：

    ![示例守护程序应用拓扑 - 证书](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) 具有一个 ASP.NET MVC Web 应用程序，该应用程序使用应用程序的标识来同步 Microsoft Graph 的数据，而不是代表用户来同步。 此示例还演示了管理员许可过程。

    ![拓扑](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

尝试快速入门 [使用应用的标识获取令牌并从 Python 控制台应用中调用 Microsoft Graph API](./quickstart-v2-python-daemon.md)。

# <a name="java"></a>[Java](#tab/java)

MSAL Java 目前为公共预览版。 有关详细信息，请参阅 [MSAL Java 开发示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)。

---