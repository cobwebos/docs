---
title: 快速入门：在 Microsoft 标识平台中注册应用 | Azure
description: 本快速入门介绍如何使用 Microsoft 标识平台注册应用程序。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperfq1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 9cd59d6bf5b9bf6e17cba0786bfac27ed12d7638
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91258126"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>快速入门：将应用程序注册到 Microsoft 标识平台

在本快速入门中，你将在 Azure 门户中注册一个应用，以便 Microsoft 标识平台可为该应用程序及其用户提供身份验证和授权服务。

希望 Microsoft 标识平台执行标识和访问管理 (IAM) 的每个应用程序都需要注册。 无论是类似于 Web 应用或移动应用的客户端应用程序是，还是支持客户端应用的 Web API，注册它都会在应用程序与标识提供程序（Microsoft 标识平台）之间建立信任关系。

## <a name="prerequisites"></a>先决条件

* 具备有效订阅的 Azure 帐户 - [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 完成[快速入门：设置租户](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>注册应用程序

注册应用程序会在应用与 Microsoft 标识平台之间建立信任关系。 信任是单向的：应用信任 Microsoft 标识平台，但标识平台并不信任应用。

按照以下步骤创建应用注册：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器:::image type="icon" source="./media/quickstart-register-app/portal-01-directory-subscription-filter.png" border="false":::，选择要在其中注册应用程序的租户。
1. 搜索并选择“Azure Active Directory”  。
1. 在“管理”下，选择“应用注册”，然后选择“新建注册”  。
1. 输入应用程序的**名称**。 应用的用户可能会看到此名称，你稍后可对其进行更改。
1. 指定可使用该应用程序的人员，这有时称为“登录访问者”。

    | 支持的帐户类型 | 说明 |
    |-------------------------|-------------|
    | **仅此组织目录中的帐户** | 如果要生成仅供租户中的用户（或来宾）使用的应用程序，请选择此选项。<br><br>通常称为业务线 (LOB) 应用程序，这是 Microsoft 标识平台中的单租户应用程序。 |
    | **任何组织目录中的帐户** | 如果希望任何 Azure AD 租户中的用户都能够使用你的应用程序，请选择此选项。 例如，如果要构建打算向多个组织提供的软件即服务 (SaaS) 应用程序，则适合使用此选项。<br><br>这在 Microsoft 标识平台中被称为多租户应用程序。 |
    | **任何组织目录中的帐户和个人 Microsoft 帐户** | 若要面向最广泛的客户，请选择此选项。<br><br>通过选择此选项，将注册一个多租户应用程序，该应用还可支持使用 Microsoft 个人帐户 (MSA) 的用户 。 |
    | **Microsoft 个人帐户** | 如果要构建仅供具有 Microsoft 个人帐户的用户使用的应用程序，请选择此选项。 Microsoft 个人帐户包括 Skype、Xbox、Live 和 Hotmail 帐户。 |

1. 请勿对“重定向 URI (可选)”输入任何内容，你将在下一部分中进行配置。
1. 选择“注册”，完成初始应用注册。

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Web 浏览器中 Azure 门户的屏幕截图，其中显示了“注册应用程序”窗格。":::

注册完成后，Azure 门户会显示应用注册的“概述”窗格，其中包括其应用程序（客户端）ID 。 此值也被称为客户端 ID，它可唯一地标识 Microsoft 标识平台中的应用程序。

应用程序的代码（或者更通常是应用程序中使用的身份验证库）也将使用客户端 ID 来验证从标识平台接收的安全令牌。

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Web 浏览器中 Azure 门户的屏幕截图，其中显示了“注册应用程序”窗格。":::

## <a name="add-a-redirect-uri"></a>添加重定向 URI

重定向 URI 是 Microsoft 标识平台重定向用户客户端并在身份验证后发送安全令牌的位置。

例如，在生产 Web 应用程序中，重定向 URI 通常是运行应用的公共终结点，比如 `https://contoso.com/auth-response`。 在开发过程中，通常还会添加在本地运行应用的终结点，例如 `https://127.0.0.1/auth-response` 或 `http://localhost/auth-response`。

可通过配置已注册应用程序的[平台设置](#configure-platform-settings)来添加和修改它的重定向 URI。

### <a name="configure-platform-settings"></a>配置平台设置

在 Azure 门户的平台配置中配置每种应用程序类型的设置（包括重定向 URI）。 某些平台（例如 Web 和单页应用程序）要求手动指定重定向 URI 。 对于其他平台（例如移动和桌面），可选择在配置其他设置时为用户生成的重定向 URI。

若要根据面向的平台或设备配置应用程序设置：

1. 在 Azure 门户中的“应用注册”中选择你的应用程序。
1. 在“管理”下，选择“身份验证”。 
1. 在“平台配置”下，选择“添加平台” 。
1. 在“配置平台”中，为应用程序类型（平台）选择磁贴，以配置其设置。

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Web 浏览器中 Azure 门户的屏幕截图，其中显示了“注册应用程序”窗格。" border="false":::

    | 平台 | 配置设置 |
    | -------- | ---------------------- |
    | **Web** | 输入应用的重定向 URI，即 Microsoft 标识平台重定向用户客户端并在身份验证后发送安全令牌的位置。<br/><br/>为服务器上运行的标准 Web 应用程序选择此平台。 |
    | **单页应用程序** | 输入应用的重定向 URI，即 Microsoft 标识平台重定向用户客户端并在身份验证后发送安全令牌的位置。<br/><br/>如果要在 JavaScript 中或使用 Angular、Vue.js、React.js 或 Blazor WebAssembly 等框架构建客户端 Web 应用，请选择此平台。 |
    | **iOS/macOS** | 输入应用捆绑 ID，可在 info.plist 中的 XCode 或生成设置中找到。<br/><br/>如果指定捆绑 ID，将生成重定向 URI。 |
    | **Android** | 输入应用包名称（可在 AndroidManifest.xml 文件中找到），然后生成并输入签名哈希。<br/><br/>如果指定这些设置，将生成重定向 URI。 |
    | **移动和桌面应用程序** | 选择建议的重定向 URI 之一，或指定自定义重定向 URI 。<br/>对于桌面应用程序，建议：<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>对于不使用最新 Microsoft 身份验证库 (MSAL) 或不使用代理的移动应用程序，请选择此平台。 同时也为桌面应用程序选择此平台。 |
1. 选择“配置”以完成平台配置。

### <a name="redirect-uri-restrictions"></a>重定向 URI 限制

对于添加到应用注册的重定向 URI 的格式，存在某些限制。 有关这些限制的详细信息，请参阅[重定向 URI（回复 URL）的限制和局限](reply-url.md)。

## <a name="add-credentials"></a>添加凭据

凭据供访问 Web API 的机密客户端应用程序使用。 Web 应用、其他 Web API 或服务类型和守护程序类型的应用程序都是机密客户端。 通过凭据，应用程序可以自己的身份进行身份验证，无需用户在运行时进行任何交互。

可将证书和客户端密码（字符串）作为凭据添加到机密客户端应用注册。

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Web 浏览器中 Azure 门户的屏幕截图，其中显示了“注册应用程序”窗格。":::

### <a name="add-a-certificate"></a>添加证书

证书有时被称为公钥，它们是推荐的凭据类型，因为它们提供的保证级别比客户端密码高。

1. 在 Azure 门户中的“应用注册”中选择你的应用程序。
1. 选择“证书和密码” > “上传证书” 。
1. 选择要上传的文件。 它必须是以下文件类型之一：.cer、.pem、.crt。
1. 选择 **添加** 。

### <a name="add-a-client-secret"></a>添加客户端密码

客户端密码（也称为应用程序密码）是应用可用于代替证书来标识自身的字符串。 这是两种凭证类型中更容易使用的一种，而且通常在开发过程中使用，但它不如证书安全。 应在生产环境中运行的应用程序中使用证书。

1. 在 Azure 门户中的“应用注册”中选择你的应用程序。
1. 选择“证书和密码” >  “新建客户端密码” 。
1. 添加客户端机密的说明。
1. 选择持续时间。
1. 选择 **添加** 。
1. “记录密码的值”，以便在客户端应用程序中使用 - 离开此页面后，它将不再显示。

## <a name="next-steps"></a>后续步骤

客户端应用程序通常需要访问 Web API 中的资源。 除了使用 Microsoft 标识平台保护客户端应用程序以外，还可使用平台对 Web API 进行范围内基于权限的访问。

请转到本系列的下一篇快速入门，为 Web API 创建另一个应用注册并公开其范围。

> [!div class="nextstepaction"]
> [配置应用程序以公开 Web API](quickstart-configure-app-expose-web-apis.md)
