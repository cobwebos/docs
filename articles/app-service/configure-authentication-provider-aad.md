---
title: 配置 Azure Active Directory 身份验证 - Azure 应用服务
description: 了解如何为应用服务应用配置 Azure Active Directory 身份验证。
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: fasttrack-edit
ms.openlocfilehash: ac73b549546c353dce4c40005b7742577e03d26c
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176985"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>将应用服务应用配置为使用 Azure AD 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何将 Azure App Service 配置为使用 Azure Active Directory （Azure AD）作为身份验证提供程序。

> [!NOTE]
> 目前，Azure AD v1.0 仅支持 Azure App Service 和 Azure Functions。 [Microsoft 标识平台](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-overview)v2.0 不支持这些版本，其中包括 Microsoft 身份验证库（MSAL）。

设置应用和身份验证时，请遵循以下最佳做法：

- 为每个应用服务应用指定自己的权限和许可。
- 将每个应用服务应用程序配置为自己的注册。
- 避免在环境之间通过单独的部署槽位使用单独的应用注册来共享权限。 测试新代码时，这种做法可帮助防止问题影响生产应用。

## <a name="express"> </a>使用快速设置进行配置

1. 在[Azure 门户]中转到应用服务应用。
1. 在左窗格中选择 "**设置**" @no__t "**身份验证/授权**"，并确保已**启用 "** **应用服务身份验证**"。
1. 选择“Azure Active Directory”，然后选择“管理模式”下的“快速”。
1. 选择“确定”，在 Azure Active Directory 中注册应用服务应用。 创建新的应用注册。

   如果要改为选择现有应用注册：

   1. 选择 "**选择现有应用**"，然后在租户中搜索以前创建的应用注册的名称。
   1. 选择 "应用注册"，然后选择 **"确定"** 。
   1. 然后在 Azure Active Directory 设置 "页上选择 **" 确定 "** 。

   默认情况下，应用服务提供身份验证，但不限制对站点内容和 Api 的授权访问。 必须在应用代码中为用户授权。
1. 可有可无若要将应用访问限制为仅限通过 Azure Active Directory 进行身份验证的用户访问，请将 "**请求未经身份验证时要执行的操作**" 设置为 " **Azure Active Directory**。 设置此功能时，应用要求对所有请求进行身份验证。 它还将所有未经身份验证的重新定向到 Azure Active Directory 进行身份验证。

    > [!CAUTION]
    > 以这种方式限制访问权限适用于对应用的所有调用，对于具有公开可用主页的应用，与在许多单页应用程序中一样，这可能并不理想。 对于此类应用程序，"**允许匿名请求（无操作）** " 可能是首选，应用程序会手动开始登录。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。
1. 选择“保存”。

## <a name="advanced"> </a>使用高级设置进行配置

如果要使用的 Azure AD 租户不同于用于登录到 Azure 的租户，可以手动配置应用设置。 若要完成此自定义配置，你需要：

1. 在 Azure AD 中创建注册。
1. 向应用服务提供一些注册详细信息。

### <a name="register"></a>在应用服务应用的 Azure AD 中创建应用注册

在配置应用服务应用时，你将需要以下信息：

- 客户端 ID
- 租户 ID
- 客户端密码（可选）
- 应用程序 ID URI

执行以下步骤：

1. 登录到[Azure 门户]并中转到应用服务应用。 记下应用程序的**URL**。 将使用它来配置 Azure Active Directory 应用注册。
1. 选择“Azure Active Directory” > “应用注册” > “新建注册”。
1. 在 "**注册应用程序**" 页中，输入应用注册的**名称**。
1. 在 "**重定向 URI**" 中，选择 " **Web** " 并输入应用服务应用的 URL，并将路径追加 `/.auth/login/aad/callback`。 例如， `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 
1. 选择“创建”。
1. 创建应用注册后，请复制**应用程序（客户端） id**和**目录（租户） id** ，以备稍后之用。
1. 选择 "**品牌**"。 在 "**主页 url**" 中，输入应用服务应用的 url，然后选择 "**保存**"。
1. 选择 "**公开 API** > **集**"。 粘贴到应用服务应用的 URL，然后选择 "**保存**"。

   > [!NOTE]
   > 此值是应用注册的**应用程序 ID URI** 。 如果 web 应用需要访问云中的 API，则在配置云应用服务资源时，需要 web 应用的**应用程序 ID URI** 。 例如，如果你希望云服务显式授予对 web 应用的访问权限，则可以使用此示例。

1. 选择“添加范围”。
   1. 在 "**作用域名称**" 中，输入*user_impersonation*。
   1. 在文本框中，输入希望用户在同意页上看到的许可范围名称和说明。 例如，输入 *"访问我的应用"* 。 
   1. 选择“添加作用域”。
1. 可有可无若要创建客户端密钥，请选择 "**证书" & 机密** > **新客户端密钥**"@no__t"**添加**"。 复制页面中显示的 "客户端机密" 值。 它不会再次显示。
1. 可有可无若要添加多个**回复 url**，请选择 "**身份验证**"。

### <a name="secrets"> </a>将 Azure Active Directory 信息添加到应用服务应用

1. 在[Azure 门户]中转到应用服务应用。 
1. 在左窗格中选择 "**设置" > "身份验证/授权**"，并确保 "**应用服务身份验证** **" 已打开**。
1. 可有可无默认情况下，应用服务身份验证允许未经身份验证的应用访问。 若要强制执行用户身份验证，请**在请求未经过身份验证时**，将操作设置为**使用 Azure Active Directory 进行登录**。
1. 在 "身份验证提供程序" 下，选择**Azure Active Directory**。
1. 在 "**管理模式**" 中，选择 "**高级**"，并根据下表配置应用服务身份验证：

    |字段|描述|
    |-|-|
    |客户端 ID| 使用应用注册的**应用程序（客户端） ID** 。 |
    |颁发者 ID| 使用 `https://login.microsoftonline.com/<tenant-id>`，并将 *@no__t 2tenant >* 替换为应用注册的**目录（租户） id** 。 |
    |客户端密码（可选）| 使用在应用注册中生成的客户端密码。|
    |允许的令牌受众| 如果这是云应用程序或服务器应用程序，并且你想要允许来自 web 应用的身份验证令牌，请在此处添加 web 应用的**应用程序 ID URI** 。 |

    > [!NOTE]
    > 无论配置**允许的令牌受众**的方式如何，配置的**客户端 ID** *始终*都被隐式视为允许的受众。
1. 选择“确定”，然后选择“保存”。

你现在可以在应用服务应用中使用 Azure Active Directory 进行身份验证。

## <a name="configure-a-native-client-application"></a>配置本机客户端应用程序

你可以注册本机客户端以允许使用客户端库（如**Active Directory 身份验证库**）进行身份验证。

1. 在[Azure 门户]中，选择 " **Active Directory**"  > **应用注册**"@no__t"**新注册**"。
1. 在 "**注册应用程序**" 页中，输入应用注册的**名称**。
1. 在 "**重定向 URI**" 中，选择 "**公用客户端（移动 & 桌面）** "，输入应用服务应用的 URL，并将路径追加 `/.auth/login/aad/callback`。 例如， `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。
1. 选择“创建”。

    > [!NOTE]
    > 对于 Windows 应用程序，请改用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作为 URI。
1. 创建应用注册后，复制 "**应用程序（客户端） ID**" 的值。
1. 选择**api 权限** >  向**我的 api** **添加权限**@no__t。
1. 选择以前为应用服务应用创建的应用注册。 如果看不到应用注册，请确保已在[为应用服务应用 Azure AD 创建应用注册](#register)中添加了**user_impersonation**作用域。
1. 选择 " **user_impersonation**"，然后选择 "**添加权限**"。

现已配置可以访问应用服务应用的本机客户端应用程序。

## <a name="related-content"></a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure 门户]: https://portal.azure.com/
[alternative method]:#advanced
