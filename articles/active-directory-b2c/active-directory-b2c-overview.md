---
title: "Azure Active Directory B2C：概述 | Microsoft Docs"
description: "使用 Azure Active Directory B2C 开发面向用户的应用程序"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: swkrish
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 2f64c905d2304bfc94415e871012a783cd2cf328
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---
# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory B2C：在应用程序中注册用户和让用户登录
Azure Active Directory B2C 是一个综合性的云标识管理解决方案，适用于面向用户的 Web 和移动应用程序。 这是一个具有高可用性的全球服务，可扩展到亿万用户标识。 以企业级安全平台为基础，Azure Active Directory B2C 可保护你的应用程序、业务和用户。

过去，想要在自己的应用程序中注册用户并使用户登录的应用程序开发人员编写了自己的代码。 他们使用本地数据库或系统存储用户名和密码。 Azure Active Directory B2C 通过基于标准的安全平台和一组丰富的可扩展策略，向开发人员提供一种更好的方式将用户标识管理集成到他们的应用程序中。 使用 Azure Active Directory B2C 时，用户可通过使用他们现有的社交帐户（Facebook、Google、Amazon、LinkedIn）或创建新的凭据（电子邮件地址和密码，或者用户名和密码）在应用程序中注册，我们将后者称为“本地帐户”。

## <a name="get-started"></a>入门
若要构建可接受用户注册和登录的应用程序，首先需要在 Azure Active Directory B2C 租户中注册应用程序。 使用 [创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)中所述的步骤获取自己的租户。

可以根据 Azure Active Directory B2C 服务编写应用程序，方法可以是选择直接发送协议消息、使用 [OAuth 2.0 或 Open ID Connect](active-directory-b2c-reference-protocols.md)，或者使用我们的库来来完成工作。 从下表中选择你最喜欢的平台并开始执行操作。

[!INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>新增功能
请经常查阅此处，以了解有关将来对 Azure Active Directory B2C 进行的更改。 我们还会使用 @AzureAD 在推特上发表任何更新。

* 了解我们的 [可扩展策略框架](active-directory-b2c-reference-policies.md) 和可以在应用程序中创建并使用的策略类型。
* 为有关服务的较小问题、更新、状态和缓解措施的通知的 [服务博客](https://blogs.msdn.microsoft.com/azureadb2c/) 添加书签。 继续监视 [Azure 状态仪表板](https://azure.microsoft.com/status/) 。
* 当前的 [服务限制、制约和约束](active-directory-b2c-limitations.md)。
* 最后是使用 Azure AD B2C 和 ASP.NET Core 的[代码示例](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c)。

## <a name="how-to-articles"></a>操作方法文章
了解如何使用 Azure Active Directory B2C 的特定功能：

* 配置在面向用户的应用程序中使用的 [Facebook](active-directory-b2c-setup-fb-app.md)、[Google+](active-directory-b2c-setup-goog-app.md)、[Microsoft account](active-directory-b2c-setup-msa-app.md)、[Amazon](active-directory-b2c-setup-amzn-app.md) 和 [LinkedIn](active-directory-b2c-setup-li-app.md) 帐户。
* [使用自定义属性来收集有关用户的信息](active-directory-b2c-reference-custom-attr.md)。
* [启用面向用户的应用程序中的 Azure 多重身份验证](active-directory-b2c-reference-mfa.md)。
* [设置用户的自助服务密码重置](active-directory-b2c-reference-sspr.md)
* [自定义由 Azure Active Directory B2C 提供的注册、登录和其他面向用户的页面的外观和感觉](active-directory-b2c-reference-ui-customization.md) 。
* [使用 Azure Active Directory 图形 API 以编程方式创建、读取、更新和删除用户](active-directory-b2c-devquickstarts-graph-dotnet.md) 。

## <a name="next-steps"></a>后续步骤
以下链接有助于深入探索服务：

* 请参阅 [Azure Active Directory B2C 定价信息](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。
* 查看我们提供的适用于 Azure Active Directory B2C 的[代码示例](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c)。 
* 使用 [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c) 标记获取有关 Stack Overflow 的帮助。
* 使用[用户之声](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)反馈你的看法 — 我们希望听到这些！
* 查看 [Azure AD B2C 协议参考](active-directory-b2c-reference-protocols.md)。
* 查看 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。
* 参阅 [Azure Active Directory B2C 常见问题解答](active-directory-b2c-faqs.md)。
* [提出针对 Azure Active Directory B2C 的支持请求](active-directory-b2c-support.md)。

## <a name="get-security-updates-for-our-products"></a>获取关于我们产品的安全更新
建议发生安全事件时获取相关通知，方法是访问 [此页](https://technet.microsoft.com/security/dd252948) 并订阅“安全公告通知”。


