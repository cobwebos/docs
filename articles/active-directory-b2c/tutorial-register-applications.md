---
title: 教程 - 在 Azure Active Directory B2C 中注册应用程序 | Microsoft Docs
description: 了解如何使用 Azure 门户在 Azure Active Directory B2C 中注册应用程序。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 511e1e9f29e6ae7602a977819f5295f76236595d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248719"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中注册应用程序

必须在你管理的租户中注册应用程序，然后这些[应用程序](active-directory-b2c-apps.md)才能与 Azure Active Directory (Azure AD) B2C 交互。 本教程介绍如何使用 Azure 门户注册应用程序。

在本文中，学习如何：

> [!div class="checklist"]
> * 注册 Web 应用程序
> * 注册 Web API
> * 注册移动或本机应用程序

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

如果尚未创建自己的 [Azure AD B2C 租户](tutorial-create-tenant.md)，请立即创建一个。 可以使用现有租户。

## <a name="register-a-web-application"></a>注册 Web 应用程序

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。

    ![切换到订阅目录](./media/tutorial-register-applications/switch-directories.png)

2. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
3. 选择“应用程序”，然后选择“添加”。

    ![添加应用程序](./media/tutorial-register-applications/add-application.png)

4. 输入应用程序的名称。 例如，“webapp1”。
5. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。
6. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点。 例如，可以在 `https://localhost:44316` 将其设置为本地侦听，如果还不知道端口号，可以先输入占位符值，以后再进行更改。 出于测试目的，可以将其设置为 `https://jwt.ms`，其显示令牌的内容以供检查。 对于本教程，请将其设置为 `https://jwt.ms`。 

    回复 URL 必须以方案 `https` 开头，并且所有回复 URL 值必须共享一个 DNS 域。 例如，如果应用程序的回复 URL 为 `https://login.contoso.com`，则可以像此 URL `https://login.contoso.com/new` 一样向其添加。 或者，你可以引用 `login.contoso.com` 的 DNS 子域，例如 `https://new.login.contoso.com`。 若要将包含 `login-east.contoso.com` 和 `login-west.contoso.com` 的应用程序作为回复 URL，则必须按以下顺序添加这些回复 URL：`https://contoso.com`、`https://login-east.contoso.com`、`https://login-west.contoso.com`。 可以添加后两个回复 URL，因为它们是第一个回复 URL (`contoso.com`) 的子域。

7. 单击“创建”。

    ![设置应用程序属性](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>创建客户端机密

如果应用程序将代码交换为令牌，则需要创建应用程序机密。

1. 选择“密钥”，然后单击“生成密钥”。

    ![生成密钥](./media/tutorial-register-applications/generate-keys.png)

2. 选择“保存”以查看密钥。 记下“应用密钥”值。 在应用程序的代码中将该值用作应用程序机密。

    ![保存密钥](./media/tutorial-register-applications/save-key.png)
    
3. 选择“API 访问权限”，单击“添加”，然后选择 Web API 和范围（权限）。

    ![配置 API 的访问权限](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>注册 Web API

1. 选择“应用程序”，然后选择“添加”。
3. 输入应用程序的名称。 例如，“webapi1”。
4. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。
5. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点。 例如，可将其设置为在 `https://localhost:44316` 上本地侦听。 如果尚不知道端口号，可以先输入占位符值，以后再进行更改。
6. 对于“应用 ID URI”，请输入 Web API 使用的标识符。 包括域在内的完整标识符 URI 是为你生成的。 例如，`https://contosotenant.onmicrosoft.com/api`。
7. 单击“创建”。
8. 选择创建的“webapi1”应用程序，然后选择“已发布的作用域”以根据需要添加更多作用域。 默认已定义 `user_impersonation` 范围。 `user_impersonation` 范围使其他应用程序可以代表已登录用户访问此 API。 如果需要，可以删除 `user_impersonation` 范围。

    ![设置已发布的作用域](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>注册移动或本机应用程序

1. 选择“应用程序”，然后选择“添加”。
2. 输入应用程序的名称。 例如，“nativeapp1”。
3. 对于“包括 Web 应用/Web API”，请选择“否”。
4. 对于“包括本机客户端”，请选择“是”。
5. 对于“重定向 URI”，请使用自定义方案输入有效的重定向 URI。 选择重定向 URI 时，有两个重要的注意事项：

    - **唯一** - 每个应用程序的重定向 URI 方案应是唯一的。 在示例 `com.onmicrosoft.contoso.appname://redirect/path` 中，`com.onmicrosoft.contoso.appname` 为方案。 应遵循此模式。 如果两个应用程序共享同一方案，则用户应选择一个应用程序。 如果用户的选择不正确，登录会失败。
    - **完整** - 重定向 URI 必须同时包含方案和路径。 路径必须在域之后包含至少一个正斜杠。 例如，`//contoso/` 有效，而 `//contoso` 会失败。 确保重定向 URI 不包含特殊字符，例如下划线。

6. 单击“创建”。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 注册 Web 应用程序
> * 注册 Web API
> * 注册移动或本机应用程序

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中创建用户流](tutorial-create-user-flows.md)