---
title: 配置 Microsoft 帐户身份验证 - Azure 应用服务
description: 了解如何为应用服务应用程序配置 Microsoft 帐户身份验证。
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 17410db91f55a053e5ec208492649157bb0b5034
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881104"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>如何将应用服务应用程序配置为使用 Microsoft 帐户登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题说明如何将 Azure 应用服务配置为使用 Microsoft 帐户作为身份验证提供程序。 

## <a name="register-microsoft-account"> </a>使用 Microsoft 帐户注册应用
1. 登录到[Azure 门户], 并导航到应用程序。 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. 导航到 "[**应用注册**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)", 并根据你的 Microsoft 帐户登录 (如果需要)。

1. 单击 "**新建注册**", 然后键入应用程序名称。

1. 在 "**重定向 uri**" 中, 选择 " `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application` **Web**", 然后键入。 将 *\<app-域名 >* 替换为应用的域名。  例如， `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback` 。 

   > [!NOTE]
   > 使用 URL 中的 HTTPS 方案。

1. 选择 "**注册**"。 

1. 复制**应用程序 (客户端) ID**。 稍后需要用到此信息。 
   
7. 从新应用注册的左侧导航栏中, 选择 "**证书" &**  > "**新的客户端密钥**"。 提供 "说明", 选择 "有效期", 然后选择 "**添加**"。

1. 复制 "**证书 & 机密**" 页中显示的值。 关闭页面后，就不再显示该值。

    > [!IMPORTANT]
    > 密码是一个非常重要的安全凭据。 请不要与任何人共享密码或者在客户端应用程序中分发它。

## <a name="secrets"> </a>向应用服务应用程序添加 Microsoft 帐户信息
1. 在 [Azure 门户]中，导航到应用程序。 在左侧导航栏中, 单击 "**身份验证/授权**"。

2. 如果未启用 "身份验证/授权" 功能, 请选择 "**打开**"。

3. 在 "**身份验证提供程序**" 下, 选择 " **Microsoft 帐户**"。 粘贴前面获取的应用程序 (客户端) ID 和客户端密码, 并根据需要启用应用程序所需的任何范围。 然后单击“确定”。

    默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。

4. 可有可无若要限制对 Microsoft 帐户用户的访问, 请将 "**请求未经身份验证时要执行的操作**" 设置为 "**使用 Microsoft 帐户登录**"。 这会要求对所有请求进行身份验证，所有未经身份验证的请求将重定向到 Microsoft 帐户进行身份验证。

5. 单击“保存”。

现在，可以使用 Microsoft 帐户在应用中进行身份验证。

## <a name="related-content"> </a>相关内容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 门户]: https://portal.azure.com/
