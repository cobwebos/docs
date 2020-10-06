---
title: Android 中的中介身份验证 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft 标识平台中适用于 Android 的中介身份验证和授权概述
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 5042bfad2cfe06c7c368c6b476aa1b02d67bcc9c
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760748"
---
# <a name="brokered-authentication-in-android"></a>Android 中的中介身份验证

必须使用 Microsoft 的身份验证代理之一参与设备范围的单一登录 (SSO) 并满足组织的条件性访问策略。 与中介集成可提供以下优势：

- 设备单一登录
- 对以下功能进行条件访问：
  - Intune 应用保护
  - 设备注册 (Workplace Join)
  - 移动设备管理
- 设备范围的帐户管理
  -  通过 Android AccountManager 和帐户设置
  - “工作帐户”- 自定义帐户类型

在 Android 上，Microsoft 身份验证代理是 [Microsoft Authenticator 应用](https://play.google.com/store/apps/details?id=com.azure.authenticator) 和 [Intune 公司门户](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)附带的组件。

下图演示了应用、Microsoft 身份验证库 (MSAL) 与 Microsoft 身份验证中介之间的关系。

![显示应用程序如何与 MSAL、broker 应用和 Android 帐户管理器相关联的关系图。](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>安装托管中介的应用

代理托管应用可以由设备所有者从其应用商店进行安装 (通常 Google Play 商店) 。 但是，某些 API（资源）受条件访问策略的保护，这些策略要求设备：

- 已注册（已加入工作区），和/或
- 已在设备管理中注册，或
- 已在 Intune 应用保护中注册

如果设备尚未安装代理应用，则 MSAL 会指示用户在应用尝试以交互方式获取令牌时立即安装一个。 然后，应用需要引导用户完成相关步骤，使设备符合所需的策略。

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>安装和卸载中介的影响

### <a name="when-a-broker-is-installed"></a>已安装中介时

在设备上安装中介后，所有后续交互式令牌请求（对 `acquireToken()` 的调用）将由该中介处理，而不是由 MSAL 在本地处理。 以前提供给 MSAL 的任何 SSO 状态不会提供给中介。 因此，用户需要重新进行身份验证，或者从设备已知的现有帐户列表中选择一个帐户。

安装代理不需要用户重新登录。 仅当用户需要解决 `MsalUiRequiredException` 时，下一个请求才会发送到中介。 `MsalUiRequiredException` 可能出于多种原因而引发，需要以交互方式解决。 例如： 。

- 用户更改了与其帐户关联的密码。
- 用户的帐户不再符合条件访问策略。
- 用户撤销了应用与其帐户关联的许可。

#### <a name="multiple-brokers"></a>多个代理

如果设备上安装了多个代理，则首先安装的代理始终是活动 broker。 设备上只有一个代理可以处于活动状态。

### <a name="when-a-broker-is-uninstalled"></a>已卸载中介时

如果只安装了一个中介托管应用，后来删除了该应用，则用户需要再次登录。 卸载活动中介会从设备中删除相应的帐户和关联的令牌。

如果 Intune 公司门户已安装并作为活动中介运行，此外还安装了 Microsoft Authenticator，则在卸载 Intune 公司门户（活动中介）后，用户需要再次登录。 一旦用户再次登录，Microsoft Authenticator 应用就会成为活动中介。

## <a name="integrating-with-a-broker"></a>与中介集成

### <a name="generating-a-redirect-uri-for-a-broker"></a>为中介生成重定向 URI

必须注册与中介兼容的重定向 URI。 代理的重定向 URI 应包含应用的包名称和应用签名的 Base64 编码表示形式。

重定向 URI 的格式为：`msauth://<yourpackagename>/<base64urlencodedsignature>`

可以使用 [keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) 通过应用的签名密钥生成 Base64 编码的签名哈希，然后使用该 Azure 门户生成使用该哈希的重定向 URI。

Linux 和 macOS：

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

使用 *keytool*生成签名哈希后，请使用 Azure 门户生成重定向 URI：

1. 登录到 [Azure 门户](https://portal.azure.com) 并选择 **应用注册**中的 Android 应用。
1. 选择 "**身份验证**" "  >  **添加平台**  >  **Android**"。
1. 在 " **配置 Android 应用程序** " 窗格打开后，输入前面生成的 **签名哈希** 和 **包名称**。
1. 选择 " **配置** " 按钮。

Azure 门户会为你生成重定向 URI，并将其显示在 " **Android 配置** " 窗格的 " **重定向 uri** " 字段中。

有关对应用进行签名的详细信息，请参阅 Android Studio 用户指南中的 " [签署应用](https://developer.android.com/studio/publish/app-signing) "。

> [!IMPORTANT]
> 对于生产版本的应用，请使用生产签名密钥。

### <a name="configure-msal-to-use-a-broker"></a>将 MSAL 配置为使用中介

若要在应用中使用中介，必须证明你已配置中介重定向。 例如，同时包含启用了 broker 的重定向 URI--并指示你已注册，方法是在 MSAL 配置文件中包括以下设置：

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

### <a name="broker-related-exceptions"></a>中介相关的异常

MSAL 通过两种方式来与中介通信：

- 中介绑定服务
- Android AccountManager

MSAL 首先使用 broker 绑定的服务，因为调用此服务不需要任何 Android 权限。 如果绑定到绑定服务失败，MSAL 将使用 Android AccountManager API。 MSAL 仅当你的应用程序已被授予了该权限时才这样做 `"READ_CONTACTS"` 。

如果收到 `MsalClientException` 和错误代码 `"BROKER_BIND_FAILURE"`，可以采取两种做法：

- 要求用户禁用 Microsoft Authenticator 应用和 Intune 公司门户的超级优化。
- 要求用户授予 `"READ_CONTACTS"` 权限

## <a name="verifying-broker-integration"></a>验证中介集成

虽然可能无法立即弄清楚中介集成是否正在运行，但你可以使用以下步骤进行检查：

1. 在 Android 设备上，使用中介完成某个请求。
1. 在 Android 设备上的设置中，查找与进行身份验证时所使用的帐户相对应的新建帐户。 该帐户的类型应为“工作帐户”。

如果要重复测试，可以从设置中删除该帐户。

## <a name="next-steps"></a>后续步骤

[适用于 android 设备的共享设备模式](msal-android-shared-devices.md) 允许你配置 android 设备，以便多名员工可以轻松共享该设备。
