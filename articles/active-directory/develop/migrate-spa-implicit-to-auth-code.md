---
title: 将 JavaScript 单页应用从隐式授权迁移到授权代码流 |Azure
titleSuffix: Microsoft identity platform
description: 如何将使用 MSAL.js 1.x 和隐式授权流的 JavaScript SPA 更新为使用 MSAL.js 2.x 和具有 PKCE 和 CORS 支持的授权代码流。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 06/01/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 6af203759cca830a6adcf9f70436d42f3d983da4
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301974"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>将 JavaScript 单页应用从隐式授权迁移到授权代码流

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会更改。

适用于 JavaScript (MSAL.js) v2.0 的 Microsoft 身份验证库为 Microsoft 标识平台上的单页面应用程序提供了对具有 PKCE 和 CORS 的授权代码流的支持。 按照以下部分中的步骤，将使用隐式授权的 MSAL.js 1.x 迁移到 MSAL.js 2.0+（以下称为 2.x）和授权代码流。

MSAL.js 2.x 支持浏览器中的授权代码流（而不是隐式授权流），从而在 MSAL.js 1.x 的基础上进行了改进。 MSAL.js 2.x 不支持隐式流。

## <a name="migration-steps"></a>迁移步骤

若要将应用程序更新为 MSAL.js 2.x 和授权代码流，有三个主要步骤：

1. 将[应用注册](#switch-redirect-uris-to-spa-platform)重定向 URI 从“Web”平台转换到“单页应用程序”平台 。
1. 将[代码](#switch-redirect-uris-to-spa-platform)从 MSAL.js 1.x 更新为 2.x。
1. 将共享注册的所有应用程序都更新为 MSAL.js 2.x 和授权代码流后，禁用应用注册中的[隐式授权](#disable-implicit-grant-settings)。

以下部分将详细介绍每个步骤。

## <a name="switch-redirect-uris-to-spa-platform"></a>将重定向 URI 转换到 SPA 平台

如果要继续对应用程序使用现有的应用注册，请使用 Azure 门户将注册的重定向 URI 更新为 SPA 平台。 这样做可为使用注册的应用（仍需将应用程序的代码更新为 MSAL.js v2.x）启用具有 PKCE 和 CORS 支持的授权代码流。

对于当前配置了 Web 平台重定向 URI 的应用注册，请按照以下步骤进行操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“Azure Active Directory”租户。
1. 在“应用注册”中，选择应用程序，然后选择“身份验证” 。
1. 在 Web 平台磁贴下的“重定向 URI”中，选择指示“应该迁移 URI”的警告横幅 。

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Azure 门户中 Web 应用磁贴上的隐式流警告横幅":::
1. 仅选择对应的应用程序将使用 MSAL.js 2.x 的重定向 URI，然后选择“配置”。

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="在 Azure 门户的 SPA 窗格中选择重定向 URI 窗格":::

这些重定向 URI 现在应会在“单页应用程序”平台磁贴中显示，其中显示为这些 URI 启用了具有 PKCE 和 CORS 支持的授权代码流。

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Azure 门户中的应用注册中的单页应用程序磁贴":::

还可以[创建新的应用注册](scenario-spa-app-registration.md)而不是更新现有注册中的重定向 URI。

## <a name="update-your-code-to-msaljs-2x"></a>将代码更新为 MSAL.js 2.x

在 MSAL 1.x 中，你通过初始化 [UserAgentApplication][msal-js-useragentapplication] 创建了一个应用程序实例，如下所示：

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

在 MSAL 2.x 中，请改为初始化 [PublicClientApplication][msal-js-publicclientapplication]：

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

有关将 MSAL 2.x 添加到应用程序的完整演练，请参阅[教程：使用授权代码流让用户登录并从 JavaScript 单页应用 (SPA) 调用 Microsoft Graph API](tutorial-v2-javascript-auth-code.md)。

如需对代码进行其他更改，请参阅 GitHub 上的[迁移指南](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md)。

## <a name="disable-implicit-grant-settings"></a>禁用隐式授权设置

将使用此应用注册及其客户端 ID 的所有生产应用程序更新为 MSAL 2.x 和授权代码流后，应在应用注册中取消选中隐式授权设置。

在应用注册中取消选中隐式授权设置后，将对使用注册及其客户端 ID 的所有应用程序禁用隐式流。

在将所有应用程序更新为 MSAL.js 2.x 和 [PublicClientApplication][msal-js-publicclientapplication] 之前，请勿禁用隐式授权流。

## <a name="next-steps"></a>后续步骤

若要了解有关授权代码流的详细信息（包括隐式和授权代码流之间的差异），请参阅 [Microsoft 标识平台和 OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

如果你想要更深入了解 Microsoft 标识平台上的 JavaScript 单页应用程序开发，由多部分组成的[方案：单页应用程序](scenario-spa-overview.md)系列文章可以帮助你入门。

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_app_publicclientapplication_.publicclientapplication.html