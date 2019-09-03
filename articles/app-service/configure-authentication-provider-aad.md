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
ms.custom: seodec18
ms.openlocfilehash: 8b4b6549f9553773cc44c311f49befbb3eec9dc9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233094"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>将应用服务应用配置为使用 Azure Active Directory 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> 目前, Azure App Service 和 Azure Functions 不支持 AAD V2 (包括 MSAL)。
>

本文介绍如何将 Azure App Service 配置为使用 Azure Active Directory 身份验证提供程序。

建议使用自己的注册配置每个应用服务应用, 使其拥有自己的权限和许可。 此外, 请考虑对单独的部署槽位使用单独的应用注册。 这可以避免在环境之间共享权限, 因此, 要测试的新代码中出现的问题不会影响生产。

## <a name="express"> </a>使用快速设置进行配置

1. 在 [Azure 门户]中，导航到应用服务应用。 在左侧导航栏中，选择“身份验证/授权”。
2. 如果尚未启用“身份验证/授权”，请选择“启用”。
3. 选择“Azure Active Directory”，然后选择“管理模式”下的“快速”。
4. 选择“确定”，在 Azure Active Directory 中注册应用服务应用。 这会创建一个新的应用注册。 如果想要选择现有应用注册，请单击“选择现有应用”，并在租户中搜索以前创建的应用注册的名称。 单击应用注册将其选中，然后单击“确定”。 然后在 Azure Active Directory 设置页上单击“确定”。
默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
5. 可有可无若要将对应用的访问限制为仅由 Azure Active Directory 身份验证的用户访问, 请将 "**请求未经身份验证时要执行的操作**" 设置为 " **Azure Active Directory**。 这会要求对所有请求进行身份验证，所有未经身份验证的请求将重定向到 Azure Active Directory 以进行身份验证。

    > [!NOTE]
    > 以这种方式限制访问权限适用于对应用的所有调用, 对于需要公开提供的主页的应用, 与在许多单页应用程序中一样。 对于此类应用程序, 可以首选 "**允许匿名请求 (无操作)** ", 应用手动启动登录, 如[此处](overview-authentication-authorization.md#authentication-flow)所述。
6. 单击“保存”。

## <a name="advanced"> </a>使用高级设置进行配置

如果要使用的 Azure Active Directory 租户不同于登录 Azure 所用的租户, 还可以手动提供配置设置。 要完成配置，必须先在 Azure Active Directory 中创建注册，然后向应用服务提供一些注册详细信息。

### <a name="register"></a>在应用服务应用的 Azure AD 中创建应用注册

手动创建应用注册时, 请注意以下三条信息, 你稍后将在配置应用服务应用时需要这些信息: 客户端 ID、租户 ID, 还可以选择客户端密钥和应用程序 ID URI。

1. 在[Azure 门户]中, 导航到应用服务应用, 并记下应用的**URL**。 你将使用它来配置 Azure Active Directory 应用注册。
1. 在 " [Azure 门户]中, 从左侧菜单中选择" **Active Directory** > "**应用注册** > **新注册**"。 
1. 在 "**注册应用程序**" 页中, 输入应用注册的**名称**。
1. 在 "**重定向 URI**" 中, 选择 " **Web** ", 键入应用服务应用的 URL `/.auth/login/aad/callback`并追加路径。 例如， `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 然后选择“创建”。
1. 创建应用注册后, 请复制**应用程序 (客户端) id**和**目录 (租户) id**供以后查看。
1. 选择 "**品牌**"。 在 "**主页 url**" 中, 键入应用服务应用的 URL, 然后选择 "**保存**"。
1. 选择 "**公开 API** > **集**"。 粘贴到应用服务应用的 URL, 然后选择 "**保存**"。

    > [!NOTE]
    > 此值是应用注册的**应用程序 ID URI** 。 例如, 如果你想要使用前端 web 应用访问后端 API, 并且希望后端显式授予对前端的访问权限, 则在配置的应用服务应用资源时, 你需要*前端*的**应用程序 ID URI**> 后端。
1. 选择“添加范围”。 在 "**范围名称**" 中, 键入*user_impersonation*。 在文本框中, 键入希望用户在同意页上看到的许可范围名称和说明, 如 "*访问我的应用*"。 完成后, 单击 "**添加作用域**"。
1. 可有可无若要创建客户端密钥, 请选择 "**证书" &**  > "**新的客户端机密** > **添加**"。 复制页面中显示的 "客户端机密" 值。 离开后, 不会再次显示。
1. 可有可无若要添加多个**回复 url**, 请在菜单中选择 "**身份验证**"。

### <a name="secrets"> </a>将 Azure Active Directory 信息添加到应用服务应用

1. 在 [Azure 门户]中，导航到应用服务应用。 从左侧菜单中选择 "**身份验证/授权**"。 如果未启用 "身份验证/授权" 功能, 请选择 "**打开**"。 
1. 可有可无默认情况下, 应用服务身份验证允许未经身份验证的应用访问。 若要强制执行用户身份验证, 请**在请求未经过身份验证时**, 将操作设置为**使用 Azure Active Directory 进行登录**。
1. 在 "身份验证提供程序" 下, 选择**Azure Active Directory**。
1. 在 "**管理模式**" 中, 选择 "**高级**", 并根据下表配置应用服务身份验证:

    |字段|描述|
    |-|-|
    |客户端 ID| 使用应用注册的**应用程序 (客户端) ID** 。 |
    |颁发者 ID| 使用`https://login.microsoftonline.com/<tenant-id>`, 并将 *\<租户 id >* 替换为应用注册的**目录 (租户) id** 。 |
    |客户端密码 (可选)| 使用在应用注册中生成的客户端密码。|
    |允许的标记访问群体| 如果这是*后端*应用, 并且你想要允许来自前端应用的身份验证令牌, 请在此处添加*前端*的**应用程序 ID URI** 。 |
1. 选择 **"确定"** , 然后选择 "**保存**"。

现在，可以使用 Azure Active Directory 在应用服务应用中进行身份验证。

## <a name="configure-a-native-client-application"></a>配置本机客户端应用程序
如果希望使用客户端库 (如**Active Directory 身份验证库**) 执行登录, 则可以注册本机客户端。

1. 在 " [Azure 门户]中, 从左侧菜单中选择" **Active Directory** > "**应用注册** > **新注册**"。 
1. 在 "**注册应用程序**" 页中, 输入应用注册的**名称**。
1. 在 "**重定向 URI**" 中, 选择 "**公用客户端 (移动 & 桌面)** ", 键入应用服务应用的`/.auth/login/aad/callback`URL 并追加路径。 例如， `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 然后选择“创建”。

    > [!NOTE]
    > 对于 Windows 应用程序, 请改用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作为 URI。
1. 创建应用注册后, 复制 "**应用程序 (客户端) ID**" 的值。
1. 从左侧菜单中, 选择 " **api 权限** >  **" "** **添加对 api 的权限** > "。
1. 选择以前为应用服务应用创建的应用注册。 如果看不到 "应用注册", 请检查是否已在 "[为应用服务应用 Azure AD 创建应用注册中](#register)添加了**user_impersonation**作用域。
1. 选择**user_impersonation**并单击 "**添加权限**"。

现已配置可以访问应用服务应用的本机客户端应用程序。

## <a name="related-content"> </a>相关内容

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
