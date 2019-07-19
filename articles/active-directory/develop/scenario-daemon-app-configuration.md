---
title: 调用 Web API 的守护程序应用（应用配置）- Microsoft 标识平台
description: 了解如何构建调用 Web API 的守护程序应用（应用配置）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277821"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>调用 Web API 的守护程序应用 - 代码配置

了解如何为调用 Web API 的守护程序应用程序配置代码。

## <a name="msal-libraries-supporting-daemon-apps"></a>支持守护程序应用的 MSAL 库

支持守护程序应用的 Microsoft 库包括：

  MSAL 库 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支持用于构建守护程序应用程序的平台为 .NET Framework 和 .NET Core 平台（不包括 UWP、Xamarin.iOS 和 Xamarin.Android，因为这些平台用于构建公共客户端应用程序）
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 开发中 -目前为公共预览版
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 正在进行开发 - 采用公共预览版

## <a name="configuration-of-the-authority"></a>配置颁发机构

考虑到后台应用程序不使用委派的权限, 但应用程序权限, 其*支持的帐户类型*不能是*任何组织目录和个人 Microsoft 帐户中的帐户 (例如, Skype、Xbox、Outlook.com)* 。 事实上, 没有任何租户管理员向适用于 Microsoft 个人帐户的后台应用程序授予许可。 你需要选择组织中的*帐户*或*任何组织中的帐户*。

因此, 应用程序配置中指定的颁发机构应为 "租户" (指定租户 ID 或与组织关联的域名)。

如果你是 ISV 并想要提供多租户工具, 则可以使用`organizations`。 但请记住, 你还需要向客户说明如何授予管理员同意。 有关详细信息，请参阅[请求整个租户的许可](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。 目前, MSAL 中目前有一个限制, `organizations`仅当客户端凭据是应用程序机密 (而不是证书) 时才允许使用。 请参阅[MSAL.NET bug #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>应用程序配置和实例化

在 MSAL 库中，客户端凭据（机密或证书）是作为机密客户端应用程序构造的参数传递的。

> [!IMPORTANT]
> 即使应用程序是作为服务运行的控制台应用程序，如果它是守护程序应用程序，则也需要是机密客户端应用程序。

### <a name="msalnet"></a>MSAL.NET

向应用程序添加 [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet 包。

使用 MSAL.NET 命名空间

```CSharp
using Microsoft.Identity.Client;
```

此守护程序应用程序将由 `IConfidentialClientApplication` 呈现

```CSharp
IConfidentialClientApplication app;
```

下面的代码演示如何使用应用程序机密来生成应用程序：

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

下面的代码演示如何使用证书来生成应用程序：

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

最后, 机密客户端应用程序还可以使用客户端断言来证明其身份, 而不是客户端机密或证书。 [客户端断言](msal-net-client-assertions.md)详细介绍了这一高级方案


### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [守护程序应用 - 获取应用的令牌](./scenario-daemon-acquire-token.md)
