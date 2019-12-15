---
title: iOS 和 macOS 入门 - Microsoft 标识平台 | Azure
description: iOS 和 macOS (Swift) 应用程序如何使用 Microsoft 标识平台调用需要访问令牌的 API
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64f161d94d622ae76932e88be52df6f068bfc945
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964662"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>从 iOS 或 macOS 应用将用户登录并调用 Microsoft Graph

本教程介绍如何将 iOS 或 macOS 应用与 Microsoft 标识平台集成。 应用会将用户登录，获取用于调用 Microsoft Graph API 的访问令牌，并针对 Microsoft Graph API 发出请求。  

完成本指南后，应用程序将接受个人 Microsoft 帐户（包括 outlook.com、live.com 和其他帐户）进行登录，还能够接受使用 Azure Active Directory 的任何公司或组织的工作或学校帐户进行登录。

## <a name="how-this-tutorial-works"></a>本教程工作原理

![显示本教程生成的示例应用的工作原理](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

本教程中的应用会将用户登录并代表他们获取数据。  将通过一个受保护 API（在本例中为 Microsoft Graph API）访问该数据，该 API 要求授权并且受 Microsoft 标识平台保护。

更具体说来：

* 你的应用将通过浏览器或 Microsoft Authenticator 使用户登录。
* 最终用户将接受应用程序请求的权限。
* 将为你的应用颁发 Microsoft Graph API 的一个访问令牌。
* 该访问令牌将包括在对 Web API 的 HTTP 请求中。
* 处理 Microsoft Graph 响应。

该示例使用 Microsoft 身份验证库 (MSAL) 来实现身份验证。 MSAL 将自动续订令牌，在设备上的其他应用之间提供单一登录 (SSO)，并管理帐户。

本教程适用于 iOS 和 macOS 应用。 请注意，这两个平台之间的某些步骤有所不同。 

## <a name="prerequisites"></a>先决条件

- XCode 版本 10.x 或更高版本是在本指南中构建应用所必需的。 可以从 [iTunes 网站](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下载 URL")下载 XCode。
- Microsoft 身份验证库 ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。 可使用依赖项管理器或手动添加库。 以下说明演示了如何操作。

本教程将创建新项目。 如果想要改为下载完整教程，请下载代码：
- [iOS 示例代码](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS 示例代码](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>创建新项目

1. 打开 Xcode，并选择“新建 Xcode 项目”  。
2. 对于 iOS 应用，请选择“iOS”   > “单一视图应用”  并选择“下一步”  。
3. 对于 macOS 应用，请选择“macOS”   > “Cocoa 应用”  并选择“下一步”  。
4. 提供产品名称。
5. 将“语言”设置为“Swift”，然后选择“下一步”。   
6. 选择一个文件夹以创建应用，然后单击“创建”  。

## <a name="register-your-application"></a>注册应用程序

1. 转到 [Azure 门户](https://aka.ms/MobileAppReg)
2. 打开[“应用注册”边栏选项卡](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)，单击“+新建注册”。 
3. 输入应用的“名称”，然后在不设置重定向  URI 的情况下单击“注册”。 
4. 在显示的窗格的“管理”部分，  选择“身份验证”  。

5. 单击靠近屏幕顶部的“尝试新体验”，  打开新的应用注册体验，然后单击“+ 新建注册”   > “+ 添加平台”   >   “iOS”。
    - 输入项目的捆绑 ID。 如果下载了代码，则为 `com.microsoft.identitysample.MSALiOS`。 若要创建自己的项目，请在 Xcode 中选择项目，然后打开“常规”选项卡。  此时捆绑标识符会显示在“标识”部分。 
    - 请注意，对于 macOS，也应该使用 iOS 体验。 
6. 单击 `Configure` 并保存出现在“iOS 配置”页中的“MSAL 配置”   ，以便在稍后配置应用时输入它。  单击“完成”  。

## <a name="add-msal"></a>添加 MSAL

选择以下方式之一在应用中安装 MSAL 库：

### <a name="cocoapods"></a>CocoaPods

1. 如果使用 [CocoaPods](https://cocoapods.org/)，请安装 `MSAL`，方法是先在项目的 `.xcodeproj` 文件所在的文件夹中创建名为 `podfile` 的空文件。 将下列内容添加到 `podfile`：

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. 将 `<your-target-here>` 替换为项目的名称。
3. 在终端窗口中导航到包含所创建的 `podfile` 的文件夹，然后运行 `pod install` 以安装 MSAL 库。
4. 关闭 Xcode，然后打开 `<your project name>.xcworkspace`，以便在 Xcode 中重新加载项目。

### <a name="carthage"></a>Carthage

如果使用 [Carthage](https://github.com/Carthage/Carthage)，请安装 `MSAL`，只需将其添加到 `Cartfile` 即可：

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

在终端窗口中，在与更新的 `Cartfile` 相同的目录中，运行以下命令，让 Carthage 更新项目中的依赖项。

iOS：

```bash
carthage update --platform iOS
```

macOS：

```bash
carthage update --platform macOS
```

### <a name="manually"></a>手动

还可使用 Git 子模块或查看最新版本，以便在应用程序中将其用作框架。

## <a name="add-your-app-registration"></a>添加应用注册

接下来，我们将应用注册添加到代码中。 

首先，将以下 import 语句添加到 `ViewController.swift` 和 `AppDelegate.swift` 文件顶部：

```swift
import MSAL
```

然后，将以下代码添加到 `viewDidLoad()` 之前的 `ViewController.swift`：

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

需要修改的唯一值是分配给 `kClientID` 作为[应用程序 ID](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id) 的值。 此值是你在本教程开头的步骤中保存的 MSAL 配置数据的一部分，该步骤用于在 Azure 门户中注册应用程序。

## <a name="for-ios-only-configure-url-schemes"></a>仅对于 iOS，配置 URL 方案

在此步骤中需注册 `CFBundleURLSchemes`，以便用户在登录后可重定向回应用。 另外，`LSApplicationQueriesSchemes` 也允许应用使用 Microsoft Authenticator。

在 Xcode 中将 `Info.plist` 作为源代码文件打开，在 `<dict>` 节中添加以下内容。 将 `[BUNDLE_ID]` 替换为在 Azure 门户中使用过的值。如果你已下载代码，则应知道该值为 `com.microsoft.identitysample.MSALiOS`。 若要创建自己的项目，请在 Xcode 中选择项目，然后打开“常规”选项卡。  此时捆绑标识符会显示在“标识”部分。 

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
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>仅对于 macOS，配置应用沙盒

1. 转到 Xcode 项目设置 >“功能”选项卡   >   “应用沙盒”
2. 选中“传出连接(客户端)”  复选框。 

## <a name="create-your-apps-ui"></a>创建应用 UI

现在，请将以下代码添加到 `ViewController` 类，以便创建一个 UI，其中包含用于调用 Microsoft Graph API 的按钮，用于注销的按钮，以及用于查看某些输出的文本视图：

### <a name="ios-ui"></a>iOS UI

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

### <a name="macos-ui"></a>macOS UI

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

接下来，还是在 `ViewController` 类中，将 `viewDidLoad()` 方法替换为：

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>使用 MSAL

### <a name="initialize-msal"></a>初始化 MSAL

将下面的 `initMSAL` 方法添加到 `ViewController` 类：

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

在 `initMSAL` 方法之后，将以下内容添加到 `ViewController` 类。

### <a name="ios-code"></a>iOS 代码：

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS 代码：

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>仅对于 iOS，处理登录回叫

打开 `AppDelegate.swift` 文件。 若要在登录后处理回叫，请在 `appDelegate` 类中添加 `MSALPublicClientApplication.handleMSALResponse`，如下所示：

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**如果使用的是 Xcode 11**，则应改为将 MSAL 回叫放入 `SceneDelegate.swift` 中。
如果支持兼容旧版 iOS 的 UISceneDelegate 和 UIApplicationDelegate，则需将 MSAL 回叫置于这两个文件中。

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>获取令牌

现在，我们可以实现应用程序的 UI 处理逻辑并通过 MSAL 以交互方式获取令牌。

MSAL 公开了获取令牌的两种主要方法：`acquireTokenSilently()` 和 `acquireTokenInteractively()`： 

- 只要有帐户，`acquireTokenSilently()` 就会尝试登录用户并获取令牌，无需任何用户交互。

- `acquireTokenInteractively()` 在尝试登录用户时始终显示 UI。 它可能会使用浏览器中的会话 Cookie 或 Microsoft Authenticator 中的帐户来提供交互式 SSO 体验。

将以下代码添加到 `ViewController` 类：

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
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

以下代码通过创建 `MSALInteractiveTokenParameters` 对象并调用 `acquireToken` 来首次获取令牌。 接下来将添加符合以下条件的代码：

1. 使用作用域创建 `MSALInteractiveTokenParameters`。
2. 使用创建的参数调用 `acquireToken()`。
3. 处理错误。 有关更多详细信息，请参阅[适用于 iOS 和 macOS 的 MSAL 错误处理指南](msal-handling-exceptions.md)。
4. 处理成功案例。

将以下代码添加到 `ViewController` 类。

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
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

若要以无提示方式获取更新的令牌，请向 `ViewController` 类添加以下代码。 它创建 `MSALSilentTokenParameters` 对象并调用 `acquireTokenSilent()`：

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

在你获得令牌后，你的应用可以在 HTTP 标头中使用它向 Microsoft Graph 发出经授权的请求：

| 标头密钥    | 值                 |
| ------------- | --------------------- |
| 授权 | 持有者 \<access-token> |

将以下代码添加到 `ViewController` 类：

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

请参阅 [Microsoft Graph API](https://graph.microsoft.com)，了解有关 Microsoft Graph API 的详细信息。

### <a name="use-msal-for-sign-out"></a>使用 MSAL 进行注销

接下来，添加注销支持。

> [!Important]
> 使用 MSAL 注销会从应用程序中删除有关用户的所有已知信息，但是用户的设备上仍然有一个活动会话。 如果用户尝试再次登录，则可能会看到登录 UI，但由于设备会话仍处于活动状态，可能无需重新输入其凭据。

若要添加注销功能，请将以下代码添加到 `ViewController` 类中。 该方法会遍历所有帐户并将其删除：

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>启用令牌缓存

默认情况下，MSAL 会在 iOS 或 macOS 密钥链中缓存应用的令牌。 

若要启用令牌缓存，请执行以下操作：
1. 确保应用程序已正确签名
2. 转到 Xcode 项目设置 >“功能”选项卡   >   “启用密钥链共享”
3. 单击 **+** 并输入以下“密钥链组”  条目：3.a 对于 iOS，输入 `com.microsoft.adalcache` 3.b 对于 macOS，输入 `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>添加帮助程序方法
将以下帮助程序方法添加到 `ViewController` 类以完成此示例。

### <a name="ios-ui"></a>iOS UI：

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

### <a name="macos-ui"></a>macOS UI：

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>多帐户应用程序

该应用针对单个帐户方案生成。 MSAL 也支持多帐户方案，但它需要应用的一些额外工作。 需要创建 UI 来帮助用户选择他们想要对每个需要令牌的操作使用的帐户。 或者，你的应用可以通过 `getAccounts()` 方法实现启发式选择要使用的帐户。

## <a name="test-your-app"></a>测试应用

### <a name="run-locally"></a>在本地运行

构建应用并将其部署到测试设备或模拟器。 你应能够登录并获取 Azure AD 或个人 Microsoft 帐户的令牌。

用户首次登录你的应用时，Microsoft 标识都将提示他们同意所请求的权限。  虽然大多数用户都能够同意，但某些 Azure AD 租户已禁用用户同意功能，这要求管理员代表所有用户同意。 要支持此方案，请在 Azure 门户中注册应用的作用域。

你登录后，此应用将显示从 Microsoft Graph `/me` 终结点返回的数据。

## <a name="get-help"></a>获取帮助

如果对本教程或 Microsoft 标识平台有疑问，请访问[帮助和支持](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)。

帮助我们改进 Microsoft 标识平台。 通过完成简短的两问题调查，告诉我们你的想法。

> [!div class="nextstepaction"]
> [Microsoft 标识平台调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
