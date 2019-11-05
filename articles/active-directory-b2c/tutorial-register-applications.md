---
title: 教程 - 注册应用程序 - Azure Active Directory B2C
description: 了解如何使用 Azure 门户在 Azure Active Directory B2C 中注册 Web 应用程序。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 00b7d1cfea4a36b8ba5b78aea344288e11da67cf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474735"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中注册应用程序

必须在你管理的租户中注册应用程序，然后这些[应用程序](active-directory-b2c-apps.md)才能与 Azure Active Directory B2C (Azure AD B2C) 交互。 本教程介绍如何使用 Azure 门户注册 Web 应用程序。

在本文中，学习如何：

> [!div class="checklist"]
> * 注册 Web 应用程序
> * 创建客户端机密

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

如果尚未创建自己的 [Azure AD B2C 租户](tutorial-create-tenant.md)，请立即创建一个。 可以使用现有的 Azure AD B2C 租户。

## <a name="register-a-web-application"></a>注册 Web 应用程序

若要在 Azure AD B2C 租户中注册应用程序，你可以使用当前**应用程序**体验或我们的新的统一**应用注册（预览版）** 体验。 [了解有关预览版体验的详细信息](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
1. 选择“应用程序”，然后选择“添加”。
1. 输入应用程序的名称。 例如，“webapp1”。
1. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。
1. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点。 例如，可将其设置为在 `https://localhost:44316` 上本地侦听。 如果尚不知道端口号，可以先输入占位符值，以后再进行更改。

    出于测试目的（如本教程），可以将其设置为 `https://jwt.ms`，以便显示令牌的内容以供检查。 对于本教程，请将**回复 URL** 设置为 `https://jwt.ms`。

    以下限制适用于回复 URL：

    * 回复 URL 必须以方案 `https` 开头。
    * 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此如果重定向到大小写不匹配的 `.../abc/response-oidc` URL，则可能会排除与 `.../ABC/response-oidc` 关联的 Cookie。

1. 单击“创建”以完成应用程序注册。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册（预览）](#tab/app-reg-preview/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择 "**应用注册（预览版）** "，然后选择 "**新注册**"。
1. 输入应用程序的**名称**。 例如，“webapp1”。
1. 选择**任何组织目录或任何标识提供者中的帐户**。
1. 在 "**重定向 URI**" 下，选择 " **Web**"，然后在 "URL" 文本框中输入 `https://jwt.ms`。

    "重定向 URI" 是授权服务器向其发送用户的终结点（在此示例中为 Azure AD B2C，在这种情况下，在完成其与用户的交互之后，以及成功授权后发送访问令牌或授权代码的终结点。 在生产应用程序中，它通常是在其中运行应用程序的可公开访问的终结点，如 `https://contoso.com/auth-response`。 对于像本教程这样的测试目的，你可以将其设置为 `https://jwt.ms`，这是 Microsoft 拥有的 web 应用程序，用于显示令牌的已解码内容（令牌的内容绝不会离开你的浏览器）。 在应用程序开发过程中，可以添加应用程序在本地侦听的终结点，如 `https://localhost:5000`。 随时可以添加和修改已注册应用程序中的重定向 Uri。

    以下限制适用于重定向 Uri：

    * 回复 URL 必须以方案 `https` 开头。
    * 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此如果重定向到大小写不匹配的 `.../abc/response-oidc` URL，则可能会排除与 `.../ABC/response-oidc` 关联的 Cookie。

1. 在 "**权限**" 下，选中 "向*管理员同意 openid 和 offline_access 权限*" 复选框。
1. 选择“注册”。

完成应用程序注册后，启用隐式授权流：

1. 在 "**管理**" 下选择 "**身份验证**"。
1. 选择 **"试用新体验**（如果已显示）"。
1. 在 "**隐式授予**" 下，同时选择 "**访问令牌**" 和 " **ID 令牌**" 复选框。
1. 选择“保存”。

* * *

## <a name="create-a-client-secret"></a>创建客户端机密

如果应用程序将代码交换为令牌，则需要创建应用程序机密。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 在“Azure AD B2C - 应用程序”页中，选择已创建的应用程序，例如 webapp1。
1. 选择“密钥”，然后选择“生成密钥”。
1. 选择“保存”以查看密钥。 记下“应用密钥”值。 在应用程序的代码中将此值用作应用程序机密。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册（预览）](#tab/app-reg-preview/)

1. 在 " **Azure AD B2C 应用注册（预览）** " 页中，选择你创建的应用程序，例如*webapp1*。
1. 在 "**管理**" 下，选择 "**证书 & 机密**"。
1. 选择“新建客户端机密”。
1. 在 "**说明**" 框中输入客户端密码的说明。 例如， *clientsecret1*。
1. 在 "**过期**" 下，选择机密有效的持续时间，然后选择 "**添加**"。
1. 记录机密的**值**。 在应用程序的代码中将此值用作应用程序机密。

* * *

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 注册 Web 应用程序
> * 创建客户端机密

接下来，了解如何创建用户流以使用户能够注册、登录和管理其个人资料。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中创建用户流](tutorial-create-user-flows.md)
