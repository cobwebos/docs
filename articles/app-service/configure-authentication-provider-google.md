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
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232148"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>如何将应用服务应用程序配置为使用 Google 登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题介绍如何将 Azure 应用服务配置为使用 Google 作为身份验证提供程序。

要完成本主题中的过程，必须拥有一个包含已验证电子邮件地址的 Google 帐户。 若要新建一个 Google 帐户，请转到 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register"> </a>向 Google 注册应用程序
1. 遵循 google[登录以进行服务器端应用](https://developers.google.com/identity/sign-in/web/server-side-flow), 以使用以下信息创建客户端 ID 和客户端密码 (无需进行任何代码更改):
    - 对于**授权的 JavaScript**源, `https://<app-name>.azurewebsites.net`请在 *\<应用名称 >* 中将与应用名称一起使用。
    - 对于**授权的重定向 URI**, 请使用`https://<app-name>.azurewebsites.net/.auth/login/google/callback`。
1. 创建客户端 ID 和客户端密码后, 复制其值。

    > [!IMPORTANT]
    > 客户端密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。


## <a name="secrets"></a>向应用程序添加 Google 信息
1. 在 [Azure 门户]中，导航到应用服务应用。 从左侧菜单中选择 "**身份验证/授权**"。
2. 如果“身份验证/授权”功能未启用，请切换为“打开”。
3. 单击“Google”。 粘贴前面获取的应用程序 ID 和应用程序密钥值, 还可以选择启用应用程序所需的任何范围。 然后单击“确定”。

   应用服务提供身份验证, 但不限制对站点内容和 Api 的授权访问。 有关详细信息, 请参阅[授权或拒绝用户](app-service-authentication-how-to.md#authorize-or-deny-users)。
4. （可选）要限制只有通过 Google 帐户身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“Google”。 这会要求对所有请求进行身份验证，而所有未经身份验证的请求会被重定向到 Google 进行身份验证。

    > [!NOTE]
    > 以这种方式限制访问权限适用于对应用的所有调用, 对于需要公开提供的主页的应用, 与在许多单页应用程序中一样。 对于此类应用程序, 可以首选 "**允许匿名请求 (无操作)** ", 应用手动启动登录, 如[此处](overview-authentication-authorization.md#authentication-flow)所述。
    
5. 单击“保存”。

现在，可以在应用中使用 Google 进行身份验证了。

## <a name="related-content"> </a>相关内容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 门户]: https://portal.azure.com/

