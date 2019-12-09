---
title: 客户端应用程序配置（MSAL） |Microsoft
titleSuffix: Microsoft identity platform
description: 使用 Microsoft 身份验证库（MSAL）了解公用客户端应用程序和机密客户端应用程序的配置选项。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fd70757fe5ce8761fd3444f3f0dd31e2bfcc3b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917057"
---
# <a name="application-configuration-options"></a>应用程序配置选项

在代码中，你可以初始化新的公共或机密客户端应用程序（或 MSAL 的用户代理），以进行身份验证和获取令牌。 在 Microsoft 身份验证库（MSAL）中初始化客户端应用时，可以设置多个配置选项。 这些选项分为两组：

- 注册选项，包括：
    - [颁发机构](#authority)（由标识提供者[实例](#cloud-instance)和应用的登录[受众](#application-audience)，还可能是租户 ID）。
    - [客户端 ID](#client-id)。
    - [重定向 URI](#redirect-uri)。
    - [客户端密码](#client-secret)（适用于机密客户端应用程序）。
- [日志记录选项](#logging)，包括日志级别、个人数据的控制以及使用库的组件名称。

## <a name="authority"></a>颁发机构

颁发机构是一个 URL，用于指示 MSAL 可以从其请求令牌的目录。 常见权限包括：

- https\://login.microsoftonline.com/\<租户\>/，其中 &lt;租户&gt; 是 Azure Active Directory （Azure AD）租户的租户 ID 或与此 Azure AD 租户关联的域。 仅用于登录特定组织的用户。
- https\://login.microsoftonline.com/common/。 用于使用工作、学校帐户或个人 Microsoft 帐户登录用户。
- https\://login.microsoftonline.com/organizations/。 用于使用工作和学校帐户登录用户。
- https\://login.microsoftonline.com/consumers/。 用于仅使用个人 Microsoft 帐户（以前称为 Windows Live ID 帐户）登录用户。

颁发机构设置需要与应用程序注册门户中声明的设置一致。

颁发机构 URL 由实例和受众组成。

此颁发机构可以：
- Azure AD 云机构。
- Azure AD B2C 机构。 请参阅[B2C 细节](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)。
- Active Directory 联合身份验证服务（AD FS）机构。 请参阅[AD FS 支持](https://aka.ms/msal-net-adfs-support)。

Azure AD 云机构有两部分：
- 标识提供程序*实例*
- 应用的登录*受众*

实例和受众可以连接起来并作为颁发机构 URL 提供。 MSAL.NET 早于 MSAL 3 的版本。*x*，你必须自己基于你要面向的云和登录受众来编写权限。  此图显示了如何编写证书颁发机构 URL：

![如何编写证书颁发机构 URL](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>云实例

*实例*用于指定你的应用程序是从 Azure 公有云还是从国家云对用户进行签名。 在代码中使用 MSAL，可以通过使用枚举或将[国家/地区云实例](authentication-national-cloud.md#azure-ad-authentication-endpoints)的 URL 作为 `Instance` 成员（如果你知道）进行传递来设置 Azure 云实例。

如果同时指定 `Instance` 和 `AzureCloudInstance`，则 MSAL.NET 将引发显式异常。

如果未指定实例，你的应用将面向 Azure 公有云实例（URL `https://login.onmicrosoftonline.com`的实例）。

## <a name="application-audience"></a>应用程序受众

登录受众依赖于您的应用程序的业务需求：
- 如果你是业务线（LOB）开发人员，则可能会生成仅在你的组织中使用的单租户应用程序。 在这种情况下，需要通过其租户 ID （Azure AD 实例的 ID）或与 Azure AD 实例相关联的域名来指定组织。
- 如果你是 ISV，你可能希望在任何组织或某些组织（多租户应用）中使用他们的工作和学校帐户来登录用户。 但你可能还希望让用户使用他们的个人 Microsoft 帐户登录。

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>如何在代码/配置中指定受众

在代码中使用 MSAL，可以使用以下值之一指定受众：
- Azure AD 机构受众枚举
- 租户 ID，可以是：
  - 单租户应用程序的 GUID （Azure AD 实例的 ID）
  - 与 Azure AD 实例关联的域名（也适用于单租户应用程序）
- 其中一个占位符作为租户 ID，用于代替 Azure AD 机构受众枚举：
    - 多租户应用程序的 `organizations`
    - 仅限用户通过个人帐户登录用户的 `consumers`
    - `common` 使用他们的工作和学校帐户或个人 Microsoft 帐户登录用户

如果同时指定 Azure AD 颁发者和租户 ID，MSAL 将引发有意义的异常。

如果你没有指定受众，你的应用将以用户身份 Azure AD 和个人 Microsoft 帐户为目标。 （也就是说，它的行为方式与指定 `common` 相同。）

### <a name="effective-audience"></a>有效受众

你的应用程序的有效受众将是你在应用程序中设置的受众的最小值（如果存在交集）和应用注册中指定的受众。 事实上，[应用注册](https://aka.ms/appregistrations)体验允许您为应用指定受众（支持的帐户类型）。 有关详细信息，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。

目前，仅使用个人 Microsoft 帐户让应用登录用户的唯一方法是配置这两个设置：
- 将应用注册受众设置为 `Work and school accounts and personal accounts`。
- 将您的代码/配置中的受众设置为 `AadAuthorityAudience.PersonalMicrosoftAccount` （或 `TenantID` = "使用者"）。

## <a name="client-id"></a>客户端 ID

客户端 ID 是在注册应用程序时 Azure AD 分配给应用程序的唯一应用程序（客户端） ID。

## <a name="redirect-uri"></a>重定向 URI

重定向 URI 是标识提供程序将安全令牌返回到的 URI。

### <a name="redirect-uri-for-public-client-apps"></a>公共客户端应用的重定向 URI

如果你是使用 MSAL 的公共客户端应用开发人员：
- 需要在桌面或 UWP 应用程序（MSAL.NET 4.1 +）中使用 `.WithDefaultRedirectUri()`。 此方法会将公共客户端应用程序的 "重定向 uri" 属性设置为公用客户端应用程序的默认建议重定向 uri。 

  平台  | 重定向 URI  
  ---------  | --------------
  桌面应用（.NET FW） | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`的值。 这将使用浏览器启用 SSO，方法是将值设置为需要注册的 WebAuthenticationBroker （）的结果。
  .NET Core | `https://localhost`。 这样，用户就可以使用系统浏览器进行交互式身份验证，因为 .NET Core 目前没有用于嵌入的 web 视图的 UI。

- 如果要构建不支持 broker 的 Xamarin Android 和 iOS 应用程序，则无需添加重定向 URI （重定向 URI 自动设置为适用于 Xamarin Android 和 iOS 的 `msal{ClientId}://auth`

- 需要在[应用注册](https://aka.ms/appregistrations)中配置重定向 URI：

   ![应用注册中的重定向 URI](media/msal-client-application-configuration/redirect-uri.png)

你可以使用 `RedirectUri` 属性（例如，如果你使用的是代理）来重写重定向 URI。 下面是此方案的重定向 Uri 的一些示例：

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $ "msauth。{//Auth ";

有关其他 iOS 详细信息，请参阅[将使用 Microsoft Authenticator 的 ios 应用程序从 ADAL.NET 迁移到 MSAL.NET](msal-net-migration-ios-broker.md) ，并[利用 iOS 上的代理](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)。
有关更多 Android 详细信息，请参阅[android 中的中转身份验证](brokered-auth.md)。

### <a name="redirect-uri-for-confidential-client-apps"></a>机密客户端应用的重定向 URI

对于 web 应用，重定向 URI （或回复 URI）是 Azure AD 用来将令牌发送回应用程序的 URI。 如果机密应用是其中的一个，则此 URI 可以是 web 应用/Web API 的 URL。 需要在应用注册中注册重定向 URI。 部署最初在本地测试的应用时，此注册尤其重要。 然后，需要在应用程序注册门户中添加已部署的应用程序的回复 URL。

对于后台应用程序，无需指定重定向 URI。

## <a name="client-secret"></a>客户端机密

此选项指定机密客户端应用程序的客户端密码。 此机密（应用密码）由应用程序注册门户提供，或在使用 PowerShell AzureAD、PowerShell AzureRM 或 Azure CLI 的应用注册期间提供给 Azure AD。

## <a name="logging"></a>日志记录

其他配置选项启用日志记录和故障排除。 有关如何使用它们的详细信息，请参阅[日志记录](msal-logging.md)一文。

## <a name="next-steps"></a>后续步骤

了解如何[使用 MSAL.NET 实例化客户端应用程序](msal-net-initializing-client-applications.md)。
了解如何[使用 MSAL 实例化客户端应用程序](msal-js-initializing-client-applications.md)。
