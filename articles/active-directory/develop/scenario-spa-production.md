---
title: 将单页应用移到生产环境 - Microsoft 标识平台 | Azure
description: 了解如何生成单页应用程序（移到生产环境）
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 21ba0193c3f1e19ffc74452aaceee34759c7e606
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949008"
---
# <a name="single-page-application-move-to-production"></a>单页应用程序：移到生产环境

现在你已了解如何获取用于调用 Web API 的令牌，接下来了解如何移到生产环境。

## <a name="improve-your-app"></a>改进应用

[启用日志记录](msal-logging.md)使应用生产环境准备就绪。

## <a name="test-your-integration"></a>测试集成

按照 [Microsoft 标识平台集成清单](identity-platform-integration-checklist.md)测试你的集成。

## <a name="deploy-your-app"></a>部署你的应用

查看 [部署示例](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) ，了解如何使用 Azure 存储和 Azure 应用服务分别部署 SPA 和 Web API 项目。 

## <a name="next-steps"></a>后续步骤

深入了解快速入门示例，其中介绍了有关如何使用**MSAL.js**登录用户和获取访问令牌来调用**Microsoft Graph API**的代码：

> [!div class="nextstepaction"]
> [JavaScript SPA 教程](./tutorial-v2-javascript-spa.md)

此示例演示如何使用 **MSAL.js**获取自己的后端 web API (ASP.NET Core) 的令牌：

> [!div class="nextstepaction"]
> [使用 ASP.NET 后端的 SPA](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

此示例演示如何使用 **passport-azure** ( # A0) 验证后端 web API 的访问令牌。

> [!div class="nextstepaction"]
> [Node.js Web API (Azure AD) ](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)

此示例演示如何使用 **MSAL.js** 在注册到 **Azure Active Directory B2C** (Azure AD B2C) 的应用中登录用户：

> [!div class="nextstepaction"]
> [使用 Azure AD B2C 的 SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

此示例演示如何使用 **passport azure ad** 来验证使用 **Azure Active Directory B2C** (Azure AD B2C 注册的应用的访问令牌) 

> [!div class="nextstepaction"]
> [Node.js Web API (Azure AD B2C) ](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
