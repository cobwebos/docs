---
title: 注册调用 web Api 的移动应用 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何生成调用 web Api 的移动应用（应用的代码配置）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0684b8002dad8e0cb29bb1b7afb6ac5a2bd4d685
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423776"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>用于调用 web Api 的移动应用-应用注册

本文包含用于创建移动应用程序的应用注册说明。

## <a name="supported-accounts-types"></a>支持的帐户类型

移动应用程序中支持的帐户类型取决于你想要启用的体验，以及你想要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

大多数移动应用程序使用交互式身份验证。 如果是这种情况，可以从任何[帐户类型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登录用户

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>集成身份验证、用户名/密码和 B2C 的受众

- 如果你打算使用集成的 Windows 身份验证（可能在 UWP 应用中）或用户名/密码，你的应用程序需要在你自己的租户（LOB 开发人员）或 Azure Active directory 组织（ISV 方案）中登录用户。 Microsoft 个人帐户不支持这些身份验证流
- 如果你使用社交标识传递 B2C 机构和策略的用户登录，则只能使用交互式和用户名密码身份验证。 用户名-当前仅支持 Xamarin、Xamarin 和 UWP 上的密码。

有关大图，请参阅[方案和支持的身份验证流](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)和[方案以及支持的平台和语言](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>平台配置和重定向 Uri  

### <a name="interactive-authentication"></a>交互式身份验证

使用交互式身份验证构建移动应用程序时，最关键的注册步骤是重定向 URI。 可以通过["身份验证" 边栏选项卡中的平台配置](https://aka.ms/MobileAppReg)来设置此项。

这种体验使你的应用能够通过 Microsoft Authenticator （和 Android 上的 Intune 公司门户）或支持设备管理策略来获取单一登录（SSO）。

请注意，在应用注册门户中有一个预览体验，可帮助你计算 iOS 和 Android 应用程序的中转回复 URI：

1. 在 "应用注册" 中，选择 "**身份验证**和选择"
   ![映像**尝试新体验**](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 选择 "**添加平台**
   ![映像"](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 支持平台列表时，选择 " **iOS**
   ![映像"](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 按要求输入捆绑 ID，然后按 "**注册**
   ![映像"](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. 将为你计算重定向 URI。
   ![图像](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

如果希望手动配置重定向 URI，可以通过应用程序清单执行此操作。 建议的格式如下所示：

- ***iOS***： `msauth.<BUNDLE_ID>://auth` （例如 "msauth： yourcompany：//auth"）
- ***Android***： `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - 可以通过 KeyTool 命令使用发布或调试密钥生成 Android 签名哈希。

### <a name="username-password"></a>用户名密码

如果应用只使用用户名/密码，则不需要为应用程序注册重定向 URI。 事实上，此流程会执行到 Microsoft 标识平台 v2.0 终结点的往返过程，并且不会对任何特定的 URI 调用你的应用程序。 但是，您需要表达您的应用程序是一个公共客户端应用程序。 此配置是通过转到应用程序的 "**身份验证**" 部分来实现的，在 "**高级设置**" 子节中，选择 **"是**"，将**应用程序视为公共客户端**（在**默认客户端类型**段落中）

## <a name="api-permissions"></a>API 权限

移动应用程序代表已登录用户调用 Api。 应用需要请求委托的权限，也称为作用域。 根据所需的体验，可以通过 Azure 门户以静态方式完成此操作，也可以在运行时动态完成。 静态注册权限允许管理员轻松批准你的应用程序，建议使用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [代码配置](scenario-mobile-app-configuration.md)
