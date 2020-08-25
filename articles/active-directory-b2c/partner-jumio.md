---
title: 通过 Jumio 配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何 Azure Active Directory B2C 配置 Jumio 以进行自动 ID 验证，保护客户数据
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817323"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>有关配置 Jumio 与 Azure Active Directory B2C 的教程

在此示例教程中，我们提供了有关如何将 Azure AD B2C 与 [Jumio](https://www.jumio.com/)集成的指导。 Jumio 是一种 ID 验证服务，用于启用实时自动 ID 验证，从而保护客户数据。

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。 租户已链接到 Azure 订阅。

## <a name="scenario-description"></a>方案描述

Jumio 集成包括以下组件：

- Azure AD B2C –授权服务器，负责验证用户的凭据，也称为标识提供者

- Jumio-采用用户提供的 ID 详细信息并对其进行验证的服务。

- 中间 Rest API –此 API 实现 Azure AD B2C 与 Jumio 服务之间的集成。

- Blob 存储-用于向 Azure AD B2C 策略提供自定义 UI 文件。

下面的体系结构关系图显示了实现。

![Jumio 的屏幕截图](./media/partner-jumio/jumio-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达登录页。 用户选择 "注册" 以创建新帐户，并将信息输入到页面中。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用中间层 API 并传递用户特性。
| 3. | 中间层 API 收集用户属性，并将其转换为 Jumio API 可以使用的格式。 然后，将其发送到 Jumio。
| 4. | 在 Jumio 使用该信息并对其进行处理后，它会将结果返回给中间层 API。
| 5. | 中间层 API 处理信息并将相关信息发送回 Azure AD B2C。
| 6. | Azure AD B2C 接收来自中间层 API 的信息。 如果显示失败响应，则向用户显示一条错误消息。 如果它显示成功响应，则会对用户进行身份验证并将其写入到目录中。

## <a name="onboard-with-jumio"></a>内置 Jumio

1. 若要创建 Jumio 帐户，请联系 [Jumio](https://www.jumio.com/contact/)

2. 创建帐户后，会在 API 配置中使用信息。 以下各节介绍了该过程。

## <a name="configure-azure-ad-b2c-with-jumio"></a>配置 Jumio Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

将提供的 [API 代码](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) 部署到 Azure 服务。 可以按照这些 [说明](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)从 Visual Studio 发布代码。

>[!NOTE]
>需要部署的服务的 URL 来配置 Azure AD，并提供所需的设置。

### <a name="part-2---deploy-the-client-certificate"></a>第2部分-部署客户端证书

1. Jumio API 调用受到客户端证书的保护。 使用以下示例代码创建自签名证书：

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. 然后，该证书将导出到为 {} 指定的位置 ``your-local-path`` 。

3. 按照本 [文档](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate)中所述的说明，将证书导入到 Azure 应用服务。

### <a name="part-3---create-a-signingencryption-key"></a>第3部分-创建签名/加密密钥

创建长度超过64个字符且仅包含字母或数字的随机字符串。

例如：``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

使用以下 PowerShell 脚本创建64字符长度的字母数字值。

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>第4部分-配置 API

可以 [在 Azure 中的应用服务中配置](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)应用程序设置。 使用此方法，可以安全地配置设置，而无需将其签入存储库。 需要为 Rest API 提供以下设置：

| 应用程序设置 | Source | 注释 |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio 帐户配置 |     |
|JumioSettings:AuthPassword | Jumio 帐户配置 |     |
|AppSettings： SigningCertThumbprint|创建的自签名证书的指纹|  |
|AppSettings： IdTokenSigningKey| 使用 PowerShell 创建的签名密钥 | |
| AppSettings： IdTokenEncryptionKey |使用 PowerShell 创建的加密密钥
| AppSettings： IdTokenIssuer | 要用作 JWT 令牌的颁发者 (guid 值为首选)  |
| AppSettings： IdTokenAudience  | 要用作 JWT 令牌的受众 (guid 值为首选)  |
|AppSettings： BaseRedirectUrl | B2C 策略的基 url | https：//{b2clogin}/{你的应用程序 id}|
| WEBSITE_LOAD_CERTIFICATES| 创建的自签名证书的指纹 |

### <a name="part-5---deploy-the-ui"></a>第5部分-部署 UI

1. [在存储帐户中设置 blob 存储容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)。

2. 将 ui 文件从 [ui 文件夹](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) 存储到 blob 容器。

#### <a name="update-ui-files"></a>更新 UI 文件

1. 在 UI 文件中，切换到文件夹 [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. 打开每个 html 文件。

3. 查找并将 {ui-blob-url} 替换为你的 blob 容器 URL。

4. 查找并将 {中间 api url} 替换为中间 API 应用服务的 URL。

>[!NOTE]
> 作为最佳做法，我们建议客户在 "属性集合" 页中添加许可通知。 通知用户信息将发送到第三方服务进行身份验证。

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>第6部分-配置 Azure AD B2C 策略

1. 在 "策略" 文件夹中转到 " [Azure AD B2C" 策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) 。

2. 按照此 [文档](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 下载 [LocalAccounts starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. 为 Azure AD B2C 租户配置策略。

>[!NOTE]
>更新提供的策略，使其与你的特定租户相关。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，并在 "策略" 下选择 " **标识体验框架**"。

2. 选择先前创建的 **SignUpSignIn**。

3. 选择 " **运行用户流** "，然后选择设置：

   a. **应用程序**：选择注册应用 (示例为 JWT) 

   b. **回复 url**：选择 "**重定向 url** "

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户

5. 创建用户属性后，将在流中调用 Jumio 服务。 如果流未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
