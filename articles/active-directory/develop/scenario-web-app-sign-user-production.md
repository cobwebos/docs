---
title: 登录用户的 Web 应用（转到生产）-Microsoft 标识平台
description: 了解如何构建用于登录用户的 Web 应用（移到生产环境）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcc7ed4f80d0e3e9750d19ff95d010052a08b87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481890"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>登录用户的 Web 应用：移至生产环境

现在，你已了解如何获取令牌来调用 web Api，了解如何将其转移到生产环境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

### <a name="scenario-for-calling-web-apis"></a>用于调用 web Api 的方案

Web 应用在用户登录后，可以代表已登录用户调用 web Api。 从 Web 应用调用 Web API 是以下方案的目标：

> [!div class="nextstepaction"]
> [用于调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>深入探讨： ASP.NET Core web 应用教程

通过此 ASP.NET Core 教程了解用于登录用户的其他方式： 

> [!div class="nextstepaction"]
> [使 web 应用可以登录用户，并使用 Microsoft 标识平台为开发人员调用 Api](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

本渐进教程提供适用于 web 应用的生产就绪代码，其中包括如何在中添加帐户登录：

- 你的组织
- 多个组织
- 工作或学校帐户或个人 Microsoft 帐户
- [Azure AD B2C](https://aka.ms/aadb2c)
- 国家云

### <a name="sample-code-java-web-app"></a>示例代码： Java web 应用

有关此示例中的 Java web 应用的详细信息，请参阅 GitHub： 

> [!div class="nextstepaction"]
> [一个 Java Web 应用程序，该应用程序使用 Microsoft 标识平台登录用户并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
