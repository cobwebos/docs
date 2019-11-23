---
title: 用于调用 web Api 应用程序代码配置的移动应用
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 Web API 的移动应用（应用的代码配置）
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
ms.openlocfilehash: 5f55e73fa1a73908d7e77bacc6af24ea1a40ba92
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803736"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>调用 Web API 的移动应用 - 应用注册

本文包含用于创建移动应用程序的应用注册说明。

## <a name="supported-accounts-types"></a>支持的帐户类型

在移动应用程序中支持的帐户类型取决于你想要启用的体验，以及要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

大多数移动应用程序使用交互式身份验证。 如果情况是这样，则可通过任何[帐户类型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登录用户

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>集成身份验证、用户名/密码以及 B2C 的受众

- 若要使用集成 Windows 身份验证（可能在 UWP 应用中）或用户名/密码，应用程序需在你自己的租户（LOB 开发人员）或 Azure Active Directory 组织（ISV 方案）中登录用户。 Microsoft 个人帐户不支持这些身份验证流
- 如果使用传入 B2C 颁发机构和策略的社交标识来登录用户，则只能使用交互式和用户-密码身份验证。 用户名-密码目前仅在 Xamarin.iOS、Xamarin.Android 和 UWP 上受支持。

如需进行全面的了解，请参阅[方案和受支持的身份验证流](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)和[方案和受支持的平台和语言](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>平台配置和重定向 URI  

### <a name="interactive-authentication"></a>交互式身份验证

使用交互式身份验证构建移动应用时，最关键的注册步骤是重定向 URI。 可以通过[“身份验证”边栏选项卡中的平台配置](https://aka.ms/MobileAppReg)对此进行设置。

此体验将允许应用通过 Microsoft Authenticator（以及 Android 上的 Intune 公司门户）获取单一登录 (SSO)，以及支持设备管理策略。

请注意，在应用注册门户中有一个预览体验，可以让你计算 iOS 和 Android 应用程序的中转回复 URI：

1. 在应用注册中选择“身份验证”，然后选择“尝试新体验”
   ![图像](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 选择“添加平台”
   ![图像](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 如果支持平台列表，请选择“iOS”
   ![图像](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 依请求输入捆绑 ID，然后按“注册”
   ![图像](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. 此时会为你计算重定向 URI。
   ![image](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

如果首选以手动方式配置重定向 URI，则可通过应用程序清单来这样做。 建议的格式如下：

- ***iOS***：`msauth.<BUNDLE_ID>://auth`（例如，“msauth.com.yourcompany.appName://auth”）
- ***Android***：`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Android 签名哈希可以通过 KeyTool 命令使用释放键或调试键来生成。

### <a name="username-password"></a>用户名/密码

如果应用仅使用用户名/密码，则不需为应用程序注册重定向 URI。 事实上，该流会往返 Microsoft 标识平台 v2.0 终结点，因此不会在任何特定 URI 上回调你的应用程序。 但是，你需要表达应用程序是公共客户端应用程序这一涵义。 实现此配置的方法是：转到应用程序的“身份验证”部分，然后在“高级设置”子部分针对“将应用程序视为公共客户端”问题（位于“默认客户端类型”段落）选择“是”

## <a name="api-permissions"></a>API 权限

移动应用程序代表已登录用户调用 API。 应用需要请求委托的权限（也称为作用域）。 根据所需体验，这可以通过 Azure 门户静态完成，也可以在运行时动态完成。 静态注册权限方便管理员批准应用，值得推荐。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [代码配置](scenario-mobile-app-configuration.md)
