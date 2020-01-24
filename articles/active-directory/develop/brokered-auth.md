---
title: Android 中的中转身份验证 |Microsoft
titlesuffix: Microsoft identity platform
description: Microsoft 标识平台中适用于 Android 的中转身份验证 & 授权概述
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697891"
---
# <a name="brokered-authentication-in-android"></a>Android 中的中转身份验证

必须使用 Microsoft 的一个身份验证代理参与设备范围的单一登录（SSO），并满足组织的条件性访问策略。 与 broker 集成具有以下优势：

- 设备单一登录
- 的条件性访问：
  - Intune 应用保护
  - 设备注册（Workplace Join）
  - 移动设备管理
- 设备范围的帐户管理
  -  通过 Android AccountManager & 帐户设置
  - "工作帐户"-自定义帐户类型

在 Android 上，Microsoft Authentication Broker 是包含在[Microsoft Authenticator 应用](https://play.google.com/store/apps/details?id=com.azure.authenticator)和[Intune 公司门户](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)中的组件

> [!TIP]
> 一次仅有一个托管 broker 的应用程序将作为 broker 处于活动状态。 作为 broker 的活动应用程序在设备上由安装顺序确定。 要安装的第一个设备或设备上的最后一个将成为活动 broker。

下图说明了你的应用程序、Microsoft 身份验证库（MSAL）和 Microsoft 的身份验证代理之间的关系。

![Broker 部署关系图](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>安装托管代理的应用程序

代理托管的应用可由设备所有者从其应用商店（通常是 Google Play 商店）进行安装。 但是，某些 Api （资源）受要求设备的条件访问策略的保护：

- 已注册（已加入工作区）和/或
- 已在设备管理中注册或
- 已在 Intune 应用保护中注册

如果设备还没有安装代理应用，则 MSAL 会指示用户在应用尝试以交互方式获取令牌时立即安装一个。 然后，应用将需要引导用户完成相关步骤，使设备符合所需的策略。

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>安装和卸载代理的效果

### <a name="when-a-broker-is-installed"></a>安装 broker 时

在设备上安装 broker 后，代理将处理所有后续的交互式令牌请求（对 `acquireToken()`的调用），而不是由 MSAL 在本地处理。 任何以前可供 MSAL 使用的 SSO 状态对代理不可用。 因此，用户将需要重新进行身份验证，或者从设备已知的现有帐户列表中选择一个帐户。

安装代理不需要用户重新登录。 仅当用户需要解析 `MsalUiRequiredException` 时，下一个请求才会发送到 broker。 引发 `MsalUiRequiredException` 的原因有很多，需要以交互方式解决。 以下是一些常见的原因：

- 用户更改了与帐户关联的密码。
- 用户的帐户不再满足条件性访问策略。
- 用户撤消了其同意将应用与其帐户关联。

### <a name="when-a-broker-is-uninstalled"></a>卸载代理时

如果只安装了一个代理托管应用，并且该应用已被删除，则该用户将需要再次登录。 卸载活动代理会从设备中删除帐户和关联的令牌。

如果 Intune 公司门户已安装并且正在作为 active broker 运行，并且还安装了 Microsoft Authenticator，则在 Intune 公司门户（活动 broker）已卸载的情况下，用户将需要再次登录。 一旦他们重新登录，Microsoft Authenticator 应用就会成为活动 broker。

## <a name="integrating-with-a-broker"></a>与 broker 集成

### <a name="generating-a-redirect-uri-for-a-broker"></a>正在为 broker 生成重定向 URI

必须注册与 broker 兼容的重定向 URI。 代理的重定向 URI 需要包含应用的包名称，以及应用签名的 base64 编码表示形式。

重定向 URI 的格式为： `msauth://<yourpackagename>/<base64urlencodedsignature>`

使用应用的签名密钥生成 Base64 url 编码签名。 下面是使用您的调试签名密钥的一些示例命令：

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

有关对应用进行签名的信息，请参阅[对应用进行签名](https://developer.android.com/studio/publish/app-signing)。

> [!IMPORTANT]
> 为应用的生产版本使用生产签名密钥。

### <a name="configure-msal-to-use-a-broker"></a>将 MSAL 配置为使用 broker

若要在应用中使用 broker，必须证明已配置了 broker 重定向。 例如，同时包含启用了 broker 的重定向 URI--并指示你已注册，方法是在 MSAL 配置文件中包含以下内容：

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> 新的 Azure 门户应用注册用户界面可帮助你生成 broker 重定向 URI。 如果你使用较旧的体验注册了应用，或者使用 Microsoft 应用注册门户注册了应用，则可能需要生成重定向 URI，并手动更新门户中的重定向 Uri 列表。

### <a name="broker-related-exceptions"></a>与 Broker 相关的异常

MSAL 通过两种方式与 broker 通信：

- 代理绑定服务
- Android AccountManager

MSAL 首先使用 broker 绑定的服务，因为调用此服务不需要任何 Android 权限。 如果绑定到绑定服务失败，MSAL 将使用 Android AccountManager API。 仅当你的应用程序已被授予 `"READ_CONTACTS"` 权限时，MSAL 才会执行此功能。

如果收到 `MsalClientException` 错误代码 `"BROKER_BIND_FAILURE"`，则有两个选项：

- 要求用户禁用 Microsoft Authenticator 应用和 Intune 公司门户的电源优化。
- 要求用户授予 `"READ_CONTACTS"` 权限
