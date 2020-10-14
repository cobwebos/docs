---
title: 快速入门：调用受 Microsoft 标识平台保护的 ASP.NET Web API | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍如何从 Windows 桌面 (WPF) 应用程序调用受 Microsoft 标识平台保护的 ASP.NET Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 786f566b121d5f0d5d64e7b8b269c7cdfab9e4a6
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825067"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>快速入门：调用受 Microsoft 标识平台保护的 ASP.NET Web API

本快速入门将公开一个 Web API 并对其进行保护，以便只有通过身份验证的用户才能访问它。 本文演示如何公开 ASP.NET Web API，使其可以接受由个人帐户（例如 outlook.com 或 live.com）以及由集成了 Microsoft 标识平台的任何公司或组织的工作或学校帐户发出的令牌。

本文还使用 Windows Presentation Foundation (WPF) 应用演示如何请求访问令牌来访问 Web API。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Visual Studio 2017 或 2019。 免费下载 [Visual Studio](https://www.visualstudio.com/downloads/)。

## <a name="clone-or-download-the-sample"></a>克隆或下载示例

可以通过以下两种方式之一获取该示例：

* 在 shell 或命令行中进行克隆：
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [以 ZIP 文件下载](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)。

## <a name="register-your-web-api"></a>注册 Web API

本部分中将在 Azure 门户的“应用注册”中注册 Web API。

### <a name="choose-your-azure-ad-tenant"></a>选择 Azure AD 租户

若要手动注册应用，请选择要在其中创建应用的 Azure Active Directory (Azure AD) 租户。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果帐户位于多个 Azure AD 租户中，请在右上角选择你的个人资料，然后选择“切换目录”。
1. 将门户会话更改为所需的 Azure AD 租户。

### <a name="register-the-todolistservice-app"></a>注册 TodoListService 应用

1. 转到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)门户。
1. 选择“新注册”。
1. “注册应用程序”页出现后，请输入应用程序的注册信息：

    1. 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称。 例如，输入 AppModelv2-NativeClient-DotNet-TodoListService。
    1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户” 。
    1. 选择“注册”以创建应用程序。

1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，然后记下该值以供后续使用 。 你将需要使用该值为此项目配置 Visual Studio 配置文件（即 TodoListService\Web.config 文件中的 `ClientId`）。

1. 在“公开 API”部分，选择“添加范围”，通过选择“保存并继续”来接受建议的应用程序 ID URI (`api://{clientId}`)，然后输入以下信息  ：

    1. 对于“范围名称”，请输入“access_as_user” 。
    1. 对于“谁能同意?”，请确保选择了“管理员和用户”选项 。
    1. 在“管理员同意显示名称”框中，输入“以用户身份访问 TodoListService” 。
    1. 在“管理员同意说明”框中，输入“以用户身份访问 TodoListService Web API” 。
    1. 在“用户同意显示名称”框中，输入“以用户身份访问 TodoListService” 。
    1. 在“用户同意说明”框中，输入“以用户身份访问 TodoListService Web API” 。
    1. 对于“状态”，保留“启用” 。
    1. 选择“添加范围”。

### <a name="configure-the-service-project"></a>配置服务项目

按照以下步骤，配置服务项目以匹配注册的 Web API：

1. 在 Visual Studio 中打开解决方案，然后在“TodoListService”项目的根目录下打开 Web.config 文件。

1. 将 `ida:ClientId` 参数的值替换为刚才在“应用注册”门户中注册的应用程序中的客户端 ID（应用程序 ID）值。

### <a name="add-the-new-scope-to-the-appconfig-file"></a>将新范围添加到 app.config 文件

若要将新范围添加到 TodoListClient 的 app.config 文件中，请执行以下操作：

1. 在 TodoListClient 项目根文件夹中，打开 app.config 文件。

1. 将 TodoListService 项目中刚注册的应用程序 ID 粘贴到 `TodoListServiceScope` 参数中，并替换 `{Enter the Application ID of your TodoListService from the app registration portal}` 字符串。

  > [!NOTE]
  > 请确保应用程序 ID 使用以下格式：`api://{TodoListService-Application-ID}/access_as_user`（其中 `{TodoListService-Application-ID}` 是表示 TodoListService 应用的应用程序 ID 的 GUID）。

## <a name="register-the-todolistclient-client-app"></a>注册 TodoListClient 客户端应用

本部分将在 Azure 门户的“应用注册”中注册 TodoListClient 应用，然后在 TodoListClient 项目中配置代码。 如果客户端和服务器视为相同的应用程序，则可重复使用在“步骤 2”中注册的同一应用程序。 如果希望用户使用 Microsoft 个人帐户登录，则可使用相同的应用程序。

### <a name="register-the-app"></a>注册应用

若要注册 TodoListClient 应用，请执行以下操作：

1. 转到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)门户。
1. 选择“新注册”。
1. “注册应用程序”页出现后，请输入应用程序的注册信息：

    1. 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称，例如 NativeClient-DotNet-TodoListClient 。
    1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户” 。
    1. 选择“注册”以创建应用程序。

   > [!NOTE]
   > 在 TodoListClient 项目的 app.config 文件中，将 `ida:Tenant` 的默认值设置为 `common`。 可能的值为：
   > - `common`：可以使用工作或学校帐户或 Microsoft 个人帐户登录（因为你在步骤 3b 中选择了“任何组织目录中的帐户”）。
   > - `organizations`：可使用工作或学校帐户登录。
   > - `consumers`：只能使用 Microsoft 个人帐户登录。

1. 在应用的“概述”页面，选择“身份验证”，然后执行以下操作 ：

    1. 在“平台配置”下，选择“添加平台”按钮 。
    1. 对于“移动和桌面应用程序”，选择“移动和桌面应用程序” 。
    1. 对于“重定向 URI”，请选择“https://login.microsoftonline.com/common/oauth2/nativeclient”复选框 。
    1. 选择“配置” 。

1. 选择“API 权限”，然后执行以下操作：

    1. 选择“添加权限”按钮。
    1. 选择“我的 API”选项卡。
    1. 在 API 列表中，选择 AppModelv2-NativeClient-DotNet-TodoListService API 或选择为 Web API 输入的名称。
    1. 如果“access_as_user”权限复选框未处于选中状态，请将其选中。 如有必要，请使用搜索框。
    1. 选择“添加权限”按钮。

### <a name="configure-your-project"></a>配置项目

若要配置 TodoListClient 项目，请执行以下操作：

1. 在“应用注册门户”的“概述”页中，复制“应用程序(客户端) ID”的值  。

1. 打开位于“TodoListClient”项目根文件夹中的 app.config 文件，然后将应用程序 ID 值粘贴到 `ida:ClientId` 参数中。

## <a name="run-your-todolistclient-project"></a>运行 TodoListClient 项目

若要运行 TodoListClient 项目，请执行以下操作：

1. 按 F5 打开 TodoListClient 项目。 项目页面应打开。

1. 选择右上角的“登录”，然后使用注册应用程序时所用的同一凭据登录，或以同一目录中的用户身份登录。

   如果你是首次登录，系统可能会提示你同意 TodoListService Web API。

   登录时还需要请求 access_as_user 范围的访问令牌，用于访问 TodoListService Web API 并处理“待办事项”列表 。

## <a name="pre-authorize-your-client-application"></a>为客户端应用程序预先授权

允许其他目录中的用户访问 Web API 的一种方法是，预先授权客户端应用程序访问 Web API。 为此，可将客户端应用中的应用程序 ID 添加到 Web API 的预授权应用程序列表中。 添加预授权客户端，即表示你允许用户访问你的 Web API，而不必再征求你的同意。 若要预授权客户端应用，请执行以下操作：

1. 在“应用注册”门户中，打开 TodoListService 应用的属性。
1. 在“公开 API”部分，选择“授权的客户端应用程序”部分下的“添加客户端应用程序”  。
1. 在“客户端 ID”框中，粘贴 TodoListClient 应用的应用程序 ID。
1. 在“授权范围”部分中，为此 `api://<Application ID>/access_as_user` Web API 选择范围。
1. 选择“添加应用程序”。

### <a name="run-your-project"></a>运行项目

1. 按 F5 运行项目。 随即应打开 TodoListClient 应用。
1. 选择右上角的“登录”，然后使用个人 Microsoft 帐户（例如 live.com 或 hotmail.com）或工作或学校帐户登录。

## <a name="optional-limit-sign-in-access-to-certain-users"></a>可选：限制对特定用户的登录访问

默认情况下，当你执行前面的步骤时，任何个人帐户（如 outlook.com 或 live.com），或者集成了 Azure AD 的组织的工作或学校帐户都可以请求令牌并访问你的 Web API。

若要指定可登录应用程序的人员，请使用以下某个选项：

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>选项 1：将访问权限限为单个组织（单个租户）

可以仅限单个 Azure AD 租户中的用户帐户（包括该租户的“来宾帐户”）登录应用程序。 此场景对业务线应用程序较为常见：

1. 打开“App_Start\Startup.Auth”文件，然后将传递到 `OpenIdConnectSecurityTokenProvider` 的元数据终结点的值更改为 `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`。 还可使用租户名称，例如 `contoso.onmicrosoft.com`。
2. 在同一文件中，将 `TokenValidationParameters` 中的 `ValidIssuer` 属性设置为 `"https://sts.windows.net/{Tenant ID}/"`，并将 `ValidateIssuer` 参数设置为 `true`。

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>选项 2：使用自定义方法来验证颁发者

可通过 `IssuerValidator` 参数实现自定义方法来验证颁发者。 有关此参数的详细信息，请参阅 [TokenValidationParameters 类](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true)。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤
详细了解 Microsoft 标识平台支持的受保护 Web API 方案：
> [!div class="nextstepaction"]
> [受保护的 Web API 方案](scenario-protected-web-api-overview.md)
