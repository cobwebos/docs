---
title: "Azure Active Directory B2C：使用 Android 应用程序获取令牌 | Microsoft 文档"
description: "本文说明如何创建一个使用 AppAuth 和 Azure Active Directory B2C 来管理用户标识以及对用户进行身份验证的 Android 应用。"
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: fdf9f494f30f11a00831b6c56a3d6ac40582c0ad
ms.lasthandoff: 03/14/2017


---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C：使用 Android 应用程序登录

Microsoft 标识平台使用开放式标准，例如 OAuth2 和 OpenID Connect。 这样，开发人员便利用他们想要与服务集成的任何库。 为了帮助开发人员将平台与其他库结合使用，我们编写了多份演练（例如本演练），演示如何配置第三方库，使其连接到 Microsoft 标识平台。 大部分实施 [RFC6749 OAuth2 规范](https://tools.ietf.org/html/rfc6749) 的库都可连接到 Microsoft 标识平台。

> [!WARNING]
> Microsoft 不提供第三方库的修复程序，且尚未审查这些库。 本示例使用名为 AppAuth 的第三方库，该库经测试可与 Azure AD B2C 的基本方案兼容。 问题和功能请求应重定向到库的开源项目。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)。  
>
>

对于 OAuth2 或 OpenID Connect 的新手，该示例配置中的大部分内容可能较难理解。 建议查看 [此处所述的简要协议概述](active-directory-b2c-reference-protocols.md)。

B2C 平台不一定支持所有 Azure Active Directory 方案和功能。  若要确定是否应使用 B2C 平台，请阅读 [B2C limitations](active-directory-b2c-limitations.md)（B2C 限制）。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录

只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后再继续。

## <a name="create-an-application"></a>创建应用程序

接下来，需要在 B2C 目录中创建应用。 此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 若要创建移动应用，请遵循[这些说明](active-directory-b2c-app-registration.md)。 请务必：

* 在应用程序中包含**本机客户端**。
* 复制分配给应用的 **应用程序 ID** 。 稍后你将需要此项。
* 设置本机客户端**重定向 URI**（例如 com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect）。 稍后也需要用到此信息。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略

在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 此应用包含一个标识体验：合并的登录和注册。 需要根据[策略参考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)中所述创建此策略。 创建策略时，请务必：

* 选择“显示名称”作为策略中的注册属性。
* 在每个策略中，选择“显示名称”和“对象 ID”应用程序声明。 也可以选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 其前缀应为 `b2c_1_`。  稍后需要用到策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

创建策略后，可以开始构建应用。

## <a name="download-the-sample-code"></a>下载示例代码

我们[在 GitHub 上](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)提供了有关将 AppAuth 与 Azure AD B2C 配合使用的实践示例。 可以下载该代码并运行它。 可以遵照 [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) 中的说明，使用自己的 Azure AD B2C 配置快速开始操作自己的应用。

该示例是 [AppAuth](https://openid.github.io/AppAuth-Android/) 提供的示例的修改版。 请访问 AppAuth 的页面来了解该产品及其功能。

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>修改应用，以便将 AppAuth 与 Azure AD B2C 配合使用

> [!NOTE]
> AppAuth 支持 Android API 16 (Jellybean) 和更高版本。 建议使用 API 23 和更高版本。
>

### <a name="configuration"></a>配置

可以通过指定发现 URI 或者指定授权终结点和令牌终结点 URI，来配置与 Azure AD B2C 的通信。 在任一情况下，都需要提供以下信息：

* 租户 ID（例如 contoso.onmicrosoft.com）
* 策略名称（例如 B2C\_1\_SignUpIn）

如果选择自动发现授权和令牌终结点 URI，需要从发现 URI 中提取信息。 可以通过替换以下 URL 中的 Tenant\_ID 和 Policy\_Name 来生成发现 URI：

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

然后，可以获取授权和令牌终结点 URI，并运行以下命令来创建 AuthorizationServiceConfiguration 对象：

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

如果不使用发现功能来获取授权和令牌终结点 URI，也可以通过替换以下 URL 中的 Tenant\_ID 和 Policy\_Name 来显式指定这些 URI：

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

运行以下代码创建 AuthorizationServiceConfiguration 对象：

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>授权

配置或检索授权服务配置后，可以构造授权请求。 若要创建该请求，需要提供以下信息：

* 客户端 ID（例如 00000000-0000-0000-0000-000000000000）
* 使用自定义方案的重定向 URI（例如 com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect）

[注册应用](#create-an-application)时应已保存这两项信息。

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

有关如何完成余下的过程，请参阅 [AppAuth 指南](https://openid.github.io/AppAuth-Android/)。 如果需要快速开始创建一个正常运行的应用，请查看[我们的示例](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)。 遵循 [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) 中的步骤输入你自己的 Azure AD B2C 配置。

我们始终乐于接受反馈和建议！ 如果在完成本主题的过程中遇到任何难题，或者在改进此内容方面有任何建议，请在页面底部提供反馈，我们将不胜感激。 对于功能请求，请将其添加到 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)。


