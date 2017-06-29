---
title: "Azure Active Directory B2C：应用程序注册 | Microsoft Docs"
description: "如何将应用程序注册到 Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 4649bec5290e09b8a18e3e12719e63bf5e57c0b0
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C：注册应用程序

> [!IMPORTANT]
> 从 Azure 门户中的 Azure AD B2C 边栏选项卡创建的应用程序必须从同一位置进行管理。 如果使用 PowerShell 或另一个门户编辑 B2C 应用程序，则应用程序将不受支持且不适用于 Azure AD B2C。 通过[下方内容](#faulted-apps)了解详细信息。
>

## <a name="prerequisite"></a>先决条件

若要构建可接受使用者注册和登录的应用程序，首先需要使用 Azure Active Directory B2C 租户注册应用程序。 通过使用 [创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)中所述的步骤获取自己的租户。 按照该文章中的所有步骤执行操作后，将具有固定到启动板的 B2C 功能边栏选项卡。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>导航到 B2C 功能边栏选项卡

如果你有固定到启动板的 B2C 功能边栏选项卡，则只要以 B2C 租户的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/) ，便可看到此边栏选项卡。

此外，还可以通过单击“更多服务”，然后搜索 [Azure 门户](https://portal.azure.com/)左侧导航窗格中的“Azure AD B2C”访问此边栏选项卡。

> [!IMPORTANT]
> 你需要是能够访问 B2C 功能边栏选项卡的 B2C 租户全局管理员。 任何其他租户的全局管理员或任何租户的用户均不能访问它。  可以通过 Azure 门户右上角的租户切换器切换到 B2C 租户。
>
>

## <a name="register-a-web-application"></a>注册 Web 应用程序

1. 在 Azure 门户的 B2C 功能边栏选项卡上，单击“ **应用程序**”。
1. 单击边栏选项卡顶部的“ **+添加** ”。
1. 输入应用程序的“ **名称** ”，用于向使用者描述你的应用程序。 例如，你可以输入“Contoso B2C app”。
1. 将“包括 Web 应用/Web API”开关切换到“是”。
1. 为“回复 URL”输入[适当](#choosing-a-web-app/api-reply-url)的值，回复 URL 是 Azure AD B2C 将在其中返回应用程序请求的任何令牌的终结点。 例如，输入 `https://localhost:44316/`。
1. 单击“ **创建** ”以注册你的应用程序。
1. 单击刚刚创建的应用程序，并复制稍后将在代码中使用的全局唯一“ **应用程序客户端 ID** ”。
1. 如果 Web 应用程序还将调用受 Azure AD B2C 保护的 Web API，则需：
    1. 创建应用程序机密，方法是：转到“密钥”边栏选项卡，然后单击“生成密钥”按钮。
    1. 单击“API 访问权限”，再单击“添加”，然后选择 Web API 和作用域（权限）。

> [!NOTE]
> **应用程序密钥** 是一个重要的安全凭据，应进行适当地保护。
>

## <a name="register-a-web-api"></a>注册 Web API

1. 在 Azure 门户的 B2C 功能边栏选项卡上，单击“ **应用程序**”。
1. 单击边栏选项卡顶部的“ **+添加** ”。
1. 输入应用程序的“ **名称** ”，用于向使用者描述你的应用程序。 例如，可以输入“Contoso B2C api”。
1. 将“包括 Web 应用/Web API”开关切换到“是”。
1. 为“回复 URL”输入[适当](#choosing-a-web-app/api-reply-url)的值，回复 URL 是 Azure AD B2C 将在其中返回应用程序请求的任何令牌的终结点。 例如，输入 `https://localhost:44316/`。
1. 输入**应用 ID URI**。 这是用于 Web API 的标识符。 例如，输入“notes”。 将会在下方生成完整标识符 URI。
1. 单击“ **创建** ”以注册你的应用程序。
1. 单击刚刚创建的应用程序，并复制稍后将在代码中使用的全局唯一“ **应用程序客户端 ID** ”。
1. 单击“已发布范围”。 这是定义授予其他应用程序的权限（作用域）的位置。
1. 根据需要添加更多作用域。 默认情况下，将定义“user_impersonation”作用域。 这样一来，其他应用程序就可以代表已登录用户访问该 API。 可以根据需要将其删除。
1. 单击“保存” 。

## <a name="register-a-mobilenative-application"></a>注册移动/本机应用程序

1. 在 Azure 门户的 B2C 功能边栏选项卡上，单击“ **应用程序**”。
1. 单击边栏选项卡顶部的“ **+添加** ”。
1. 输入应用程序的“ **名称** ”，用于向使用者描述你的应用程序。 例如，你可以输入“Contoso B2C app”。
1. 将“包括本机客户端”开关切换到“是”。
1. 输入使用自定义方案的**重定向 URI**。 例如，com.onmicrosoft.contoso.appname://redirect/path。 请确保选择[正确的重定向 URI ](#choosing-a-native-application-redirect-uri)，且不能包含下划线等特殊字符。
1. 单击“保存”注册应用程序。
1. 单击刚刚创建的应用程序，并复制稍后将在代码中使用的全局唯一“ **应用程序客户端 ID** ”。
1. 如果本机应用程序还将调用受 Azure AD B2C 保护的 Web API，则需：
    1. 创建应用程序机密，方法是：转到“密钥”边栏选项卡，然后单击“生成密钥”按钮。
    1. 单击“API 访问权限”，再单击“添加”，然后选择 Web API 和作用域（权限）。

> [!NOTE]
> **应用程序密钥** 是一个重要的安全凭据，应进行适当地保护。
>

## <a name="limitations"></a>限制

### <a name="choosing-a-web-appapi-reply-url"></a>选择 Web 应用/API 回复 URL

使用 Azure AD B2C 注册的应用目前仅限使用一组有限的回复 URL 值。 Web 应用和服务的回复 URL 必须以方案 `https` 开头，并且所有回复 URL 值必须共享一个 DNS 域。 例如，无法注册具有以下回复 URL 的 Web 应用：

`https://login-east.contoso.com`

`https://login-west.contoso.com`

注册系统会将现有回复 URL 的完整 DNS 名称与要添加的回复 URL 的 DNS 名称相比较。 如果满足以下任一条件，添加 DNS 名称的请求将失败：

* 新回复 URL 的完整 DNS 名称与现有回复 URL 的 DNS 名称不匹配。
* 新回复 URL 的完整 DNS 名称不是现有回复 URL 的子域。

例如，如果应用具有以下回复 URL：

`https://login.contoso.com`

你可以向其添加，如下所示：

`https://login.contoso.com/new`

在这种情况下，DNS 名称将完全匹配。 或者，你可以执行下面的操作：

`https://new.login.contoso.com`

在这种情况下，你将引用 login.contoso.com 的 DNS 子域。 如果希望应用使用 login-east.contoso.com 和 login-west.contoso.com 作为回复 URL，必须按以下顺序添加这些回复 URL：

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

可以添加后两个回复 URL，因为它们是第一个回复 URL (contoso.com) 的子域。

### <a name="choosing-a-native-application-redirect-uri"></a>选择本机应用程序重定向 URI

为移动/本机应用程序选择重定向 URI 时，有两个重要的考虑事项：

* **唯一**：每个应用程序的重定向 URI 的方案应是唯一的。 在我们的示例 (com.onmicrosoft.contoso.appname://redirect/path) 中，我们使用 com.onmicrosoft.contoso.appname 作为方案。 建议遵循此模式。 如果两个应用程序共享同一方案，用户会看到“选择应用”对话框。 如果用户的选择不正确，登录将失败。
* **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域后包含至少一个正斜杠（例如，//contoso/ 可以使用，//contoso 不能使用）。

请确保重定向 URI 中没有下划线等特殊字符。

### <a name="faulted-apps"></a>出错的应用

不应按以下方式编辑 B2C 应用程序：

* 在其他应用程序管理门户上（如 [Azure 经典门户](https://manage.windowsazure.com/)和[应用程序注册门户](https://apps.dev.microsoft.com/)）编辑。
* 使用图形 API 或 PowerShell 编辑

如果按照上述方式编辑 B2C 应用程序，并尝试在 Azure 门户上的 Azure AD B2C 功能边栏选项卡中再次进行编辑，那么它将成为出错的应用，且应用程序将无法再用于 Azure AD B2C。 需删除应用程序并重新创建。

若要删除应用，请转到[应用程序注册门户](https://apps.dev.microsoft.com/)并删除该应用程序。 若要使应用程序可见，须为该应用程序的所有者（而不仅仅是租户管理员）。

## <a name="next-steps"></a>后续步骤

将应用程序注册到 Azure AD B2C 以后，接下来可以完成[快速入门教程](active-directory-b2c-overview.md#get-started)之一，进一步做好启动和运行准备。
