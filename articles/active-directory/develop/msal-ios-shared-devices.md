---
title: 适用于 iOS 设备的共享设备模式
titleSuffix: Microsoft identity platform | Azure
description: 了解如何启用共享设备模式以允许 Firstline 工作人员共享 iOS 设备
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
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550248"
---
# <a name="shared-device-mode-for-ios-devices"></a>适用于 iOS 设备的共享设备模式

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Firstline 工作人员（例如零售协会、航班员工和现场服务工作人员）通常使用共享的移动设备来执行工作。 如果用户无意中共享其密码或 Pin 来访问共享设备上的客户和业务数据，则这些共享设备可能会带来安全风险。

使用共享设备模式，你可以配置 iOS 13 或更高版本的设备，使其更轻松、更安全地由员工共享。 员工可以登录并快速访问客户信息。 当他们完成了移动或任务后，他们可以从设备注销，并立即供下一名员工使用。

共享设备模式还提供了 Microsoft 的设备支持身份的管理。

此功能使用[Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)来管理设备上的用户，并分发[适用于 Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)。

## <a name="create-a-shared-device-mode-app"></a>创建共享设备模式应用

开发人员和云设备管理员可以共同创建共享设备模式应用：

1. **应用程序开发人员**编写单帐户应用（在共享设备模式下不支持多帐户应用）并编写代码来处理共享设备注销等任务。

1. **设备管理员**使用移动设备管理（MDM）提供程序（如 Microsoft Intune 管理其组织中的设备）准备要共享的设备。 MDM 将 Microsoft Authenticator 应用推送到设备，并通过设备的配置文件更新打开每个设备的 "共享模式"。 此共享模式设置将更改设备上受支持的应用程序的行为。 MDM 提供程序的此配置设置设备的共享设备模式，并为共享设备模式所需的[Apple 设备启用 Microsoft 企业 SSO 插件](apple-sso-plugin.md)。

1. [**仅在公共预览期间必需**]具有[云设备管理员](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator)角色的用户必须启动[Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)并将其设备加入组织。

    在 Azure 门户中配置组织角色的成员身份： **Azure Active Directory** > **角色和管理员** > **云设备管理员**

以下各节可帮助你更新应用程序以支持共享设备模式。

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>使用 Intune 启用共享设备模式 & SSO 扩展

> [!NOTE]
> 仅在公共预览期间需要执行以下步骤。

需要将设备配置为支持共享设备模式。 它必须安装了 iOS 13 +，并已注册了 MDM。 MDM 配置还需要为[Apple 设备启用 Microsoft 企业 SSO 插件](apple-sso-plugin.md)。 若要了解有关 SSO 扩展的详细信息，请参阅[Apple 视频](https://developer.apple.com/videos/play/tech-talks/301/)。

1. 在 Intune 配置门户中，告诉设备使用以下配置为[Apple 设备启用 Microsoft 企业 SSO 插件](apple-sso-plugin.md)：

    - **类型**：重定向
    - **扩展 ID**： azureauthenticator. ssoextension
    - **团队 ID**： SGGM6D27TK
    - **Url**：https://login.microsoftonline.com
    - 要配置的其他数据：
      - 密钥： sharedDeviceMode
      - 类型：布尔值
      - 值： True

    有关配置 Intune 的详细信息，请参阅[intune 配置文档](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)。

1. 接下来，将 MDM 配置为通过 MDM 配置文件将 Microsoft Authenticator 应用推送到设备。

    设置以下配置选项以打开共享设备模式：

    - 配置 1：
      - 密钥： sharedDeviceMode
      - 类型：布尔值
      - 值： True

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>修改 iOS 应用程序以支持共享设备模式

用户需要确保其数据不会泄露给其他用户。 以下各节提供了有用的信号来向应用程序指示更改已发生并且应进行处理。

每次使用应用程序时，都要负责检查设备上用户的状态，并清除以前的用户数据。 这包括在多任务中从后台重新加载时的情况。

用户更改时，应确保已清除上一用户的数据，并且删除应用程序中显示的所有缓存数据。 我们强烈建议您和您的公司在更新应用程序以支持共享设备模式之后执行安全审核过程。

### <a name="detect-shared-device-mode"></a>检测共享设备模式

检测共享设备模式对于您的应用程序很重要。 当应用程序用于共享设备时，许多应用程序都需要更改用户体验（UX）。 例如，你的应用程序可能有一个 "注册" 功能，该功能不适合 Firstline 工作线程，因为他们可能已经有了一个帐户。 如果你的应用程序的数据处于共享设备模式，则可能还需要为其添加额外的安全性。

使用中`getDeviceInformationWithParameters:completionBlock:`的 API `MSALPublicClientApplication`来确定应用是否正在处于共享设备模式的设备上运行。

以下代码片段显示了`getDeviceInformationWithParameters:completionBlock:`使用 API 的示例。

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>获取已登录用户并确定是否已在设备上更改了用户

支持共享设备模式的另一个重要部分是确定设备上用户的状态，并在用户已更改或设备上没有用户时清除应用程序数据。 你负责确保数据不会泄露给其他用户。

你可以使用`getCurrentAccountWithParameters:completionBlock:` API 来查询设备上当前已登录的帐户。

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

当设备配置为共享设备时，应用程序可以调用`acquireTokenWithParameters:completionBlock:` API 来登录帐户。 首次应用登录帐户后，该帐户将在设备上全局提供给所有符合条件的应用。

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>将用户全局注销

下面的代码将删除已登录的帐户，并清除应用程序中的缓存令牌，还会从共享设备模式下的设备中清除。 不过，它不会清除应用程序中的*数据*。 您必须清除应用程序中的数据，并清除您的应用程序可能会向用户显示的所有缓存数据。

#### <a name="clear-browser-state"></a>清除浏览器状态

> [!NOTE]
> 仅在公共预览期间需要执行以下步骤。

在此公共预览版中， [Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)仅清除应用程序的状态。 它不会在 Safari 浏览器中清除状态。 建议手动清除浏览器会话，以确保不会留下用户状态跟踪。 可以使用下面所示`signoutFromBrowser`的可选属性来清除任何 cookie。 这将导致浏览器在设备上启动。

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

若要查看操作中的共享设备模式，GitHub 上的以下代码示例包含一个示例，该示例在 iOS 设备上以共享设备模式运行 Firstline Worker 应用程序：

[MSAL iOS Swift Microsoft Graph API 示例](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
