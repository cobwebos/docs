---
title: 配置 Google 身份验证 - Azure 应用服务
description: 了解如何为应用服务应用配置 Google 身份验证。
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 917fa87a0cd0f7b0615a5139a7c15311f866739a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176965"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>将应用服务应用配置为使用 Google 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题介绍如何将 Azure 应用服务配置为使用 Google 作为身份验证提供程序。

要完成本主题中的过程，必须拥有一个包含已验证电子邮件地址的 Google 帐户。 若要新建一个 Google 帐户，请转到 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register"> </a>向 Google 注册应用程序

1. 按照[适用于服务器端应用的 Google 登录](https://developers.google.com/identity/sign-in/web/server-side-flow)上的 google 文档来创建客户端 ID 和客户端密码。 无需进行任何代码更改。 只需使用以下信息：
    - 对于**授权的 JavaScript**源，请将 `https://<app-name>.azurewebsites.net` 与应用名称一起使用 *@no__t 3app >* 。
    - 对于**授权的重定向 URI**，请使用 `https://<app-name>.azurewebsites.net/.auth/login/google/callback`。
1. 复制 "应用 ID" 和 "应用密钥" 值。

    > [!IMPORTANT]
    > 应用程序密钥是一个重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。

## <a name="secrets"></a>向应用程序添加 Google 信息

1. 在[Azure 门户]中转到应用服务应用。
1. 选择 "**设置**" @no__t "**身份验证/授权**"，并确保已**启用 "** **应用服务身份验证**"。
1. 选择 " **Google**"，然后粘贴你之前获取的应用程序 ID 和应用程序密钥值。 启用应用程序所需的任何范围。
1. 选择“确定”。

   应用服务提供身份验证，但不限制对站点内容和 Api 的授权访问。 有关详细信息，请参阅[授权或拒绝用户](app-service-authentication-how-to.md#authorize-or-deny-users)。

1. 可有可无若要将站点访问限制为仅限 Google 身份验证的用户，请在 "请求未通过**google** **身份验证时" 设置要执行的操作**。 设置此功能时，应用要求对所有请求进行身份验证。 它还将所有未经身份验证的请求重定向到 Google 进行身份验证。

    > [!CAUTION]
    > 以这种方式限制访问权限适用于对应用的所有调用，对于具有公开可用主页的应用，与在许多单页应用程序中一样，这可能并不理想。 对于此类应用程序，"**允许匿名请求（无操作）** " 可能首选，以便应用手动启动身份验证。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 选择“保存”。

现在，可以在应用中使用 Google 进行身份验证了。

## <a name="related-content"></a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 门户]: https://portal.azure.com/

