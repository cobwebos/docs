---
title: Android 中的中介身份验证 | Azure
titlesuffix: Microsoft identity platform
description: Microsoft 标识平台中适用于 Android 的中介身份验证和授权概述
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697891"
---
# <a name="brokered-authentication-in-android"></a>Android 中的中介身份验证

必须使用 Microsoft 的某个身份验证中介来参与设备范围的单一登录 (SSO) 并符合组织条件访问策略的要求。 与中介集成可提供以下优势：

- 设备单一登录
- 对以下功能进行条件访问：
  - Intune 应用保护
  - 设备注册 (Workplace Join)
  - 移动设备管理
- 设备范围的帐户管理
  -  通过 Android AccountManager 和帐户设置
  - “工作帐户”- 自定义帐户类型

在 Android 上，微软身份验证代理是[微软身份验证器应用](https://play.google.com/store/apps/details?id=com.azure.authenticator)和[Intune 公司门户](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)中包含的组件

> [!TIP]
> 每次只有一个托管中介的应用程序作为中介处于活动状态。 作为中介处于活动状态的应用程序由设备上的安装顺序确定。 要安装的第一个中介或设备上的最后一个中介将成为活动中介。

下图演示了应用、Microsoft 身份验证库 (MSAL) 与 Microsoft 身份验证中介之间的关系。

![中介部署关系图](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>安装托管中介的应用

代理托管应用可以在任何时候由设备所有者从其应用商店（通常是 Google Play 商店）安装。 但是，某些 API（资源）受条件访问策略的保护，这些策略要求设备：

- 已注册（已加入工作区），和/或
- 已在设备管理中注册，或
- 已在 Intune 应用保护中注册

如果某个设备上尚未安装中介应用，当应用尝试以交互方式获取令牌时，MSAL 会立即指示用户安装一个中介应用。 然后，应用需要引导用户完成相关步骤，使设备符合所需的策略。

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>安装和卸载中介的影响

### <a name="when-a-broker-is-installed"></a>已安装中介时

在设备上安装中介后，所有后续交互式令牌请求（对 `acquireToken()` 的调用）将由该中介处理，而不是由 MSAL 在本地处理。 以前提供给 MSAL 的任何 SSO 状态不会提供给中介。 因此，用户需要重新进行身份验证，或者从设备已知的现有帐户列表中选择一个帐户。

安装中介不需要用户再次登录。 仅当用户需要解决 `MsalUiRequiredException` 时，下一个请求才会发送到中介。 引发 `MsalUiRequiredException` 的原因有很多，需要以交互方式解决。 下面是一些常见原因：

- 用户更改了与其帐户关联的密码。
- 用户的帐户不再符合条件访问策略。
- 用户撤销了应用与其帐户关联的许可。

### <a name="when-a-broker-is-uninstalled"></a>已卸载中介时

如果只安装了一个中介托管应用，后来删除了该应用，则用户需要再次登录。 卸载活动中介会从设备中删除相应的帐户和关联的令牌。

如果 Intune 公司门户已安装并作为活动中介运行，此外还安装了 Microsoft Authenticator，则在卸载 Intune 公司门户（活动中介）后，用户需要再次登录。 一旦用户再次登录，Microsoft Authenticator 应用就会成为活动中介。

## <a name="integrating-with-a-broker"></a>与中介集成

### <a name="generating-a-redirect-uri-for-a-broker"></a>为中介生成重定向 URI

必须注册与中介兼容的重定向 URI。 中介的重定向 URI 需要包含应用的包名称，以及应用签名的 base64 编码表示形式。

重定向 URI 的格式为：`msauth://<yourpackagename>/<base64urlencodedsignature>`

使用应用的签名密钥生成 Base64 URL 编码的签名。 下面是使用调试签名密钥的一些示例命令：

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

有关为应用签名的信息，请参阅[为应用签名](https://developer.android.com/studio/publish/app-signing)。

> [!IMPORTANT]
> 对于生产版本的应用，请使用生产签名密钥。

### <a name="configure-msal-to-use-a-broker"></a>将 MSAL 配置为使用中介

若要在应用中使用中介，必须证明你已配置中介重定向。 例如，通过在 MSAL 配置文件中包含以下内容，来包含中介启用的重定向 URI，并指示已注册该中介：

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> 新的 Azure 门户应用注册 UI 可帮助你生成中介重定向 URI。 如果应用是使用旧版体验注册的，或者是使用 Microsoft 应用注册门户注册的，则可能需要在门户中手动生成重定向 URI 并更新重定向 URI 列表。

### <a name="broker-related-exceptions"></a>中介相关的异常

MSAL 通过两种方式来与中介通信：

- 中介绑定服务
- Android AccountManager

MSAL 首先使用中介绑定服务，因为调用此服务不需要任何 Android 权限。 如果绑定到绑定服务失败，MSAL 将使用 Android AccountManager API。 仅当已为应用授予 `"READ_CONTACTS"` 权限时，MSAL 才使用此 API。

如果收到 `MsalClientException` 和错误代码 `"BROKER_BIND_FAILURE"`，可以采取两种做法：

- 要求用户禁用 Microsoft Authenticator 应用和 Intune 公司门户的超级优化。
- 要求用户授予 `"READ_CONTACTS"` 权限
