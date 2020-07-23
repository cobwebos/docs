---
title: 将调用 Web API 的 Web 应用移到生产环境 - Microsoft 标识平台 | Azure
description: 了解如何将调用 Web API 的 Web 应用移到生产环境。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6404d00b87b9ee745b9e3a92c646404e574417c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881655"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>调用 Web API 的 Web 应用：移到生产环境

现在你已了解如何获取用于调用 Web API 的令牌，接下来了解如何移到生产环境。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

通过试用 ASP.NET Core Web 应用的完整渐进式教程了解更多信息。 本教程：

- 演示如何将用户登录到多个受众或国家云，或使用社交标识登录。
- 调用 Microsoft Graph。
- 调用多个 Microsoft API。
- 处理增量同意。
- 调用自己的 Web API。

> [!div class="nextstepaction"]
> [ASP.NET Core Web 应用教程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
