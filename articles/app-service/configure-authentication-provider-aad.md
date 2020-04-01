---
title: 配置 Azure AD 身份验证
description: 了解如何将 Azure 活动目录身份验证配置为应用服务或 Azure 功能应用的标识提供程序。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 4b42f0966288e4ee72b689ddce6313a41e91f13e
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438037"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>将应用服务或 Azure 功能应用配置为使用 Azure AD 登录名

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何配置 Azure 应用服务或 Azure 函数，以便使用 Azure 活动目录 （Azure AD） 作为身份验证提供程序。

> [!NOTE]
> 此时，Azure 应用服务和 Azure 函数不支持[Azure 活动目录 v2.0（](../active-directory/develop/v2-overview.md)包括[MSAL）。](../active-directory/develop/msal-overview.md) 请回头查看是否有更新。
>

设置应用和身份验证时，请遵循以下最佳做法：

- 为每个应用服务应用提供其自身的权限和许可。
- 为每个应用服务应用配置其自身的注册。
- 避免通过对不同的部署槽使用不同的应用注册，在环境之间共享权限。 测试新代码时，这种做法有助于防止问题影响到生产应用。

## <a name="configure-with-express-settings"></a><a name="express"> </a>使用快速设置进行配置

> [!NOTE]
> **"快速"** 选项不适用于政府云。 

1. 在 [Azure 门户]中搜索并选择“应用服务”****，然后选择应用。
2. 从左侧导航中，选择 **"身份验证/授权** > **"。**
3. 选择**Azure 活动目录** > **快速 。**

   若要改为选择现有的应用注册：

   1. 选择“选择现有 AD 应用”，然后单击“Azure AD 应用”。********
   2. 选择现有的应用注册，然后单击“确定”。****

3. 选择“确定”，在 Azure Active Directory 中注册应用服务应用****。 随即会创建一个新的应用注册。
   
    ![Azure Active Directory 中的快速设置](./media/configure-authentication-provider-aad/express-settings.png)
   
4. （可选）默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的授权访问。 必须在应用代码中为用户授权。 若只允许经 Azure Active Directory 身份验证的用户访问应用，请将“请求未经身份验证时需执行的操作”**** 设置为“使用 Azure Active Directory 登录”****。 设置此功能时，应用会要求对所有请求进行身份验证。 它还将所有未经身份验证的用户重定向到 Azure Active Directory 进行身份验证。

    > [!CAUTION]
    > 以这种方式限制访问适用于对应用的所有调用，对于主页公开可用的应用程序来说，这可能是不可取的，就像在许多单页应用程序中一样。 对于此类应用程序，“允许匿名请求(无操作)”**** 可能是首选，应用本身手动启动登录。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。
5. 选择“保存”。 

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>使用高级设置进行配置

若要使用另一 Azure AD 租户中的应用注册，可以手动配置应用设置。 若要完成此自定义配置，请执行以下操作：

1. 在 Azure AD 中创建一个注册。
2. 向应用服务提供一些注册详细信息。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>在 Azure AD 中为应用服务应用创建应用注册

配置应用服务应用时，需要提供以下信息：

- 客户端 ID
- 租户 ID
- 客户端机密（可选）
- 应用程序 ID URI

执行以下步骤：

1. 登录到 [Azure 门户]，搜索并选择“应用服务”****，然后选择应用。 记下应用的“URL”。**** 稍后要使用此 URL 来配置 Azure Active Directory 应用注册。
1. 选择**Azure 活动目录** > **应用注册** > **新注册**。
1. 在“注册应用程序”页中，输入应用注册的**名称**。****
1. 在“重定向 URI”**** 中，选择“Web”**** 并键入 `<app-url>/.auth/login/aad/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 
1. 选择“创建”  。
1. 创建应用注册后，复制“应用程序(客户端) ID”和“目录(租户) ID”供稍后使用。********
1. 选择“身份验证”****。 在**隐式授予**下，启用**ID 令牌**以允许 App 服务中的 OpenID 连接用户登录。
1. （可选）选择**品牌**。 在“主页 URL”中，输入应用服务应用的 URL，然后选择“保存”。********
1. 选择 **"公开 API** > **集**"。 粘贴应用服务应用的 URL，然后选择“保存”。****

   > [!NOTE]
   > 此值是应用注册的“应用程序 ID URI”。**** 如果 Web 应用需要访问云中的 API，则在配置云应用服务资源时，需要提供该 Web 应用的“应用程序 ID URI”。**** 例如，如果你希望云服务显式向该 Web 应用授予访问权限，则可以使用此 URI。

1. 选择 **"添加范围**"。
   1. 在“范围名称”中输入 *user_impersonation*。****
   1. 在文本框中，输入许可范围名称，以及希望在许可页上向用户显示的说明。 例如，输入“访问我的应用”。** 
   1. 选择 **"添加范围**"。
1. （可选）要创建客户端机密，请选择**证书&机密** > **新客户端机密** > **添加**。 复制页面中显示的客户端机密值。 它不会再次显示。
1. （可选）若要添加多个“回复 URL”，请选择“身份验证”。********

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>在应用服务应用中启用 Azure Active Directory

1. 在 [Azure 门户]中搜索并选择“应用服务”****，然后选择应用。 
1. 在左窗格中的“设置”下，选择“身份验证/授权” > “启用”。************
1. （可选）默认情况下，应用服务身份验证允许在未经过身份验证的情况下访问应用。 若要强制用户身份验证，请将“请求未经身份验证时需执行的操作”设置为“使用 Azure Active Directory 登录”********。
1. 在“身份验证提供程序”**** 下，选择“Azure Active Directory”****。
1. 在“管理模式”中，选择“高级”并根据下表配置应用服务身份验证：********

    |字段|说明|
    |-|-|
    |客户端 ID| 使用应用注册的“应用程序(客户端) ID”。**** |
    |颁发者 Url| 使用`https://login.microsoftonline.com/<tenant-id>`，并将*\<租户 id>* 替换为应用注册的**目录（租户）ID。** 此值用于将用户重定向到正确的 Azure AD 租户，以及下载相应的元数据以确定适当的令牌签名密钥和令牌颁发者声明值。 |
    |客户端机密（可选）| 使用在应用注册中生成的客户端机密。|
    |允许的令牌受众| 如果这是一个云应用或服务器应用，而你希望允许来自 Web 应用的身份验证令牌，请在此处添加该 Web 应用的“应用程序 ID URI”。**** 系统始终会将配置的“客户端 ID”隐式视为允许的受众。****** |

2. 选择“确定”，然后选择“保存”********。

现在，可以使用 Azure Active Directory 在应用服务应用中进行身份验证。

## <a name="configure-a-native-client-application"></a>配置本机客户端应用程序

您可以注册本机客户端，以允许使用客户端库（如**活动目录身份验证库**）对应用中托管的 Web API 进行身份验证。

1. 在 [Azure 门户]中，选择“Active Directory”**** > “应用注册”**** > “新建注册”****。
1. 在“注册应用程序”页中，输入应用注册的**名称**。****
1. 在“重定向 URI”中选择“公共客户端(移动和桌面)”，然后键入 URL `<app-url>/.auth/login/aad/callback`。******** 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback` 。

    > [!NOTE]
    > 对于 Microsoft 应用商店应用程序，请使用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作为 URI。
1. 选择“创建”  。
1. 创建应用注册后，复制“应用程序(客户端) ID”的值。****
1. 选择**API 权限** > **添加** > **权限我的 API**。
1. 选择前面为应用服务应用创建的应用注册。 如果未看到该应用注册，请确保在[在 Azure AD 中为应用服务应用创建应用注册](#register)部分已添加 **user_impersonation** 范围。
1. 依次选择“user_impersonation”、“添加权限”。********

您现在配置了一个本机客户端应用程序，该应用程序可以代表用户访问应用服务应用。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure 门户]: https://portal.azure.com/
