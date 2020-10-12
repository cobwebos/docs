---
title: 注册调用 Web API 的移动应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的移动应用（应用的注册）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: e0f1cc446b6b957b17153dd538922d2b9acd891f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442728"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>注册调用 Web API 的移动应用

可以借助本文中的说明注册你正在创建的移动应用程序。

## <a name="supported-account-types"></a>支持的帐户类型

移动应用程序支持的帐户类型取决于要启用的体验，以及要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

大多数移动应用程序使用交互式身份验证。 如果应用使用这种形式的身份验证，则你可以从任何[帐户类型](quickstart-register-app.md)将用户登录。

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Windows 集成身份验证、用户名-密码以及 B2C 的受众

如果你有通用 Windows 平台 (UWP) 应用，则可以使用 Windows 集成身份验证将用户登录。 若要使用 Windows 集成身份验证或用户名-密码身份验证，应用程序需在你自己的业务线 (LOB) 开发人员租户中将用户登录。 在独立软件供应商 (ISV) 方案中，应用程序可以在 Azure Active Directory 组织中将用户登录。 Microsoft 个人帐户不支持这些身份验证流。

还可以使用传递 B2C 颁发机构和策略的社交标识将用户登录。 若要使用此方法，只能使用交互式身份验证和用户名-密码身份验证。 用户名-密码身份验证目前仅在 Xamarin.iOS、Xamarin.Android 和 UWP 上受支持。

有关详细信息，请参阅[方案和支持的身份验证流](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)以及[方案和支持的平台与语言](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>平台配置和重定向 URI

### <a name="interactive-authentication"></a>交互式身份验证

构建使用交互式身份验证的移动应用时，最关键的注册步骤是重定向 URI。 可以通过[“身份验证”边栏选项卡上的平台配置](https://aka.ms/MobileAppReg)来设置交互式身份验证。

应用可以在此体验中通过 Microsoft Authenticator（以及 Android 上的 Intune 公司门户）实现单一登录 (SSO)。 它还支持设备管理策略。

应用注册门户提供一个预览版体验来帮助你计算 iOS 和 Android 应用程序的中介回复 URI：

1. 在应用注册门户中选择“身份验证” > “尝试新体验”。

   ![“身份验证”边栏选项卡，可在其中选择新体验](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 选择“添加平台”。 

   ![添加平台](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 如果支持平台列表，请选择“iOS”。 

   ![选择移动应用程序](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 输入捆绑 ID，然后选择“注册”。 

   ![输入捆绑 ID](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

完成这些步骤后，系统将会计算重定向 URI，如下图所示。

![生成的重定向 URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

如果你偏向于手动配置重定向 URI，可以通过应用程序清单进行配置。 下面是清单的建议格式：

- **iOS**：`msauth.<BUNDLE_ID>://auth`
  - 例如，输入 `msauth.com.yourcompany.appName://auth`
- **Android**：`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - 可以通过 KeyTool 命令使用发布密钥或调试密钥来生成 Android 签名哈希。

### <a name="username-password-authentication"></a>用户名-密码身份验证

如果应用仅使用用户名-密码身份验证，则无需为应用程序注册重定向 URI。 此流将往返访问 Microsoft 标识平台版本 2.0 终结点。 不会在任何特定 URI 上调用你的应用程序。

但是，需要将应用程序标识为公共客户端应用程序。 为此，请从应用程序的“身份验证”部分开始。  在“高级设置”子部分的“默认客户端类型”段落中，对于“将应用程序视为公共客户端”问题，请选择“是”。    

## <a name="api-permissions"></a>API 权限

移动应用程序代表已登录用户调用 API。 应用需要请求委托的权限。 这些权限也称为范围。 根据所需的体验，可以通过 Azure 门户以静态方式请求委托的权限。 或者，可以在运行时动态请求这些权限。

以静态方式注册权限可让管理员轻松审批应用。 建议使用静态注册。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [代码配置](scenario-mobile-app-configuration.md)
