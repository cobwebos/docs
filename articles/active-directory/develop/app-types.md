---
title: v1.0 中的应用程序类型 | Azure
description: 介绍 Azure Active Directory v2.0 终结点支持的应用和方案类型。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 5a8e43934393c2e6a97b81809284781a5c207e41
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816338"
---
# <a name="application-types-in-v10"></a>v1.0 中的应用程序类型

Azure Active Directory (Azure AD) 支持各种新型应用体系结构的身份验证，所有这些体系结构都基于行业标准协议 OAuth 2.0 或 OpenID Connect。

下图说明了这些方案和应用程序类型，以及如何添加各种组件：

![应用程序类型和方案](./media/authentication-scenarios/application_types_and_scenarios.png)

下面是 Azure AD 支持的五种主要应用程序方案：

- **[单页应用程序 (SPA)](single-page-application.md)**：用户需要登录到受 Azure AD 保护的单页应用程序。
- **[Web 浏览器到 Web 应用程序](web-app.md)**：用户需要登录到受 Azure AD 保护的 Web 应用程序。
- **[本机应用程序到 Web API](native-app.md)**：在手机、平板电脑或电脑上运行的本机应用程序需要对用户进行身份验证以通过受 Azure AD 保护的 Web API 获取资源。
- **[Web 应用程序到 Web API](web-api.md)**：Web 应用程序需要通过受 Azure AD 保护的 Web API 获取资源。
- **[守护程序或服务器应用程序到 Web API](service-to-service.md)**：没有 Web 用户界面的守护程序应用程序或服务器应用程序需要通过受 Azure AD 保护的 Web API 获取资源。

在开始使用代码之前，请打开链接详细了解每种类型应用并了解高级方案。 还可以了解在编写适用于 v1.0 终结点或 v2.0 终结点的特定应用时需要了解的差异。

> [!NOTE]
> v2.0 终结点并非支持所有 Azure AD 方案和功能。 若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。

你可以使用各种语言和平台开发此处所述的任何应用和方案。 它们都受到代码示例指南中提供的完整代码示例的支持：[按方案分类的 v1.0 代码示例](sample-v1-code.md)和[按方案分类的 v2.0 代码示例](sample-v2-code.md)。 也可以直接从相应的 [GitHub 示例存储库](https://github.com/Azure-Samples?q=active-directory)下载代码示例。

此外，如果应用程序需要某个端到端方案的特定片段，在大多数情况下都可以独立添加该功能。 例如，如果有一个调用某个 Web API 的本机应用程序，则可以轻松添加也调用该 Web API 的 Web 应用程序。

## <a name="app-registration"></a>应用注册

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>注册使用 Azure AD v1.0 终结点的应用程序

将身份验证外包给 Azure AD 的任何应用程序都必须在目录中进行注册。 此步骤涉及告诉 Azure AD 关于应用程序的情况，包括应用程序所在的 URL、在进行身份验证后要将回复发送到的 URL、用以标识应用程序的 URI，以及其他信息。 该信息是必需的，有以下几个重要原因：

* 在处理登录或者交换令牌时，Azure AD 需要与应用程序进行通信。 Azure AD 和应用程序之间传递的信息包括以下内容：
  
  * **应用程序 ID URI** - 应用程序的标识符。 此值在身份验证期间发送给 Azure AD 以指明调用者是希望为哪个应用程序获取令牌。 另外，此值还包括在令牌中以便应用程序知道它是预定目标。
  * **回复 URL** 和**重定向 URI** - 对于 Web API 或 Web 应用程序，回复 URL 是当身份验证成功时 Azure AD 要将身份验证响应（包括令牌）发送到的位置。 对于本机应用程序，重定向 URI 是一个唯一标识符，Azure AD 会将 OAuth 2.0 请求中的用户代理重定向到该标识符。
  * **应用程序 ID** - 应用程序的 ID，这是在注册应用程序时由 Azure AD 生成的。 当请求授权代码或令牌时，在身份验证期间会将应用程序 ID 和密钥发送到 Azure AD。
  * **密钥** - 向 Azure AD 进行身份验证以调用 Web API 时会随应用程序 ID 一起发送的密钥。
* Azure AD 需要确保应用程序具有访问目录数据、组织中的其他应用程序等内容所需的权限。

有关详细信息，请了解如何[向 Azure AD v1.0 终结点注册应用](quickstart-v1-add-azure-ad-app.md)。

## <a name="single-tenant-and-multi-tenant-apps"></a>单租户应用和多租户应用

了解可以开发两类与 Azure AD 集成的应用程序时，设置工作将变得更为清晰：

* **单租户应用程序** - 单租户应用程序预定在单个组织中使用。 它们通常是由企业开发人员编写的业务线 (LoB) 应用程序。 单租户应用程序只需要供单个目录中的用户进行访问，因此，只需要将其设置在单个目录中。 这些应用程序通常由组织中的开发人员进行注册。
* **多租户应用程序** - 多租户应用程序预定在许多组织中使用，而不仅是在单个组织中使用。 它们通常是由独立软件供应商 (ISV) 编写的软件即服务 (SaaS) 应用程序。 多租户应用程序需要设置在将使用它们的每个目录中，需要经过用户或管理员许可才能注册它们。 当在目录中注册应用程序并向其授予对 Graph API 或者另一可能的 Web API 的访问权限时，此许可过程即已开始。 当其他组织的用户或管理员注册使用应用程序时，会向他们显示一个对话框，其中显示了应用程序要求的权限。 然后，用户或管理员可以许可应用程序的要求，这会向应用程序授予对指定数据的访问权限，并最终在其目录中注册该应用程序。 有关详细信息，请参阅[许可框架概述](consent-framework.md)。

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>开发单租户应用或多租户应用时的其他注意事项

与开发单租户应用程序相比，当开发多租户应用程序时，会出现一些额外的注意事项。 例如，如果要使应用程序可供多个目录中的用户使用，需要一种机制来确定用户在哪个租户中。 单租户应用程序只需要在其自己的目录中查找用户，而多租户应用程序需要从 Azure AD 中的所有目录来识别特定用户。 为此，Azure AD 提供了一个任何多租户应用程序都可以在其中对登录请求进行定向的通用身份验证终结点，而不是提供特定于租户的终结点。 对于 Azure AD 中的所有目录，此终结点为 https://login.microsoftonline.com/common，而特定于租户的终结点可能为 https://login.microsoftonline.com/contoso.onmicrosoft.com。 在开发应用程序时考虑通用终结点尤为重要，因为在登录、注销和令牌验证期间需要必要的逻辑来处理多租户。

如果当前在开发单租户应用程序但希望使其可供许多组织使用，可以轻松地在 Azure AD 中更改该应用程序及其配置以使其支持多租户。 此外，无论是在单租户应用程序中还是在多租户应用程序中提供身份验证，Azure AD 都将为所有目录中的所有令牌使用相同的签名密钥。

本文档中列出的每个方案都包括一个小节，用以介绍其预配要求。 有关在 Azure AD 中预配应用程序以及单租户应用程序与多租户应用程序之间区别的更深入信息，请参阅[将应用程序与 Azure Active Directory 集成](quickstart-v1-integrate-apps-with-azure-ad.md)了解详细信息。 继续阅读以了解 Azure AD 中的常见应用程序方案。

## <a name="next-steps"></a>后续步骤

- 详细了解其他 Azure AD [身份验证基础知识](authentication-scenarios.md)
