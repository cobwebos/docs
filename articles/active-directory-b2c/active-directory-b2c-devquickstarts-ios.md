---
title: "使用 iOS 应用程序获取令牌 - Azure AD B2C | Microsoft Docs"
description: "本文说明如何创建一个使用 AppAuth 和 Azure Active Directory B2C 来管理用户标识以及对用户进行身份验证的 iOS 应用。"
services: active-directory-b2c
documentationcenter: ios
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objectivec
ms.topic: article
ms.date: 03/07/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: cc26d4d2209564fc5c994c2bc73f6a572fe87d28
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C：使用 iOS 应用程序登录

Microsoft 标识平台使用开放式标准，例如 OAuth2 和 OpenID Connect。 在选择要与我们的服务集成的库时，使用开放标准协议可让开发人员有更多的选择。 我们提供了本演练和其他类似文档来帮助开发人员编写可以连接到 Microsoft 标识平台的应用程序。 大部分实现 [RFC6749 OAuth2 规范](https://tools.ietf.org/html/rfc6749)的库都可连接到 Microsoft 标识平台。

> [!WARNING]
> Microsoft 不提供第三方库的修复程序，且尚未审查这些库。 本示例使用名为 AppAuth 的第三方库，该库经测试可与 Azure AD B2C 的基本方案兼容。 问题和功能请求应重定向到库的开源项目。 有关详细信息，请参阅[此文章](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)。
>
>

对于 OAuth2 或 OpenID Connect 的新手，该示例配置中的大部分内容可能较难理解。 建议查看 [此处所述的简要协议概述](active-directory-b2c-reference-protocols.md)。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，再继续。

## <a name="create-an-application"></a>创建应用程序
接下来，需要在 B2C 目录中创建应用。 应用注册为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 若要创建移动应用，请遵循[这些说明](active-directory-b2c-app-registration.md)。 请务必：

* 在应用程序中包含**本机客户端**。
* 复制分配给应用的 **应用程序 ID** 。 稍后需要用到此 GUID。
* 使用自定义方案（例如 com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect）设置“重定向 URI”。 稍后需要用到此 URI。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略
在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 此应用包含一个标识体验：合并的登录和注册。 根据[策略参考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)中所述创建此策略。 创建策略时，请务必：

* 在“注册属性”下，选择“显示名称”属性。  还可选择其他属性。
* 在“应用程序声明”下，选择“显示名称”和“用户的对象 ID”声明。 也可选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 保存策略时，策略名称带有前缀 `b2c_1_`。  稍后需要用到该策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

创建策略后，可以开始构建应用。

## <a name="download-the-sample-code"></a>下载示例代码
我们[在 GitHub 上](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)提供了有关将 AppAuth 与 Azure AD B2C 配合使用的实践示例。 可以下载该代码并运行它。 若要使用自己的 Azure AD B2C 租户，请遵循 [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) 中的说明。

此示例是在 [GitHub 上的 iOS AppAuth 项目](https://github.com/openid/AppAuth-iOS)中根据 README 说明创建的。 有关示例和库的工作原理的详细信息，请参阅在 GitHub 上的 AppAuth README。

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>修改应用，以便将 AppAuth 与 Azure AD B2C 配合使用

> [!NOTE]
> AppAuth 支持 iOS 7 和更高版本。  但是，若要在 Google 中支持社交登录，则需要安装 SFSafariViewController，这就需要使用 iOS 9 或更高版本。
>

### <a name="configuration"></a>配置

可以通过指定授权终结点和令牌终结点 URI，来配置与 Azure AD B2C 的通信。  若要生成这些 URI，需要提供以下信息：
* 租户 ID（例如 contoso.onmicrosoft.com）
* 策略名称（例如 B2C\_1\_SignUpIn）

可以通过替换以下 URL 中的 Tenant\_ID 和 Policy\_Name 来生成令牌终结点 URI：

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

可以通过替换以下 URL 中的 Tenant\_ID 和 Policy\_Name 来生成授权终结点 URI：

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

运行以下代码创建 AuthorizationServiceConfiguration 对象：

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>授权

配置或检索授权服务配置后，可以构造授权请求。 若要创建该请求，需要提供以下信息：  
* 客户端 ID（例如 00000000-0000-0000-0000-000000000000）
* 使用自定义方案的重定向 URI（例如 com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect）

[注册应用](#create-an-application)时应已保存这两项信息。

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

要将应用程序设置为处理重定向到使用自定义方案的 URI，需要在 Info.pList 中更新“URL 方案”列表：
* 打开 Info.pList。
* 将鼠标悬停在带有“BBundle OS 类型代码”字样的行上，然后单击 \+ 符号。
* 将新行重命名为“URL 类型”。
* 单击“URL 类型”左侧的箭头打开该树。
* 单击“项目 0”左侧的箭头打开该树。
* 将项目 0 下面的第一项重命名为“URL 方案”。
* 单击“URL 方案”左侧的箭头打开该树。
* 在“值”列中，“URL 方案”下面“项目 0”左侧具有空白字段。  将值设置为应用程序的唯一方案。  创建 OIDAuthorizationRequest 对象时，该值必须与 redirectURL 中使用的方案匹配。  本示例使用了方案“com.onmicrosoft.fabrikamb2c.exampleapp”。

有关如何完成余下的过程，请参阅 [AppAuth 指南](https://openid.github.io/AppAuth-iOS/)。 如果需要快速开始创建一个正常运行的应用，请查看[我们的示例](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)。 遵循 [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) 中的步骤输入自己的 Azure AD B2C 配置。

我们始终乐于接受反馈和建议！ 如果在完成本主题的过程中遇到任何难题，或者在改进此内容方面有任何建议，请在页面底部提供反馈，我们将不胜感激。 对于功能请求，请将其添加到 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)。
