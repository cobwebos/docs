---
title: iOS 入门 - Microsoft 标识平台 | Azure
description: iOS (Swift) 应用程序如何使用 Microsoft 标识平台调用需要访问令牌的 API
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06b4ecc151f8402bfb8f05968475b0305737ecd9
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406482"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>从 iOS 应用将用户登录并调用 Microsoft Graph

在本教程中，你将了解如何构建 iOS 应用程序并将其集成到 Microsoft 标识平台中。 具体而言，此应用会将用户登录，获取用于调用 Microsoft Graph API 的访问令牌，并针对 Microsoft Graph API 发出基本请求。  

完成本指南后，应用程序将接受个人 Microsoft 帐户（包括 outlook.com、live.com 和其他帐户）进行登录，还能够接受使用 Azure Active Directory 的任何公司或组织的工作或学校帐户进行登录。

## <a name="how-this-guide-works"></a>本指南的工作原理

![显示本教程生成的示例应用的工作原理](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

此示例中的应用会将用户登录并代表他们获取数据。  将通过一个受保护 API（在本例中为 Microsoft 图形 API）访问该数据，该 API 要求授权并且还受 Microsoft 标识平台保护。

更具体说来：

* 你的应用将通过浏览器或 Microsoft Authenticator 使用户登录。
* 最终用户将接受应用程序请求的权限。 
* 将为你的应用颁发 Microsoft Graph API 的一个访问令牌。
* 该访问令牌将包括在对 Web API 的 HTTP 请求中。
* 处理 Microsoft Graph 响应。

该示例使用 Microsoft 身份验证库 (MSAL) 来实现身份验证。MSAL 将自动续订令牌，在设备上的其他应用之间提供 SSO，并管理帐户。

## <a name="prerequisites"></a>先决条件

- XCode 版本 10.x 是在本指南中创建的示例所必需的。 可从 [iTunes 网站](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下载 URL") 下载 XCode。
- Microsoft 身份验证库 ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。 可使用依赖项管理器或手动添加。 以下部分提供了[详细信息](#add-msal)。 

## <a name="set-up-your-project"></a>设置项目

本教程将创建新项目。 如果想要下载完整教程，请[下载代码](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)。

### <a name="create-a-new-project"></a>创建新项目

1. 打开 Xcode，并选择“新建 Xcode 项目”。
2. 选择“iOS”>“单一视图应用程序”，并选择“下一步”。
3. 提供产品名称并选择“下一步”。
4. 选择一个文件夹以创建应用，然后单击“创建”。

## <a name="register-your-application"></a>注册应用程序 

如接下来的两部分中所述，可以采用两种方式之一注册应用程序。

### <a name="register-your-app"></a>注册应用

1. 转到 [Azure 门户](https://aka.ms/MobileAppReg)，然后选择 `New registration`。 
2. 为你的应用输入名称，然后选择 `Register`。 在此阶段不设置重定向 URI。 
3. 在 `Manage` 部分，转到 `Authentication` > `Add a platform` > `iOS`
    - 输入项目的捆绑 ID。 如果下载了代码，则为 `com.microsoft.identitysample.MSALiOS`。
4. 点击 `Configure` 并存储 `MSAL Configuration` 以备稍后使用。 

## <a name="add-msal"></a>添加 MSAL

### <a name="get-msal"></a>获取 MSAL

#### <a name="cocoapods"></a>CocoaPods

可使用 [CocoaPods](https://cocoapods.org/) 安装 `MSAL`，方法是将其添加到以下目标下的 `Podfile`：

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

可通过将 [Carthage](https://github.com/Carthage/Carthage) 添加到 `Cartfile` 来安装 `MSAL`： 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>手动

还可使用 Git 子模块或查看最新版本，并在应用程序中用作框架。

### <a name="add-your-app-registration"></a>添加应用注册

接下来，将应用注册添加到代码中。 将“客户端/应用程序 ID”添加到 `ViewController.swift`：

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>配置 URL 方案

注册 `CFBundleURLSchemes` 以允许回叫，以便用户在登录后可重定向回应用。

通过 `LSApplicationQueriesSchemes`，可使用应用来使用 Microsoft Authenticator（如果可用）。 

为此，请打开 `Info.plist` 作为源代码并添加以下内容，将“BUNDLE_ID”替换为你在 Azure 门户中配置的内容。

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

### <a name="import-msal"></a>导入 MSAL

在 `ViewController.swift` 和 `AppDelegate.swift` 文件中导入 MSAL 框架：

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>创建应用 UI

对于本教程的内容，你需要创建：

- 调用 Graph API 按钮
- 注销按钮
- 日志记录 textview

在 `ViewController.swift` 中，定义属性和 `initUI()`，如下所示：

```swift

var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

接下来，添加到 ViewController.swift 的 `viewDidLoad()`：

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>使用 MSAL

### <a name="initialize-msal"></a>初始化 MSAL

首先，需要为你的应用程序创建一个 `MSALPublicClientApplication` 并包含 `MSALPublicClientConfiguration` 实例：

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-callback"></a>处理回叫

要在登录后处理回叫，请在 `appDelegate` 中添加 `MSALPublicClientApplication.handleMSALResponse`：

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>获取令牌

现在，我们可以实现应用程序的 UI 处理逻辑并通过 MSAL 以交互方式获取令牌。 

MSAL 公开了获取令牌的两种主要方法：`acquireTokenSilently` 和 `acquireTokenInteractively`。  

如果有帐户，`acquireTokenSilently()` 会尝试登录用户并获取令牌，而无需任何用户交互。

`acquireTokenInteractively` 在尝试登录用户并获取令牌时将始终显示用户界面；但是，它可能会使用浏览器中的会话 cookie 或 Microsoft Authenticator 中的帐户来提供交互式 SSO 体验。 

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>以交互方式获取令牌

要首次获取令牌，你需要创建 `MSALInteractiveTokenParameters` 并调用 `acquireToken`。

1. 使用作用域创建 `MSALInteractiveTokenParameters`。
2. 使用创建的参数调用 `acquireToken`。
3. 相应地处理错误。 有关详细信息，请参阅 [iOS 错误处理指南](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling)。
4. 处理成功案例。 

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
     // #2        
        applicationContext.acquireToken(with: parameters) { (result, error) in
     // #3            
            if let error = error {
                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }
            guard let result = result else {   
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
     // #4            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```



#### <a name="get-a-token-silently"></a>以无提示方式获取令牌

要以无提示方式获取更新的令牌，需要创建 `MSALSilentTokenParameters` 并调用 `acquireTokenSilent`：

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>调用 Microsoft Graph API 

通过 `self.accessToken` 获得令牌后，你的应用可以使用 HTTP 标头中的此值向 Microsoft Graph 发出授权请求：

| 标头密钥    | 值                 |
| ------------- | --------------------- |
| 授权 | 持有者 <access-token> |

将下列内容添加到 `ViewController.swift`：

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

了解有关 [Microsoft 图形 API](https://graph.microsoft.com) 的详细信息

### <a name="use-msal-for-sign-out"></a>使用 MSAL 进行注销

接下来，我们将添加对注销应用的支持。 

需要注意的是，使用 MSAL 注销会从此应用程序中删除有关用户的所有已知信息，但是用户的设备上仍然有一个活动会话。 如果用户尝试再次登录，则可能会看到交互，但由于设备会话处于活动状态，可能无需重新输入其凭据。 

要添加注销，请将以下方法复制到 `ViewController.swift` 中：

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>启用令牌缓存

默认情况下，MSAL 会在 iOS 密钥链中缓存你的应用令牌。 

要启用令牌缓存，请转到 Xcode 项目设置 > `Capabilities tab` > `Enable Keychain Sharing` > 单击 `Plus` > 输入“com.microsoft.adalcache”。

### <a name="add-helper-methods"></a>添加帮助程序方法

添加这些帮助程序方法以完成示例：

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="multi-account-applications"></a>多帐户应用程序

该应用针对单个帐户方案生成。 MSAL 也支持多帐户方案，但它需要应用的一些额外工作。 需要创建 UI 来帮助用户选择他们想要为每个需要令牌的操作使用的帐户。 或者，你的应用可以通过 `getAllAccounts(...)` 方法实现启发式选择要使用的帐户。

## <a name="test-your-app"></a>测试应用

### <a name="run-locally"></a>在本地运行

如果已遵循上述代码，请尝试构建应用并将其部署到测试设备或模拟器。 你应能够登录并获取 Azure AD 或个人 Microsoft 帐户的令牌！ 用户登录后，此应用将显示从 Microsoft Graph `/me` 终结点返回的数据。 

若有任何问题，请随时打开此文档或 MSAL 库中的问题并告知我们。 

### <a name="consent-to-your-app"></a>许可应用

任何用户首次登录你的应用时，Microsoft 标识都将提示他们同意所请求的权限。  虽然大多数用户都能够同意，但某些 Azure AD 租户已禁用用户同意功能，这要求管理员代表所有用户同意。  要支持此方案，请务必在 Azure 门户中注册应用的作用域。

## <a name="help-and-support"></a>帮助和支持

对本教程或 Microsoft 标识平台有任何问题吗？ 请参阅[帮助与支持](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

