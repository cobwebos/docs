---
title: 适用于 iOS 设备的共享设备模式
titleSuffix: Microsoft identity platform | Azure
description: 了解如何启用共享设备模式，使一线工作人员能够共享 iOS 设备
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: d5699c1d08df8364f33371f911ea3be892b4b285
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90528122"
---
# <a name="shared-device-mode-for-ios-devices"></a>适用于 iOS 设备的共享设备模式

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

一线工作人员（例如零售职员、机组人员和现场服务工人）通常使用共享移动设备来完成其工作。 如果用户共享其密码或 PIN（不管是有意还是无意）来访问共享设备上的客户和数据业务数据，那么这些共享设备可能会有安全风险。

使用共享设备模式，能够将 iOS 13 或更高版本的设备配置为可由员工更轻松、更安全地共享。 员工可以登录并快速访问客户信息。 当他们完成其班次或任务后，他们可以从设备注销，然后，该设备将立即可供下一名员工使用。

共享设备模式还提供 Microsoft 标识支持的设备管理。

此功能使用 [Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md) 来管理设备上的用户，并分发 [适用于 Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)。

## <a name="create-a-shared-device-mode-app"></a>创建共享设备模式应用

开发人员和云设备管理员可以共同创建共享设备模式应用：

1. 应用程序开发人员编写单帐户应用（共享设备模式不支持多帐户应用），并编写代码来处理共享设备注销等任务  。

1. 设备管理员使用 Microsoft Intune 等移动设备管理 (MDM) 提供程序来管理其组织中的设备，以准备好要共享的设备  。 MDM 将 Microsoft Authenticator 应用推送到设备，并通过设备的配置文件更新来为每台设备启用“共享模式”。 正是此“共享模式”设置更改了设备上受支持应用的行为。 MDM 提供程序中的此项配置为设备设置共享设备模式，并启用共享设备模式所需的[适用于 Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)。

1. [**仅在公共预览期间必需**]具有 [云设备管理员](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) 角色的用户必须启动 [Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md) 并将其设备加入组织。

    若要在 Azure 门户中配置组织角色的成员身份：“Azure Active Directory” > “角色和管理员” > “云设备管理员”   

以下部分将帮助你更新应用程序以支持共享设备模式。

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>使用 Intune 启用共享设备模式和 SSO 扩展

> [!NOTE]
> 仅在公共预览期需要执行以下步骤。

需要将设备配置为支持共享设备模式。 必须在该设备上安装 iOS 13+，并将该设备注册到 MDM。 MDM 配置还需要启用[适用于 Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)。 若要详细了解 SSO 扩展，请参阅 [Apple 视频](https://developer.apple.com/videos/play/tech-talks/301/)。

1. 在 Intune 配置门户中，使用以下配置告知设备要启用[适用于 Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)：

    - **类型**：重定向
    - **扩展 ID**：com.microsoft.azureauthenticator.ssoextension
    - **团队 ID**： iOS) 不需要此字段 (
    - **URL**：   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **要配置的其他数据**：
      - 密钥：sharedDeviceMode
      - 键入：布尔
      - 值： true

    有关使用 Intune 进行配置的详细信息，请参阅 [Intune 配置文档](/intune/configuration/ios-device-features-settings)。

1. 接下来，配置 MDM 以通过 MDM 配置文件将 Microsoft Authenticator 应用推送到设备。

    设置以下配置选项以启用共享设备模式：

    - 配置 1：
      - 密钥：sharedDeviceMode
      - 键入：布尔
      - 值： true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>修改 iOS 应用程序以支持共享设备模式

用户依赖于你来确保其数据不会泄露给其他用户。 以下部分提供了有用的信号，用于向应用程序指示已发生了需要处理的更改。

每次使用应用时，你都要负责检查设备上用户的状态，并清除上一个用户的数据。 这还包括检查应用是不是从正在执行多个任务的后台重新加载的。

发生用户更改后，你应确保前一个用户的数据已清除，并且应用程序中显示的所有缓存数据已删除。 我们强烈建议你和你的公司在更新应用以支持共享设备模式之后，执行安全评审过程。

### <a name="detect-shared-device-mode"></a>检测共享设备模式

检测共享设备模式对于应用程序而言非常重要。 许多应用程序在共享设备上使用时，都需要对用户体验 (UX) 进行更改。 例如，应用程序可能会提供一个“注册”功能，而该功能并不适用于一线工作人员，因为他们可能已有一个帐户。 如果应用程序处于共享设备模式，你还可能想要为应用程序的数据处理添加额外的安全性。

使用 `MSALPublicClientApplication` 中的 `getDeviceInformationWithParameters:completionBlock:` API 确定应用是否正在处于共享设备模式的设备上运行。

以下代码片段演示使用 `getDeviceInformationWithParameters:completionBlock:` API 的示例。

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>获取已登录用户并确定该设备上的用户是否已更改

支持共享设备模式的另一个重要部分是确定设备上用户的状态，并在设备上的用户已更改或者设备上根本没有任何用户时清除应用程序数据。 你需要负责确保数据不会泄露给其他用户。

可以使用 `getCurrentAccountWithParameters:completionBlock:` API 查询设备上当前已登录的帐户。

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>将用户全局登录

将设备配置为共享设备后，应用程序可以调用 `acquireTokenWithParameters:completionBlock:` API 将帐户登录。 第一个应用将帐户登录后，该帐户将全局性地可供设备上所有符合条件的应用使用。

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>将用户全局注销

以下代码将删除已登录的帐户，并且不仅从应用中清除缓存的令牌，而且还从处于共享设备模式下的设备中清除缓存的令牌。 不过，它不会清除应用程序中的数据  。 你必须清除应用程序中的数据，并清除应用程序可能会向用户显示的所有缓存数据。

#### <a name="clear-browser-state"></a>清除浏览器状态

> [!NOTE]
> 仅在公共预览期需要执行以下步骤。

在此公共预览版中，[适用于 Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)只会为应用程序清除状态。 它不会清除 Safari 浏览器中的状态。 我们建议手动清除浏览器会话，以确保不会留下用户状态的跟踪信息。 可以使用下面所示的可选 `signoutFromBrowser` 属性来清除任何 Cookie。 这会导致在设备上短暂启动浏览器。

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>后续步骤

GitHub 上的以下代码示例可帮助你了解共享设备模式的运作方式，该示例演示如何在处于共享设备模式的 iOS 设备上运行某个一线工作人员应用：

[MSAL iOS Swift Microsoft Graph API 示例](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
