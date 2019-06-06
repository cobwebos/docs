---
title: 客户端应用程序配置 （Microsoft 身份验证库） |Azure
description: 了解应用程序中 Microsoft 身份验证库 (MSAL) 公共客户端和机密客户端的配置选项。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430806"
---
# <a name="application-configuration-options"></a>应用程序配置选项

在代码中，初始化新的公共或机密客户端 （或 MSAL.js 的用户代理） 应用程序进行身份验证并获取令牌。 在初始化客户端应用程序中 Microsoft 身份验证库 (MSAL) 时，可以设置许多配置选项。 这些选项划分为两个组：

- 注册选项，包括：
    - [颁发机构](#authority)(标识提供程序的组成[实例](#cloud-instance)和登录[受众](#application-audience)针对应用程序中，或租户 ID)。
    - [客户端 ID](#client-id)。
    - [重定向 URI](#redirect-uri)。
    - [客户端机密](#client-secret)（针对机密客户端应用程序）。
- [日志记录选项](#logging)，包括日志级别、 个人数据的控件和组件使用库的名称。

## <a name="authority"></a>颁发机构
颁发机构是指示 MSAL 可以请求从令牌的目录的 URL。 常见的颁发机构是：

- https://login.microsoftonline.com/&lt租户&gt;/，其中&lt;租户&gt;是 Azure Active Directory (Azure AD) 租户或域与此 Azure AD 租户相关联的租户 ID。 仅用于特定组织的用户登录。
- https://login.microsoftonline.com/common/。 用于在用户的工作和学校帐户或个人 Microsoft 帐户登录。
- https://login.microsoftonline.com/organizations/。 用于在使用用户的工作和学校帐户登录。
- https://login.microsoftonline.com/consumers/。 用于使用仅个人 Microsoft 帐户 （以前称为 Windows Live ID 帐户） 登录用户。

授权机构设置必须与在应用程序注册门户中声明的内容一致。

机构 URL 组成的实例和受众。

颁发机构可以是：
- Azure AD 云颁发机构。
- Azure AD B2C 颁发机构。 请参阅[B2C 细节](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)。
- Active Directory 联合身份验证服务 (ADFS) 颁发机构。 请参阅[ADFS 支持](https://aka.ms/msal-net-adfs-support)。

Azure AD 云颁发机构具有两个部分：
- 标识提供者*实例*
- 单一登录*受众*应用

可以连接在一起并作为颁发机构 URL 提供的实例和受众。 在以前的版本 MSAL.NET MSAL 3。*x*，必须自行编写该颁发机构、 基于云上，你想要面向和登录受众。  此图显示了如何构成的颁发机构 URL:

![如何构成的颁发机构 URL](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>云实例
*实例*用来指定您的应用程序签名从 Azure 公有云或国家/地区云的用户。 在代码中使用 MSAL，可以设置 Azure 云实例使用一个枚举，或传递到 URL[国家/地区云实例](authentication-national-cloud.md#azure-ad-authentication-endpoints)作为`Instance`成员 （如果知道）。

如果这两个，MSAL.NET 将引发的显式异常`Instance`和`AzureCloudInstance`指定。

如果未指定实例，您的应用程序将面向 Azure 公有云实例 (实例 URL `https://login.onmicrosoftonline.com`)。

## <a name="application-audience"></a>应用程序访问群体

在登录受众取决于您的应用程序的业务需求：
- 如果您的业务线 (LOB) 开发人员行，您将能够仅在你的组织中可能生成的单租户应用程序将使用。 在这种情况下，您需要指定该组织，通过其租户 ID (Azure AD 实例的 ID) 或通过与 Azure AD 实例相关联的域名。
- 如果你是 ISV，你可能想要登录用户使用其工作和学校帐户在任何组织中或在某些组织 （多租户应用程序）。 但你可能还想让用户使用其个人 Microsoft 帐户登录。

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>如何在你的代码/配置中指定的受众
使用以下值之一，请在代码中使用 MSAL，指定受众：
- Azure AD 颁发机构受众枚举
- 租户 ID，它可以是：
  - 对于单租户应用程序 GUID (Azure AD 实例的 ID)
  - 与 Azure AD 实例 （也适用于单租户应用程序） 相关联的域名
- 作为租户 ID 来替代 Azure AD 颁发机构受众枚举这些占位符之一：
    - `organizations` 对于多租户应用程序
    - `consumers` 若要仅使用其个人帐户的用户登录
    - `common` 若要在用户与他们的工作和学校帐户，或者其个人 Microsoft 帐户登录

MSAL 将引发有意义的异常，如果指定的 Azure AD 颁发机构受众和租户 id。

如果未指定访问者，应用的目标 Azure AD 和个人 Microsoft 帐户作为受众。 (也就是说，它的行为将如同`common`指定。)

### <a name="effective-audience"></a>有效的受众
你的应用程序的有效受众将最小值 （如果没有交集） 的应用程序中设置的用户和应用注册中指定的受众。 事实上，[应用注册](https://aka.ms/appregistrations)体验可让你指定应用程序的受众 （支持的帐户类型）。 有关详细信息，请参阅[快速入门：注册一个应用程序的 Microsoft 标识平台](quickstart-register-app.md)。

目前，获取用于在用户使用个人 Microsoft 帐户登录的应用的唯一方法是配置这两个设置：
- 将应用注册受众设置为`Work and school accounts and personal accounts`。
- 到你的代码/配置中设置受众`AadAuthorityAudience.PersonalMicrosoftAccount`(或`TenantID`="使用者")。

## <a name="client-id"></a>客户端 ID
客户端 ID 是由 Azure AD 注册应用程序时分配给您的应用程序的唯一的应用程序 （客户端） ID。

## <a name="redirect-uri"></a>重定向 URI
重定向 URI 是标识提供程序将发送的安全令牌返回到的 URI。

### <a name="redirect-uri-for-public-client-apps"></a>公共客户端应用程序的重定向 URI
如果您的公共客户端应用程序开发人员使用 MSAL:
- 不需要传递`RedirectUri`它是由 MSAL 自动计算。 此重定向 URI 设置为以下值，具体取决于平台之一：
   - `urn:ietf:wg:oauth:2.0:oob` 所有 Windows 平台
   - `msal{ClientId}://auth` 适用于 Xamarin Android 和 iOS

- 是否需要配置重定向 URI[应用注册](https://aka.ms/appregistrations):

   ![在应用注册中的重定向 URI](media/msal-client-application-configuration/redirect-uri.png)

您可以通过使用替代的重定向 URI`RedirectUri`属性 （例如，如果使用代理）。 下面是该方案的重定向 Uri 的一些示例：

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

有关详细信息，请参阅[适用于 Android 和 iOS 文档](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)。

### <a name="redirect-uri-for-confidential-client-apps"></a>机密客户端应用程序的重定向 URI
对于 web 应用，重定向 URI （或回复 URI） 是 Azure AD 将用来将令牌发送回应用程序的 URI。 如果机密的应用程序就是其中之一，这可以是 web 应用/Web API 的 URL。 重定向 URI 必须在应用注册中注册。 此注册的应用程序最初本地测试部署时尤其重要。 然后需要在应用程序注册门户中添加已部署的应用的答复 URL。

对于后台程序应用程序，不需要指定重定向 URI。

## <a name="client-secret"></a>客户端机密
此选项指定机密客户端应用程序的客户端密码。 此密钥 （应用程序密码） 为提供的应用程序注册门户或到 Azure AD 应用注册期间提供与 azure Ad PowerShell、 PowerShell AzureRM 或 Azure CLI。

## <a name="logging"></a>日志记录
其他配置选项，启用日志记录和故障排除。 请参阅[日志记录](msal-logging.md)一文，了解如何使用它们的详细信息。

## <a name="next-steps"></a>后续步骤
了解如何[实例化客户端应用程序使用 MSAL.NET](msal-net-initializing-client-applications.md)。

了解如何[实例化客户端应用程序使用 MSAL.js](msal-js-initializing-client-applications.md)。
