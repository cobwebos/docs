---
title: 配置 Azure AD 身份验证
description: 了解如何将 Azure Active Directory 身份验证配置为应用服务或 Azure Functions 应用的标识提供者。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 60a5d50b511fc9db02daa9b7e74eedfe40eeb7a5
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2020
ms.locfileid: "82609895"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>将应用服务或 Azure Functions 应用配置为使用 Azure AD 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍了如何将 Azure 应用服务或 Azure Functions 配置为使用 Azure Active Directory (Azure AD) 作为身份验证提供者。

> [!NOTE]
> 快速设置流设置的是 AAD V1 应用注册。 若要使用 [Azure Active Directory v2.0](../active-directory/develop/v2-overview.md)（包括 [MSAL](../active-directory/develop/msal-overview.md)），请按照[高级配置说明](#advanced)操作。

在设置应用和身份验证时，请遵循以下最佳做法：

- 为每个应用服务应用指定它自己的权限和同意。
- 为每个应用服务应用配置它自己的注册。
- 通过对不同的部署槽位使用不同的应用注册，避免环境之间的权限共享。 在测试新代码时，这种做法可有助于防止出现影响生产应用的问题。

> [!NOTE]
> 此功能暂不适用于 Azure Functions 的 Linux 消耗计划

## <a name="configure-with-express-settings"></a><a name="express"> </a>使用快速设置进行配置

> [!NOTE]
> “快速”选项不适用于政府云。

1. 在 [Azure 门户]中，搜索并选择“应用服务”，然后选择你的应用。
2. 在左侧导航栏中，依次选择“身份验证/授权” > “开”。
3. 依次选择“Azure Active Directory” > “快速”。

   若要改为选择现有应用注册，请执行以下操作：

   1. 选择“选择现有 AD 应用”，然后单击“Azure AD 应用”。
   2. 选择现有应用注册，然后单击“确定”。

3. 选择“确定”，在 Azure Active Directory 中注册应用服务应用。 此时会新建一个应用注册。

    ![Azure Active Directory 中的快速设置](./media/configure-authentication-provider-aad/express-settings.png)

4. （可选）默认情况下，应用服务提供身份验证，但不限制对站点内容和 API 的授权访问。 必须在应用代码中为用户授权。 若要限制为只有经过 Azure Active Directory 身份验证的用户才能访问应用，请将“请求未经验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。 如果你设置此功能，应用会要求对所有请求进行身份验证。 它还将所有未经身份验证的请求都重定向到 Azure Active Directory 进行身份验证。

    > [!CAUTION]
    > 以这种方式限制访问权限适用于对应用的所有调用，但这对于有可公开访问的主页的应用（如许多单页应用）可能并不可取。 对于此类应用，“允许匿名请求(无操作)”可能是首选，因为应用是手动启动登录本身。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。
5. 选择“保存”。

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>使用高级设置进行配置

若要使用来自其他 Azure AD 租户的应用注册，可以手动配置应用设置。 若要完成此自定义配置，请执行以下操作：

1. 在 Azure AD 中创建注册。
2. 向应用服务提供一些注册详细信息。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>在 Azure AD 中为应用服务应用创建应用注册

在配置应用服务应用时，需要以下信息：

- 客户端 ID
- 租户 ID
- 客户端密码（可选）
- 应用 ID URI

执行以下步骤：

1. 登录 [Azure 门户]，搜索并选择“应用服务”，然后选择你的应用。 记下应用的 URL。 稍后要使用它来配置 Azure Active Directory 应用注册。
1. 依次选择“Azure Active Directory” > “应用注册” > “新建注册”。
1. 在“注册应用”页上的“名称”中，输入应用注册的名称。
1. 在“重定向 URI”中，选择“Web”，然后键入“`<app-url>/.auth/login/aad/callback`”。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback` 。
1. 选择“创建”。
1. 在应用注册创建后，复制“应用(客户端) ID”和“目录(租户) ID”，以供稍后使用。
1. 选择“身份验证”。 在“隐式授权”下，启用“ID 令牌”，以允许 OpenID Connect 用户从应用服务登录。
1. （可选）选择“品牌”。 在“主页 URL”中，输入应用服务应用的 URL，然后选择“保存”。
1. 依次选择“公开 API” > “设置”。 对于单租户应用，请粘贴应用服务应用的 URL，然后选择“保存”；对于多租户应用，请粘贴基于租户验证域之一的 URL，然后选择“保存”。

   > [!NOTE]
   > 此值是应用注册的应用 ID URI。 如果 Web 应用需要访问云中的 API，则在配置云应用服务资源时，需要使用 Web 应用的应用 ID URI。 例如，如果希望云服务显式向 Web 应用授予访问权限，则可以使用此值。

1. 选择“添加范围”。
   1. 在“范围名称”中，输入“user_impersonation”。
   1. 在文本框中，输入你希望用户在同意页上看到的同意范围名称和说明。 例如，输入“访问我的应用”。
   1. 选择“添加范围”。
1. （可选）若要创建客户端密码，请依次选择“证书和密码” > “新客户端密码” > “添加”。 复制页面中显示的客户端密码值。 它不会再次显示。
1. （可选）若要添加多个回复 URL，请选择“身份验证”。

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>在应用服务应用中启用 Azure Active Directory

1. 在 [Azure 门户]中，搜索并选择“应用服务”，然后选择你的应用。
1. 在左侧窗格中的“设置”下，依次选择“身份验证/授权” > “开”。
1. （可选）默认情况下，应用服务身份验证允许未经身份验证的用户访问你的应用。 若要强制执行用户身份验证，请将“请求未经验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。
1. 在“身份验证提供程序”下，选择“Azure Active Directory”。
1. 在“管理模式”中，选择“高级”，然后根据下表来配置应用服务身份验证：

    |字段|说明|
    |-|-|
    |客户端 ID| 使用应用注册的应用（客户端）ID。 |
    |颁发者 URL| 使用 `<authentication-endpoint>/<tenant-id>/v2.0`，并将 \<authentication-endpoint> 替换为[云环境的身份验证终结点](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints)（例如，全局 Azure 的“https://login.microsoft.com ”），同时将 \< tenant-id> 替换为在其中创建应用注册的目录（租户）的 ID。 此值用于将用户重定向到正确的 Azure AD 租户，并下载适当的元数据，以确定相应的令牌签名密钥和令牌颁发者声明值等。 对于使用 AAD v1 的应用，可以省略 `/v2.0` 部分。 |
    |客户端密码（可选）| 使用在应用注册中生成的客户端密码。|
    |允许的令牌受众| 如果这是云应用或服务器应用，并且你希望允许使用 Web 应用中的身份验证令牌，请在此处添加 Web 应用的应用 ID URI。 配置的客户端 ID 始终被隐式地视为允许的受众。 |

2. 选择“确定”，然后选择“保存” 。

现在，可以使用 Azure Active Directory 在应用服务应用中进行身份验证。

## <a name="configure-a-native-client-application"></a>配置本机客户端应用程序

可以注册本机客户端，以允许使用客户端库（如 Active Directory 身份验证库）向应用中托管的 Web API 进行身份验证。

1. 在 [Azure 门户]中，依次选择“Active Directory” > “应用注册” > “新建注册”。
1. 在“注册应用”页上的“名称”中，输入应用注册的名称。
1. 在“重定向 URI”中，选择“公共客户端(移动和桌面)”，然后键入 URL“`<app-url>/.auth/login/aad/callback`”。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback` 。

    > [!NOTE]
    > 对于 Microsoft Store 应用，请改用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作为 URI。
1. 选择“创建”。
1. 在应用注册创建后，复制“应用(客户端) ID”的值。
1. 依次选择“API 权限” > “添加权限” > “我的 API”。
1. 选择先前为应用服务应用创建的应用注册。 如果看不到应用注册，请确保已在[在 Azure AD 中为应用服务应用创建应用注册](#register)中添加了 user_impersonation 范围。
1. 依次选择“user_impersonation”和“添加权限”。

现已配置可以代表用户访问应用服务应用的本机客户端应用。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure 门户]: https://portal.azure.com/
