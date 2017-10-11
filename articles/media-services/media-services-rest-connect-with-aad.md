---
title: "通过 Azure AD 身份验证使用 REST 访问 Azure 媒体服务 API | Microsoft Docs"
description: "了解如何通过 Azure Active Directory 身份验证使用 REST 访问 Azure 媒体服务 API。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 1c62857699fb29b3583363e1c6f2dc7874635f40
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>通过 Azure AD 身份验证使用 REST 访问 Azure 媒体服务 API

Azure 媒体服务团队发布了 Azure Active Directory (Azure AD) 身份验证支持，以供访问 Azure 媒体服务。 它还宣布了计划停止通过 Azure 访问控制服务身份验证来访问媒体服务。 由于每个 Azure 订阅和每个媒体服务帐户都附加到 Azure AD 租户，因此 Azure AD 身份验证支持增添了许多安全保障。 若要详细了解此更改和迁移（如果对应用程序使用媒体服务 .NET SDK），请参阅以下博文和文章：

- [Azure 媒体服务团队宣布支持 Azure AD 身份验证，并弃用访问控制身份验证](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation)
- [通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)
- [通过 Azure AD 身份验证使用 .NET 访问 Azure 媒体服务 API](media-services-dotnet-get-started-with-aad.md)
- [通过 Azure 门户开始使用 Azure AD 身份验证](media-services-portal-get-started-with-aad.md)

一些客户需要在以下约束条件下开发媒体服务解决方案：

*   不使用 Microsoft.NET 或 C# 编程语言，或运行时环境不是 Windows。
*   Azure AD 库（如 Active Directory 身份验证库）既不能用于编程语言，也不能用于运行时环境。

一些客户开发了应用程序，将 REST API 用于访问控制身份验证和 Azure 媒体服务访问。 对于此类客户，需要有一种方法，只对 Azure AD 身份验证和后续 Azure 媒体服务访问使用 REST API。 无需依赖任何 Azure AD 库或媒体服务 .NET SDK。 本文将介绍一种解决方案，并提供此方案的示例代码。 由于代码全都是 REST API 调用，不依赖任何 Azure AD 或 Azure 媒体服务库，因此可以将代码轻松转换为其他任何编程语言。

> [!IMPORTANT]
> 目前，媒体服务支持 Azure 访问控制服务身份验证模型。 不过，访问控制身份验证将于 2018 年 6 月 1 日弃用。 建议尽快迁移到 Azure AD 身份验证模型。


## <a name="design"></a>设计

本文将使用以下身份验证和授权设计：

*  授权协议：OAuth 2.0。 OAuth 2.0 是一种涉及身份验证和授权的 Web 安全标准。 Google、Microsoft、Facebook 和 PayPal 均支持此协议。 获准时间为 2012 年 10 月。 Microsoft 坚决支持 OAuth 2.0 和 OpenID Connect。 多个服务和客户端库都支持这两种标准，包括 Azure Active Directory、Open Web Interface for .NET (OWIN) Katana 和 Azure AD 库。
*  授权类型：客户端凭据授权类型。 客户端凭据是 OAuth 2.0 中的四种授权类型之一。 通常用于 Azure AD Microsoft Graph API 访问。
*  身份验证模式：服务主体。 另一种身份验证模式为用户或交互式身份验证。

通过 Azure AD 身份验证和授权流使用媒体服务总共涉及四个应用程序或服务。 下表介绍了这些应用程序、服务和流：

|应用程序类型 |应用程序 |流向|
|---|---|---|
|客户端 | 客户应用程序或解决方案 | 此应用程序（实际上是它的代理）在 Azure 订阅和媒体服务帐户所在的 Azure AD 租户中注册。 然后，向已注册的应用程序服务主体授予媒体服务帐户访问控制 (IAM) 中的所有者或参与者角色。 服务主体以应用程序客户端 ID 和客户端密码表示。 |
|标识提供者 (IDP) | 作为 IDP 的 Azure AD | 作为 IDP 的 Azure AD 对已注册的应用程序服务主体（客户端 ID 和客户端密码）进行身份验证。 此身份验证在内部隐式进行。 如客户端凭据流中所示，验证的是客户端，而不是用户。 |
|安全令牌服务 (STS)/OAuth 服务器 | 作为 STS 的 Azure AD | 在 IDP（或 OAuth 2.0 术语中的 OAuth 服务器）进行身份验证后，作为 STS/OAuth 服务器的 Azure AD 颁发访问令牌或 JSON Web 令牌 (JWT)，以访问中间层资源（在此示例中，为媒体服务 REST API 终结点）。 |
|资源 | 媒体服务 REST API | 每个媒体服务 REST API 调用都由作为 STS 的 Azure AD 或 OAuth 服务器颁发的访问令牌进行授权。 |

如果运行示例代码，并捕获 JWT 令牌或访问令牌，那么 JWT 包含以下属性：

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

下面展示了 JWT 中属性与上表中四个应用程序或服务的映射：

|应用程序类型 |应用程序 |JWT 属性 |
|---|---|---|
|客户端 |客户应用程序或解决方案 |appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6"。 将在下一部分中注册到 Azure AD 的应用程序客户端 ID。 |
|标识提供者 (IDP) | 作为 IDP 的 Azure AD |idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/"。  GUID 是 Microsoft 租户 (microsoft.onmicrosoft.com) 的 ID。 每个租户都有自己的唯一 ID。 |
|安全令牌服务 (STS)/OAuth 服务器 |作为 STS 的 Azure AD | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/"。 GUID 是 Microsoft 租户 (microsoft.onmicrosoft.com) 的 ID。 |
|资源 | 媒体服务 REST API |aud: " https://rest.media.azure.net "。 访问令牌的接收人或受众。 |

## <a name="steps-for-setup"></a>设置步骤

若要注册并设置用于 Azure AD 身份验证的 Azure AD 应用程序，并获取访问令牌来调用 Azure 媒体服务 REST API 终结点，请完成以下步骤：

1.  在 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中，将 Azure AD 应用程序（例如，wzmediaservice）注册到 Azure AD 租户（例如，microsoft.onmicrosoft.com）。 无论注册的是 Web 应用程序，还是本机应用程序，都没有问题。 此外，还可以选择任意登录 URL 和回复 URL（例如，两个 URL 都为 http://wzmediaservice.com）。
2. 在 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中，转到应用程序的“配置”选项卡。 记下“客户端 ID”。 然后，在“密钥”下，生成“客户端密钥”（客户端密码）。 

    > [!NOTE] 
    > 记下客户端密钥。 它不会再次显示。
    
3.  在 [Azure 门户](http://ms.portal.azure.com)中，转到媒体服务帐户。 选择“访问控制(IAM)”窗格。 添加具有所有者或参与者角色的新成员。 对于主体，搜索在第 1 步中注册的应用程序名称（在此示例中，为 wzmediaservice）。

## <a name="info-to-collect"></a>要收集的信息

若要准备 REST 编码，请收集要在代码中添加的下列数据点：

*   作为 STS 终结点的 Azure AD：https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token。 可以从此终结点请求获取 JWT 访问令牌。 除了用作 IDP 之外，Azure AD 还可以用作 STS。 Azure AD 颁发用于访问资源的 JWT（访问令牌）。 JWT 令牌具有各种声明。
*   作为资源或受众的 Azure 媒体服务 REST API：https://rest.media.azure.net。
*   客户端 ID：请参阅[设置步骤](#steps-for-setup)中的第 2 步。
*   客户端密码：请参阅[设置步骤](#steps-for-setup)中的第 2 步。
*   采用以下格式的媒体服务帐户 REST API 终结点：

    https://[media_service_account_name].restv2.[data_center].media.azure.net/API 

    这是应用程序中发出的所有媒体服务 REST API 调用所针对的终结点。 例如，https://willzhanmswjapan.restv2.japanwest.media.azure.net/API。

然后，可以将这五个参数添加到 web.config 或 app.config 文件中，以便在代码中使用。

## <a name="sample-code"></a>代码示例

有关示例代码，可以参阅[通过 Azure AD 身份验证访问 Azure 媒体服务：均使用 REST API](https://github.com/willzhan/WAMSRESTSoln)。

示例代码分为以下两部分：

*   包含 Azure AD 身份验证和授权所需的全部 REST API 代码的 DLL 库项目。 其中还包含一种方法，可使用访问令牌对媒体服务 REST API 终结点发出 REST API 调用。
*   控制台测试客户端，用于启动 Azure AD 身份验证，并调用不同的媒体服务 REST API。

示例项目具有以下三种功能：

*   只使用 REST API 通过客户端凭据授权进行 Azure AD 身份验证。
*   只使用 REST API 访问 Azure 媒体服务。
*   只使用 REST API 访问 Azure 存储（就像曾使用 REST API 创建媒体服务帐户）。


## <a name="where-is-the-refresh-token"></a>刷新令牌在哪里？

一些读者可能会问：刷新令牌在哪里？ 本文为什么不使用刷新令牌？

刷新令牌并不旨在刷新访问令牌。 相反，它的用途是规避最终用户身份验证或用户干预，在早期生成的令牌过期时仍可以获得有效的访问令牌。 刷新令牌的更准确名称可能是“规避用户重新登录令牌"。

如果使用 OAuth 2.0 授权流（用户名和密码，以用户身份操作），刷新令牌有助于获取续订的访问令牌，无需请求用户干预。 不过，对于本文中介绍的 OAuth 2.0 客户端凭据授权流，客户端是以自身身份操作。 根本就无需用户干预，所以授权服务器不需要（也不会）提供刷新令牌。 如果调试“GetUrlEncodedJWT”方法，将会注意到，来自令牌终结点的响应包含访问令牌，但不含刷新令牌。

## <a name="next-steps"></a>后续步骤

开始[将文件上传到帐户](media-services-dotnet-upload-files.md)。
