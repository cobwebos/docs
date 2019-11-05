---
title: 在 Azure Active Directory B2C 中使用 iOS 应用程序的 AppAuth | Microsoft Docs
description: 本文说明如何创建一个使用 AppAuth 和 Azure Active Directory B2C 来管理用户标识以及对用户进行身份验证的 iOS 应用。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0fb5341c2e7ee55391cb38251b0ea66b55b93301
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469155"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C：使用 iOS 应用程序登录

Microsoft 标识平台使用开放式标准，例如 OAuth2 和 OpenID Connect。 在选择要与我们的服务集成的库时，使用开放标准协议可让开发人员有更多的选择。 我们提供了本演练和其他类似文档来帮助开发人员编写可以连接到 Microsoft 标识平台的应用程序。 大部分实现 [RFC6749 OAuth2 规范](https://tools.ietf.org/html/rfc6749)的库都可连接到 Microsoft 标识平台。

> [!WARNING]
> Microsoft 不提供第三方库的修复程序，且尚未审查这些库。 本示例使用名为 AppAuth 的第三方库，该库经测试可与 Azure AD B2C 的基本方案兼容。 问题和功能请求应重定向到库的开源项目。 有关详细信息，请参阅[此文章](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)。
>
>

对于 OAuth2 或 OpenID Connect 的新手，该示例配置中的大部分内容可能较难理解。 建议查看 [此处所述的简要协议概述](active-directory-b2c-reference-protocols.md)。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](tutorial-create-tenant.md) ，再继续。

## <a name="create-an-application"></a>创建应用程序

接下来，将应用程序注册到 Azure AD B2C 租户。 这为 Azure AD 提供了与应用安全通信所需的信息。

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

记录**应用程序（客户端） ID** ，以便在后面的步骤中使用。

还应记录自定义重定向 URI，以便在后面的步骤中使用。 例如，`com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`。

## <a name="create-your-user-flows"></a>创建用户流
在 Azure AD B2C 中，每个用户体验由[用户流](active-directory-b2c-reference-policies.md)定义。 此应用程序包含一个标识体验：合并的登录和注册。 创建用户流时，请务必：

* 在“注册属性”下，选择“显示名称”属性。  还可选择其他属性。
* 在“应用程序声明”下，选择“显示名称”和“用户的对象 ID”声明。 也可选择其他声明。
* 创建用户流后，请复制每个用户流的名称。 保存用户流时，用户流名称带有前缀 `b2c_1_`。  稍后需要用户流名称。

创建用户流后，可以开始构建应用。

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
* 用户流名称（例如 B2C\_1\_SignUpIn）

可以通过替换以下 URL 中的 Tenant\_ID 和 Policy\_Name 来生成令牌终结点 URI：

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

可以通过替换以下 URL 中的 Tenant\_ID 和 Policy\_Name 来生成授权终结点 URI：

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

运行以下代码创建 AuthorizationServiceConfiguration 对象：

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>授权

配置或检索授权服务配置后，可以构造授权请求。 若要创建该请求，需要提供以下信息：

* 之前记录的客户端 ID（应用程序 ID）。 例如，`00000000-0000-0000-0000-000000000000`。
* 之前记录的自定义重定向 URI。 例如，`com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`。

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
* 将鼠标悬停在带有“BBundle OS 类型代码”字样的行上，并单击 \+ 符号。
* 将新行重命名为“URL 类型”。
* 单击“URL 类型”左侧的箭头打开该树。
* 单击“项目 0”左侧的箭头打开该树。
* 将项目 0 下面的第一项重命名为“URL 方案”。
* 单击“URL 方案”左侧的箭头打开该树。
* 在“值”列中，“URL 方案”下面“项目 0”左侧具有空白字段。  将值设置为应用程序的唯一方案。  创建 OIDAuthorizationRequest 对象时，该值必须与 redirectURL 中使用的方案匹配。  本示例使用了方案“com.onmicrosoft.fabrikamb2c.exampleapp”。

有关如何完成余下的过程，请参阅 [AppAuth 指南](https://openid.github.io/AppAuth-iOS/)。 如果需要快速开始使用一个正常运行的应用，请查看[示例](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)。 遵循 [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) 中的步骤输入自己的 Azure AD B2C 配置。

我们始终乐于接受反馈和建议！ 如果在学习本文的过程中遇到任何难题，或者在改进此内容方面有任何建议，请在页面底部提供反馈，我们将不胜感激。 对于功能请求，请将其添加到 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)。
