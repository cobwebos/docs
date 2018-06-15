---
title: 如何为应用服务应用程序配置 Google 身份验证
description: 了解如何为应用服务应用程序配置 Google 身份验证。
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: f89ff3a030f1da75bca538eefaf2496e9be8e97b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233813"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>如何将应用服务应用程序配置为使用 Google 登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题介绍如何将 Azure 应用服务配置为使用 Google 作为身份验证提供程序。

要完成本主题中的过程，必须拥有一个包含已验证电子邮件地址的 Google 帐户。 若要新建一个 Google 帐户，请转到 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register"> </a>向 Google 注册应用程序
1. 登录到 [Azure 门户]并导航到应用程序。 复制 **URL**，稍后将用此来配置 Google 应用。
2. 导航到 [Google API](http://go.microsoft.com/fwlink/p/?LinkId=268303) 网站，使用 Google 帐户凭据登录，单击“创建项目”，提供“项目名称”，并单击“创建”。
3. 创建项目后，请选择它。 从项目仪表板中，单击“转到 API 概述”。
4. 选择“启用 API 和服务”。 搜索 **Google + API**，然后选择它。 单击“启用”。
5. 在左侧导航中，单击“凭据” > “OAuth 许可屏幕”，选择“电子邮件地址”，输入“产品名称”，并单击“保存”。
6. 在“凭据”选项卡中，单击“创建凭据” > “OAuth 客户端 ID”。
7. 在“创建客户端 ID”屏幕上，选择“Web 应用程序”。
8. 将之前复制的应用服务 **URL** 粘贴到“已授权的 JavaScript 源”中，然后将重定向 URI 粘贴到“已授权的重定向 URI”中。 重定向 URI 是应用程序的 URL 加上路径 */.auth/login/google/callback*。 例如，`https://contoso.azurewebsites.net/.auth/login/google/callback`。 请务必使用 HTTPS 方案。 然后单击“创建”。
9. 在下一个屏幕上，记下客户端 ID 和客户端密钥的值。

    > [!IMPORTANT]
    > 客户端密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。


## <a name="secrets"></a>向应用程序添加 Google 信息
1. 返回 [Azure 门户]，导航到应用程序。 依次单击“设置”和“身份验证/授权”。
2. 如果“身份验证/授权”功能未启用，请切换为“打开”。
3. 单击“Google”。 粘贴前面获取的应用 ID 和应用密钥值，启用应用程序所需的任何范围（可选）。 然后单击“确定”。
   
   ![][1]
   
   默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
4. （可选）要限制只有通过 Google 帐户身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“Google”。 这会要求对所有请求进行身份验证，而所有未经身份验证的请求会被重定向到 Google 进行身份验证。
5. 单击“ **保存**”。

现在，可以在应用中使用 Google 进行身份验证了。

## <a name="related-content"> </a>相关内容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 门户]: https://portal.azure.com/

