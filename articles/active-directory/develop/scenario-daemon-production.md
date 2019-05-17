---
title: 守护程序应用程序调用 web Api （移动到生产环境）-Microsoft 标识平台
description: 了解如何构建守护程序应用调用 web Api （移动到生产环境）
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545399"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>守护程序应用调用 web Api-移动到生产环境

现在，了解如何获取和使用的令牌进行服务到服务调用，了解如何将应用移动到生产环境。

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>部署-多租户守护程序应用的大小写

如果你是 ISV 创建可以在多个租户中运行的后台应用程序，将需要确保租户管理员：

- 预配应用程序的服务主体
- 授予同意该应用程序

你将需要向客户介绍如何执行这些操作。 有关详细信息，请参阅[请求整个租户的许可](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

以下是几个链接以了解详细信息：

### <a name="net"></a>.NET

- 如果尚不存在，请尝试本快速入门[获取令牌并调用 Microsoft Graph API 从一个控制台应用使用应用的标识](./quickstart-v2-netcore-daemon.md)。
- 参考文档：
  - 实例化[ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - 调用[AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- 其他示例/教程：
  - [microsoft 的标识的平台的控制台-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon)功能显示查询 Microsoft Graph 的租户的用户的简单.NET Core 守护程序控制台应用。

    ![拓扑](media/scenario-daemon-app/daemon-app-sample.svg)

    同一个示例还说明了使用证书的变体。

    ![拓扑](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon)功能可将数据从 Microsoft Graph 使用而不是应用程序代表用户的标识同步的 ASP.NET MVC web 应用程序。 该示例还说明了管理员同意过程。

    ![拓扑](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

MSAL Python 目前处于公共预览状态。 有关详细信息，请参阅[MSAL Python 客户端凭据中存储库示例](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py)。

### <a name="java"></a>Java

MSAL Python 目前处于公共预览状态。 有关详细信息，请参阅[在存储库的 MSAL Java 示例](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples)。