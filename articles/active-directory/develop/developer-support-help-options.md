---
title: Azure AD 应用开发人员的支持和帮助选项 | Microsoft Docs
description: 了解在创建与 Microsoft 标识（Azure Active Directory 和 Microsoft 帐户）集成的应用程序时，如何获取所遇到的开发相关问题的帮助和支持
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f1c6d89af1d88be9a241c94462b17edf0c93de2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843183"
---
# <a name="support-and-help-options-for-developers"></a>开发人员的支持和帮助选项

如果是刚刚开始与 Azure Active Directory (Azure AD)、Microsoft 标识或 Microsoft 图形 API 集成，或者是正在应用程序中实现新功能，有时需要从社区获得帮助，或了解开发人员可用的支持选项。 可以通过本文了解这些选项，其中包括：

> [!div class="checklist"]
> * 如何通过搜索来确定是问题尚未由社区做出解答，还是你尝试实现的功能已存在现有文档
> * 在某些情况下，我们只想使用自己的支持工具来帮助调试特定的问题
> * 如果找不到所需的解答，可在 *Stack Overflow* 上提问
> * 如果发现某个身份验证库出现问题，请提出 *GitHub* 问题
> * 最后，如果需要与某人交流，可以提出支持请求

## <a name="search"></a>搜索

如果遇到开发相关的问题，也许能够在文档、[GitHub 示例](https://github.com/azure-samples)或 [Stack Overflow](https://www.stackoverflow.com) 问题解答中找到答案。

### <a name="scoped-search"></a>限定范围的搜索

为了更快地找到结果，请在偏好的搜索引擎中使用以下查询，将搜索范围限定为 Stack Overflow、文档和代码示例：

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

其中， *{Your Search Terms}* 对应于搜索关键字。

## <a name="use-the-development-support-tools"></a>使用开发支持工具

| 工具  | 描述  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | 粘贴 ID 或访问令牌来解码声明名称和值。 |
| [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)| 用于针对 Microsoft Graph API 发出请求和查看响应的工具。 |

## <a name="post-a-question-to-stack-overflow"></a>在 Stack Overflow 上发布问题

Stack Overflow 是开发相关问题的首选渠道。 在这里，开发人员社区成员和 Microsoft 团队成员可直接助你解决问题。

如果通过搜索未找到你的问题的解答，请在 Stack Overflow 中提交新问题。 在提问时请使用以下标记之一，以便社区更快速地识别问题和解答问题：

|组件/区域  | Tags |
|---------|---------|
| ADAL 库 | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| MSAL 库     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN 中间件  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft 图形 API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| 与身份验证或授权主题相关的其他任何方面 | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Stack Overflow 中的以下帖子包含有关如何提问和如何添加源代码的提示。 遵循以下准则可提高社区成员快速评估和响应问题的可能性：

* [如何正确提问](https://stackoverflow.com/help/how-to-ask)
* [How to create a minimal, complete, and verifiable example](https://stackoverflow.com/help/mcve)（如何创建最小、完整且可验证的示例）

## <a name="create-a-github-issue"></a>创建 GitHub 问题

如果发现了与库相关的 Bug 或问题，请在 GitHub 存储库中提出问题。 由于我们的库是开源的，因此也可提交拉取请求。

有关库及其 GitHub 存储库的列表，请参阅以下内容：

* [ADAL](active-directory-authentication-libraries.md) 库和 GitHub 存储库
* [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md)， [MSAL。Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)和 MSAL 库和 GitHub 存储库[obj_c](https://github.com/AzureAD/microsoft-authentication-library-for-objc)

## <a name="open-a-support-request"></a>提出支持请求

如果需要与某人交流，可以提出支持请求。 Azure 客户可以使用多个支持选项。 若要比较计划，请参阅[此页](https://azure.microsoft.com/support/plans/)。 开发人员支持也适用于 Azure 客户。 有关如何购买开发人员支持计划的信息，请参阅[此页](https://azure.microsoft.com/support/plans/developer/)。

* 如果已有 Azure 支持计划，请[在此处提出支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* 如果你不是 Azure 客户，也可以通过[我们的商业支持](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)向 Microsoft 提出支持请求。

还可以尝试通过[虚拟代理](https://support.microsoft.com/contactus/?ws=support)获取支持或提出问题。
