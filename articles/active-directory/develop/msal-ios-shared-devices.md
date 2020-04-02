---
title: iOS 设备的共享设备模式
titleSuffix: Microsoft identity platform | Azure
description: 了解如何启用共享设备模式，以允许一线工作人员共享 iOS 设备
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
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550248"
---
# <a name="shared-device-mode-for-ios-devices"></a>iOS 设备的共享设备模式

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

一线员工（如零售员工、机组人员和现场服务人员）通常使用共享移动设备来执行任务。 如果用户有意或无意共享其密码或 PIN 以访问共享设备上的客户和业务数据，则这些共享设备可能会带来安全风险。

共享设备模式允许您将 iOS 13 或更高版本的设备配置为员工更轻松、更安全地共享设备。 员工可以登录并快速访问客户信息。 当他们完成轮班或任务后，他们可以注销设备，并立即准备好供下一位员工使用。

共享设备模式还提供 Microsoft 标识支持的设备管理。

此功能使用[微软身份验证器应用程序](../user-help/user-help-auth-app-overview.md)来管理设备上的用户，并分发[适用于苹果设备的微软企业 SSO 插件](apple-sso-plugin.md)。

## <a name="create-a-shared-device-mode-app"></a>创建共享设备模式应用

开发人员和云设备管理员可以共同创建共享设备模式应用：

1. **应用程序开发人员**编写单帐户应用（在共享设备模式下不支持多帐户应用），并编写代码来处理共享设备注销等内容。

1. **设备管理员**使用 Microsoft Intune 等移动设备管理 （MDM） 提供商来管理其组织中的设备，准备要共享的设备。 MDM 将 Microsoft 身份验证器应用推送到设备，并通过对设备的配置文件更新打开每个设备的"共享模式"。 此共享模式设置是更改设备上受支持应用的行为的原因。 MDM 提供商的此配置为设备设置共享设备模式，并启用共享设备模式所需的[Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)。

1. [**仅在公共预览期间需要**]然后，具有[云设备管理员](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator)角色的用户必须启动[Microsoft 身份验证器应用](../user-help/user-help-auth-app-overview.md)，并将其设备加入组织。

    要在 Azure 门户中配置组织角色的成员身份 **：Azure 活动目录** > **角色和管理员** > **云设备管理员**

以下部分可帮助您更新应用程序以支持共享设备模式。

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>使用 Intune 启用共享设备模式& SSO 扩展

> [!NOTE]
> 以下步骤仅在公共预览期间是必需的。

您的设备需要配置为支持共享设备模式。 它必须安装 iOS 13+ 并注册 MDM。 MDM 配置还需要启用[适用于 Apple 设备的微软企业 SSO 插件](apple-sso-plugin.md)。 要了解有关 SSO 扩展的更多，请参阅[Apple 视频](https://developer.apple.com/videos/play/tech-talks/301/)。

1. 在 Intune 配置门户中，告诉设备启用具有以下配置[的 Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)：

    - **类型**： 重定向
    - **扩展 ID**： com.microsoft.azure 身份验证器.sso 扩展
    - **团队 ID**： SGGM6D27TK
    - **URL**：https://login.microsoftonline.com
    - 要配置的其他数据：
      - 密钥：共享设备模式
      - 类型：布尔值
      - 值： 正确

    有关使用 Intune 进行配置的详细信息，请参阅[Intune 配置文档](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)。

1. 接下来，配置 MDM 以通过 MDM 配置文件将 Microsoft 身份验证器应用推送到您的设备。

    设置以下配置选项以打开共享设备模式：

    - 配置 1：
      - 密钥：共享设备模式
      - 类型：布尔值
      - 值： 正确

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>修改 iOS 应用程序以支持共享设备模式

您的用户依赖您来确保其数据不会泄露给其他用户。 以下各节提供有用的信号，以向应用程序指示发生了更改，应进行处理。

您负责在每次使用应用时检查设备上的用户状态，然后清除前一个用户的数据。 这包括如果在多任务处理中从后台重新加载它，则包括它。

在用户更改时，应确保清除前一个用户的数据，并删除应用程序中显示的任何缓存数据。 我们强烈建议您和您的公司在更新应用以支持共享设备模式后进行安全审查过程。

### <a name="detect-shared-device-mode"></a>检测共享设备模式

检测共享设备模式对于应用程序非常重要。 许多应用程序在共享设备上使用应用程序时，需要更改其用户体验 （UX）。 例如，您的应用程序可能具有"注册"功能，这不适用于一线工作人员，因为它们可能已经具有帐户。 如果数据处于共享设备模式，您可能还希望为应用程序处理数据添加额外的安全性。

使用`getDeviceInformationWithParameters:completionBlock:`中的 API`MSALPublicClientApplication`确定应用是否以共享设备模式在设备上运行。

以下代码段显示了使用`getDeviceInformationWithParameters:completionBlock:`API 的示例。

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>获取登录用户并确定用户是否在设备上已更改

支持共享设备模式的另一个重要部分是确定用户在设备上的状态，并在用户已更改或设备上根本没有用户时清除应用程序数据。 您有责任确保数据不会泄露给其他用户。

您可以使用`getCurrentAccountWithParameters:completionBlock:`API 查询设备上的当前登录帐户。

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

当设备配置为共享设备时，应用程序可以调用`acquireTokenWithParameters:completionBlock:`API 登录帐户。 该帐户将在帐户中出现第一个应用签名后，面向设备上的所有符合条件的应用在全球可用。

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>将用户全局注销

以下代码删除登录帐户，并清除缓存的令牌，不仅从应用，而且从处于共享设备模式的设备。 但是，它不会清除应用程序*中的数据*。 您必须清除应用程序中的数据，以及清除应用程序可能向用户显示的任何缓存数据。

#### <a name="clear-browser-state"></a>清除浏览器状态

> [!NOTE]
> 以下步骤仅在公共预览期间是必需的。

在此公共预览版本中，适用于[Apple 设备的 Microsoft 企业 SSO 插件](apple-sso-plugin.md)仅清除应用程序的状态。 它不清除 Safari 浏览器上的状态。 我们建议您手动清除浏览器会话，以确保不会留下用户状态的痕迹。 您可以使用下面显示的可选`signoutFromBrowser`属性清除任何 Cookie。 这将导致浏览器在设备上短暂启动。

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

要查看共享设备模式在起作用，GitHub 上的以下代码示例包括一个在共享设备模式下在 iOS 设备上运行一线辅助应用的示例：

[MSAL iOS 斯威夫特微软图形 API 示例](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
