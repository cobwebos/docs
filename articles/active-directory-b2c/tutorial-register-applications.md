---
title: 教程 - 注册应用程序以便能够使用 Azure Active Directory B2C 进行注册和登录 | Microsoft Docs
description: 使用 Azure 门户创建 Azure AD B2C 租户并向其注册应用程序。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: patricka
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.openlocfilehash: 85e61f96ff829052e857d4823b3c944c6d981d2f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>教程：注册应用程序以便能够使用 Azure Active Directory B2C 进行注册和登录

本教程帮助你在数分钟内创建一个 Microsoft Azure Active Directory (Azure AD) B2C 租户并向其注册一个应用程序。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅
> * 注册应用程序

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

无法在现有的租户中启用 B2C 功能。 需要创建一个 Azure AD B2C 租户。

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

恭喜，已成功创建 Azure Active Directory B2C 租户。 你是该租户的全局管理员。 可以根据需要添加其他全局管理员。 若要切换到新租户，请单击“管理新租户”链接。

![“管理新租户”链接](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> 如果计划将 B2C 租户用于生产应用，请阅读[生产规模与预览 B2C 租户](active-directory-b2c-reference-tenant-type.md)一文。 当删除现有 B2C 租户并重新创建具有相同域名的 B2C 租户时，存在已知问题。 需创建具有不同域名的 B2C 租户。
>
>

## <a name="link-your-tenant-to-your-subscription"></a>将租户链接到订阅

需要将 Azure AD B2C 租户链接至 Azure 订阅，才能启用所有 B2C 功能并支付使用费。 若要了解详细信息，请参阅[此文章](active-directory-b2c-how-to-enable-billing.md)。 如果未将 Azure AD B2C 租户链接到 Azure 订阅，则某些功能会被阻止，且 B2C 设置中将显示一条警告消息（“未将任何订阅链接到此 B2C 租户，或者订阅需要关注。”）。 在将应用转移到生产环境之前，必须执行此步骤。


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

也可在门户顶部的“搜索资源”中输入 `Azure AD B2C` 来访问边栏选项卡。 在结果列表中，选择“Azure AD B2C”即可访问 B2C 设置边栏选项卡。

## <a name="register-your-application"></a>注册应用程序

若要构建可接受使用者注册和登录的应用程序，首先需要使用 Azure Active Directory B2C 租户注册应用程序。 通过使用 [创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)中所述的步骤获取自己的租户。

在 Azure 门户中创建的应用程序必须从同一位置进行管理。 如果使用 PowerShell 或另一个门户编辑 Azure AD B2C 应用程序，则应用程序不受支持且不适用于 Azure AD B2C。 有关详细信息，请参阅[出错的应用](#faulted-apps)部分。 

本文所用示例有助于示例入门。 可以在后续文章中详细了解这些示例。

### <a name="navigate-to-b2c-settings"></a>导航到 B2C 设置

以 B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>根据应用程序类型选择后续步骤

* [注册 Web 应用程序](#register-a-web-app)
* [注册 Web API](#register-a-web-api)
* [注册移动或本机应用程序](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>注册 Web 应用

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>创建 Web 应用客户端机密

如果 Web 应用程序调用受 Azure AD B2C 保护的 Web API，请执行以下步骤：
   1. 创建应用程序机密，方法是：转到“密钥”边栏选项卡，并单击“生成密钥”按钮。 记下“应用密钥”值。 在应用程序的代码中将该值用作应用程序机密。
   2. 单击“API 访问权限”，再单击“添加”，然后选择 Web API 和作用域（权限）。

> [!NOTE]
> **应用程序密钥** 是一个重要的安全凭据，应进行适当地保护。
> 

[跳转到“后续步骤”](#next-steps)

### <a name="register-a-web-api"></a>注册 Web API

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

单击“已发布作用域”，根据需要添加更多作用域。 默认情况下，定义“user_impersonation”作用域。 有了 user_impersonation 作用域，其他应用程序就可以代表已登录用户访问该 API。 可以随意删除 user_impersonation 作用域。

[跳转到“后续步骤”](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>注册移动或本机应用

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[跳转到“后续步骤”](#next-steps)

### <a name="choosing-a-web-app-or-api-reply-url"></a>选择 Web 应用或 API 回复 URL

使用 Azure AD B2C 注册的应用目前仅限使用一组有限的回复 URL 值。 Web 应用和服务的回复 URL 必须以方案 `https` 开头，并且所有回复 URL 值必须共享一个 DNS 域。 例如，无法注册具有以下回复 URL 的 Web 应用：

`https://login-east.contoso.com`

`https://login-west.contoso.com`

注册系统会将现有回复 URL 的完整 DNS 名称与要添加的回复 URL 的 DNS 名称相比较。 如果满足以下任一条件，添加 DNS 名称的请求会失败：

* 新回复 URL 的完整 DNS 名称与现有回复 URL 的 DNS 名称不匹配。
* 新回复 URL 的完整 DNS 名称不是现有回复 URL 的子域。

例如，如果应用具有以下回复 URL：

`https://login.contoso.com`

可以向其添加，如下所示：

`https://login.contoso.com/new`

在这种情况下，DNS 名称将完全匹配。 或者，可以执行下面的操作：

`https://new.login.contoso.com`

在这种情况下，将引用 login.contoso.com 的 DNS 子域。如果希望应用使用 login-east.contoso.com 和 login-west.contoso.com 作为回复 URL，必须按以下顺序添加这些回复 URL：

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

可以添加后两个回复 URL，因为它们是第一个回复 URL (contoso.com) 的子域。

### <a name="choosing-a-native-app-redirect-uri"></a>选择本机应用重定向 URI

为移动/本机应用程序选择重定向 URI 时，有两个重要的考虑事项：

* **唯一**：每个应用程序的重定向 URI 的方案应是唯一的。 在示例 (com.onmicrosoft.contoso.appname://redirect/path) 中，com.onmicrosoft.contoso.appname 是方案。 建议遵循此模式。 如果两个应用程序共享同一方案，用户会看到“选择应用”对话框。 如果用户的选择不正确，登录会失败。
* **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域后包含至少一个正斜杠（例如，//contoso/ 可以使用，//contoso 不能使用）。

请确保重定向 URI 中没有下划线等特殊字符。

### <a name="faulted-apps"></a>出错的应用

不应按以下方式编辑 B2C 应用程序：

* 在其他应用程序管理门户（如[应用程序注册门户](https://apps.dev.microsoft.com/)）中编辑。
* 使用图形 API 或 PowerShell 编辑

如果按照所述方式编辑 Azure AD B2C 应用程序，并尝试在 Azure 门户的 Azure AD B2C 功能中再次进行编辑，那么它将成为出错的应用，且应用程序无法再用于 Azure AD B2C。 需删除应用程序，然后重新创建它。

若要删除应用，请转到[应用程序注册门户](https://apps.dev.microsoft.com/)并删除该应用程序。 若要使应用程序可见，须为该应用程序的所有者（而不仅仅是租户管理员）。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅
> * 注册应用程序

> [!div class="nextstepaction"]
> [使 Web 应用程序能够使用帐户进行身份验证](active-directory-b2c-app-registration.md)