---
title: 在 iOS 上添加身份验证
description: 了解如何使用 Azure 移动应用通过 AAD、Google、Facebook、Twitter 和 Microsoft 等标识提供者对 iOS 应用的用户进行身份验证。
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 925894cab00537cb9aeb03ca05c9699bb4bf6a84
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668457"
---
# <a name="add-authentication-to-your-ios-app"></a>Add authentication to your iOS app（将身份验证添加到 iOS 应用）
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”、“测试”和“分发”服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用“分析”和“诊断”服务监视其应用的状态和使用情况，并使用“推送”服务吸引用户。 开发人员还可以利用“身份验证”对其用户进行身份验证，并使用“数据”服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

本教程介绍如何使用支持的标识提供者向 [iOS 快速入门]项目添加身份验证。 本教程基于 [iOS 快速入门]教程，这是必须首先完成的教程。

## <a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>将应用添加到允许的外部重定向 URL

安全身份验证要求为应用定义新的 URL 方案。  这允许身份验证系统在身份验证过程完成后，重定向回应用。  在本教程中，我们将通篇使用 URL 方案 _appname_。  但是，可以使用所选择的任何 URL 方案。  对于移动应用程序而言，它应是唯一的。  在服务器端启用重定向：

1. 在 [Azure 门户]中，选择“应用服务”。

2. 单击“身份验证/授权”菜单选项。

3. 单击“身份验证提供程序”部分下的“Azure Active Directory”。

4. 将“管理模式”设置为“高级”。

5. 在“允许的外部重定向 URL”中，输入 `appname://easyauth.callback`。  此字符串中的 _appname_ 是移动应用程序的 URL 方案。  它应该遵循协议的正常 URL 规范（仅使用字母和数字，并以字母开头）。  请记下所选的字符串，你将需要在几个地方使用 URL 方案调整移动应用程序代码。

6. 单击 **“确定”** 。

7. 单击“保存”。

## <a name="permissions"></a>将权限限制为已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Xcode 中，按“运行”启动应用。 这样将引发异常，因为应用尝试以未经身份验证的用户身份访问后端，但 *TodoItem* 表现在要求身份验证。

## <a name="add-authentication"></a>向应用添加身份验证
**Objective-C**：

1. 在 Mac 的 Xcode 中打开 QSTodoListViewController.m 并添加以下方法：

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    若未使用 Google 作为标识提供者，请将 google 更改为 microsoftaccount、twitter、facebook 或 windowsazureactivedirectory。 如果使用 Facebook，则必须在应用中将[Facebook 域列入][1]允许列表。

    将 **urlScheme** 替换为应用程序的唯一名称。  urlScheme 应与在 Azure 门户的“允许的外部重定向 URL”字段中所指定的 URL 方案协议保持一致。 当身份验证请求完成后，身份验证回调使用 urlScheme 切回应用程序。

2. 按以下代码替换 QSTodoListViewController.m 的 `viewDidLoad` 中的 `[self refresh]`：

    ```Objective-C
    [self loginAndGetData];
    ```

3. 打开 `QSAppDelegate.h` 文件，并添加以下代码：

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. 打开 `QSAppDelegate.m` 文件，并添加以下代码：

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   在行读取 `#pragma mark - Core Data stack` 前直接添加此代码。  将_appname_替换为你在步骤1中使用的 urlScheme 值。

5. 打开 `AppName-Info.plist` 文件（用应用的名称替换 AppName），并添加以下代码：

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    此代码应置于 `<dict>` 元素内部。  用在步骤 1 中选择的应用名称替换 _appname_ 字符串（在 **CFBundleURLSchemes** 的数组内）。  还可在 plist 编辑器中做这些更改 - 单击 XCode 中的 `AppName-Info.plist` 文件，打开 plist 编辑器。

    使用 Apple 捆绑包标识符将 `com.microsoft.azure.zumo` 字符串替换为 **CFBundleURLName**。

6. 按“运行”以启动应用，并登录。 登录时，应能够查看 Todo 列表并进行更新。

**Swift**：

1. 在 Mac 的 Xcode 中打开 *ToDoTableViewController.swift* 并添加以下方法：

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    若未使用 Google 作为标识提供者，请将 google 更改为 microsoftaccount、twitter、facebook 或 windowsazureactivedirectory。 如果使用 Facebook，则必须在应用中将[Facebook 域列入][1]允许列表。

    将 **urlScheme** 替换为应用程序的唯一名称。  urlScheme 应与在 Azure 门户的“允许的外部重定向 URL”字段中所指定的 URL 方案协议保持一致。 当身份验证请求完成后，身份验证回调使用 urlScheme 切回应用程序。

2. 删除 *ToDoTableViewController.swift* 中 `viewDidLoad()` 末尾的 `self.refreshControl?.beginRefreshing()` 和 `self.onRefresh(self.refreshControl)` 行。 在其位置上添加对 `loginAndGetData()` 的调用：

    ```swift
    loginAndGetData()
    ```

3. 打开 `AppDelegate.swift` 文件，将以下行添到 `AppDelegate` 类：

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    将_appname_替换为你在步骤1中使用的 urlScheme 值。

4. 打开 `AppName-Info.plist` 文件（用应用的名称替换 AppName），并添加以下代码：

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    此代码应置于 `<dict>` 元素内部。  用在步骤 1 中选择的应用名称替换 _appname_ 字符串（在 **CFBundleURLSchemes** 的数组内）。  还可在 plist 编辑器中做这些更改 - 单击 XCode 中的 `AppName-Info.plist` 文件，打开 plist 编辑器。

    使用 Apple 捆绑包标识符将 `com.microsoft.azure.zumo` 字符串替换为 **CFBundleURLName**。

5. 按“运行”以启动应用，并登录。 登录时，应能够查看 Todo 列表并进行更新。

应用服务身份验证使用 Apple 应用间通信。  有关此主题的更多详细信息，请参阅[Apple 文档][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure 门户]: https://portal.azure.com

[iOS 快速入门]: app-service-mobile-ios-get-started.md

