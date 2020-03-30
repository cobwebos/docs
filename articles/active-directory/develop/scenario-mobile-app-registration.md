---
title: 注册调用 Web API 的移动应用 |蔚蓝
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
ms.openlocfilehash: 75cfd304869bfb63131dfd2afed9f925c86d32fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132439"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>注册调用 Web API 的移动应用

本文包含帮助您注册要创建的移动应用程序的说明。

## <a name="supported-account-types"></a>支持的帐户类型

移动应用程序支持的帐户类型取决于要启用的体验和要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

大多数移动应用程序使用交互式身份验证。 如果你的应用使用此形式的身份验证，则可以从任何[帐户类型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登录用户。

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>集成 Windows 身份验证、用户名和密码和 B2C 的受众

如果您有通用 Windows 平台 （UWP） 应用，则可以使用集成 Windows 身份验证登录用户。 要使用集成 Windows 身份验证或用户名和密码身份验证，应用程序需要在您自己的业务线 （LOB） 开发人员租户中登录用户。 在独立的软件供应商 （ISV） 方案中，应用程序可以在 Azure 活动目录组织中登录用户。 Microsoft 个人帐户不支持这些身份验证流。

您还可以使用传递 B2C 权限和策略的社交标识登录用户。 要使用此方法，只能使用交互式身份验证和用户名和密码身份验证。 目前仅支持 Xamarin.iOS、Xamarin.Android 和 UWP 上的用户名和密码身份验证。

有关详细信息，请参阅[方案和支持的身份验证流](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)和[方案以及受支持的平台和语言](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)。

## <a name="platform-configuration-and-redirect-uris"></a>平台配置和重定向 URI  

### <a name="interactive-authentication"></a>交互式身份验证

当您构建使用交互式身份验证的移动应用时，最关键的注册步骤是重定向 URI。 您可以通过[**身份验证**边栏选项卡上的平台配置](https://aka.ms/MobileAppReg)设置交互式身份验证。

此体验将使你的应用能够通过 Microsoft 身份验证器 （和 Android 上的 Intune 公司门户） 获得单一登录 （SSO）。 它还将支持设备管理策略。

应用注册门户提供预览体验，以帮助您计算 iOS 和 Android 应用程序的中转回复 URI：

1. 在应用注册门户中，选择 **"身份验证** > **试用"新体验**。

   ![身份验证边栏选项卡，您可以在其中选择新体验](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 选择“添加平台”。****

   ![添加平台](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 如果支持平台列表，请选择“iOS”。****

   ![选择移动应用程序](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 输入捆绑包 ID，然后选择 **"注册**"。

   ![输入捆绑包 ID](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

完成这些步骤后，将为您计算重定向 URI，如下图所示。

![生成的重定向 URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

如果您希望手动配置重定向 URI，可以通过应用程序清单执行此操作。 以下是清单的建议格式：

- **iOS**：`msauth.<BUNDLE_ID>://auth` 
  - 例如，输入 `msauth.com.yourcompany.appName://auth`
- **安卓**系统 ：`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - 您可以通过 KeyTool 命令使用释放密钥或调试密钥生成 Android 签名哈希。

### <a name="username-password-authentication"></a>用户名密码身份验证

如果应用仅使用用户名和密码身份验证，则无需为应用程序注册重定向 URI。 此流对 Microsoft 标识平台版本 2.0 终结点进行往返访问。 不会在任何特定 URI 上调用你的应用程序。 

但是，您需要将应用程序标识为公共客户端应用程序。 为此，请从应用程序的**身份验证**部分开始。 在 **"高级设置**"子节中，在 **"默认客户端类型"** 段落中，对于问题 **，将应用程序视为公共客户端**，请选择"**是**"。

## <a name="api-permissions"></a>API 权限

移动应用程序代表已登录用户调用 API。 你的应用需要请求委派的权限。 这些权限也称为作用域。 根据所需的体验，可以通过 Azure 门户静态请求委派的权限。 或者，您可以在运行时动态请求它们。 

通过静态注册权限，允许管理员轻松批准你的应用。 建议进行静态注册。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [代码配置](scenario-mobile-app-configuration.md)
