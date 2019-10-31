---
title: 后台应用程序调用 web Api （转到生产）-Microsoft 标识平台
description: 了解如何构建一个可调用 web Api 的守护程序应用（转到生产环境）
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05418cde7b31392c1a55f64945764e978daba1bf
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175398"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>用于调用 web Api 的后台应用程序-迁移到生产

现在，你已了解如何获取和使用令牌进行服务到服务调用，了解如何将应用移到生产环境。

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>部署-多租户后台程序应用的案例

如果你是创建可在多个租户中运行的后台应用程序的 ISV 应用程序，则需要确保租户管理员：

- 预配应用程序的服务主体
- 向应用程序授予许可

你需要向客户说明如何执行这些操作。 有关详细信息，请参阅[请求整个租户的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

下面是一些用于了解详细信息的链接：

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- 如果尚未安装，请尝试快速入门[获取令牌，并使用应用的标识从控制台应用调用 MICROSOFT GRAPH API](./quickstart-v2-netcore-daemon.md)。
- 参考文档：
  - 实例化[ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - 调用[AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- 其他示例/教程：
  - [microsoft 标识平台-控制台守护](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon)程序是一个简单的 .net Core 守护程序控制台应用程序，它显示查询 Microsoft Graph 的租户用户。

    ![拓扑](media/scenario-daemon-app/daemon-app-sample.svg)

    同一示例还说明了证书的变体。

    ![拓扑](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft 标识平台 webapp](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon)功能是一种 ASP.NET MVC web 应用程序，它使用应用程序的标识（而不是代表用户）从 Microsoft Graph 同步数据。 该示例还演示了管理员许可过程。

    ![拓扑](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

尝试快速入门[获取令牌，并使用应用的标识从 Python 控制台应用调用 MICROSOFT GRAPH API](./quickstart-v2-python-daemon.md)。

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java 当前为公共预览版。 有关详细信息，请参阅[MSAL Java dev 示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)。

---
