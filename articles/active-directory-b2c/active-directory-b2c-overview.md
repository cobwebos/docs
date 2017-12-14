---
title: "概述 - Azure AD B2C | Microsoft Docs"
description: "使用 Azure Active Directory B2C 开发面向用户的应用程序"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeedakhter-msft
ms.openlocfilehash: bd29d78fa38dc4b5713d018eceed56e6b93739b4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C：专注于应用，让我们考虑注册和登录问题

Azure AD B2C 是一种云标识管理解决方案，适用于 Web 和移动应用程序。 它是一个具有高可用性的全球服务，可扩展到亿万标识。 Azure AD B2C 以企业级安全平台为基础，可保护应用程序、业务和用户。

只需最小配置，Azure AD B2C 使应用程序能够验证：

* **社交帐户**（如 Facebook、Google、LinkedIn 等）
* **企业帐户**（使用 OpenID Connect 或 SAML 等开放标准协议）
* **本地帐户**（电子邮件地址和密码，或用户名和密码）

## <a name="get-started"></a>入门

首先，使用[创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)中所述的步骤获取自己的租户。

然后，选择应用程序开发方案：

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![移动应用和桌面应用](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />移动应用和桌面应用</center> | [概述](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Web 应用](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Web 应用</center> | [概述](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![单页应用](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />单页应用</center> | [概述](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![Web API](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [调用 .NET Web API](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>新增功能

请经常查阅此处，以了解有关将来对 Azure Active Directory B2C 进行的更改。 我们还会使用 @AzureAD 在推特上发表任何更新。

* 除了“内置策略”（正式版）以外，[自定义策略](active-directory-b2c-overview-custom.md)功能现已在公共预览版中可用。  自定义策略适用于需要控制其标识体验组成的标识专业人员。
* [访问令牌](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview)功能现已在公共预览版中可用。
* [基于欧洲的 Azure AD B2C 正式版](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/)目录已发布。
* 请查看我们的不断增长的 [Github 上的代码示例](https://github.com/Azure-Samples?q=b2c)库！

## <a name="how-to-articles"></a>操作方法文章

了解如何使用 Azure Active Directory B2C 的特定功能：

* 配置在面向用户的应用程序中使用的 [Facebook](active-directory-b2c-setup-fb-app.md)、[Google+](active-directory-b2c-setup-goog-app.md)、[Microsoft account](active-directory-b2c-setup-msa-app.md)、[Amazon](active-directory-b2c-setup-amzn-app.md) 和 [LinkedIn](active-directory-b2c-setup-li-app.md) 帐户。
* [使用自定义属性来收集有关用户的信息](active-directory-b2c-reference-custom-attr.md)。
* [启用面向用户的应用程序中的 Azure 多重身份验证](active-directory-b2c-reference-mfa.md)。
* [设置用户的自助服务密码重置](active-directory-b2c-reference-sspr.md)
* [自定义由 Azure Active Directory B2C 提供的注册、登录和其他面向用户的页面的外观和感觉](active-directory-b2c-reference-ui-customization.md)。
* [使用 Azure Active Directory 图形 API 以编程方式创建、读取、更新和删除用户](active-directory-b2c-devquickstarts-graph-dotnet.md) 。

## <a name="next-steps"></a>后续步骤

以下链接有助于深入探索服务：

* 请参阅 [Azure Active Directory B2C 定价信息](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。
* 查看我们提供的适用于 Azure Active Directory B2C 的[代码示例](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c)。 
* 使用 [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c) 标记获取有关 Stack Overflow 的帮助。
* 使用[用户之声](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)反馈看法 — 我们希望听到这些！
* 查看 [Azure AD B2C 协议参考](active-directory-b2c-reference-protocols.md)。
* 查看 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。
* 参阅 [Azure Active Directory B2C 常见问题解答](active-directory-b2c-faqs.md)。
* [提出针对 Azure Active Directory B2C 的支持请求](active-directory-b2c-support.md)。

## <a name="get-security-updates-for-our-products"></a>获取关于我们产品的安全更新

建议发生安全事件时获取相关通知，方法是访问 [此页](https://technet.microsoft.com/security/dd252948) 并订阅“安全公告通知”。

