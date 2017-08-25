---
title: "Azure AD v2 iOS 入门 - 用途 | Microsoft Docs"
description: "iOS (Swift) 应用程序如何通过 Azure Active Directory v2 终结点调用需访问令牌的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 81d5ae3775cca7cea5abf1922a25be0663a8a949
ms.contentlocale: zh-cn

---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft 身份验证库 (MSAL) 获取用于 Microsoft Graph API 的令牌

打开 `ViewController.swift`，并用以下内容替换代码：

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current logged in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive signin.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign-in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>更多信息
#### <a name="getting-a-user-token-interactively"></a>以交互方式获取用户令牌
调用 `acquireToken` 方法将出现提示用户登录的浏览器窗口。 用户首次访问受保护的资源或获取令牌的静默操作失败（如用户密码过期）时，应用程序通常会要求用户以交互方式登录。

#### <a name="getting-a-user-token-silently"></a>以静默方式获取用户令牌
`acquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `acquireToken` 后，通常使用 `acquireTokenSilent` 方法获取用于访问受保护资源的令牌，以便进行后续调用 - 因为调用请求或续订令牌都以静默方式进行。

`acquireTokenSilent` 最终将失败（例如，用户已注销，或已在另一台设备上更改了密码）。 MSAL 检测到可以通过请求交互式操作解决问题时，它将引发 `MSALErrorCode.interactionRequired` 异常。 应用程序可以通过两种方式处理此异常：

1.  立即调用 `acquireToken`，随后出现用户登录提示。 此模式通常用于联机应用程序，此时应用程序中没有可供用户使用的脱机内容。 此指导式设置生成的示例应用程序使用此模式：可在第一次执行应用程序时看到其正在运行。 由于没有用户使用过该应用程序，`applicationContext.users().first` 将包含一个 null 值，并且引发 ` MSALErrorCode.interactionRequired ` 异常。 此示例中的代码随后处理此异常，方法是通过调用 `acquireToken` 使其显示用户登录提示。

2.  应用程序还可以直观地提示用户以交互方式登录，用户可以选择在合适的时间登录，或者应用程序可以稍后重试 `acquireTokenSilent`。 如果用户可以在不中断应用程序的情况下（例如，应用程序中有可用的脱机内容）使用应用程序的其他功能，则通常会使用此方法。 在这种情况下，用户可以决定何时登录并访问受保护的资源，或何时刷新过期信息，或在网络暂时不可用得到还原后，应用程序可以决定重试 `acquireTokenSilent`。

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用刚获得的令牌调用 Microsoft Graph API

将以下新方法添加到 `ViewController.swift`。 该方法用于使用 HTTP 授权标头对 Microsoft Graph API 执行 `GET` 请求：

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>对受保护 API 进行 REST 调用

在此示例应用程序中，使用 `getContentWithToken()` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求，然后将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 此示例中的资源是 Microsoft Graph API me 终结点 - 显示用户个人资料信息。
<!--end-collapse-->

## <a name="set-up-sign-out"></a>设置注销

将以下方法添加到 `ViewController.swift` 可注销用户：

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>注销详细信息

`signoutButton` 方法删除 MSAL 用户缓存中的用户 - 这会有效地告知 MSAL 忘记当前用户，以便将来请求获取令牌仅在交互模式下取得成功。

此示例中的应用程序支持单个用户，但 MSAL 也支持可同时注册多个帐户的情况（例如，用户可在电子邮件应用程序包含多个帐户）。
<!--end-collapse-->

## <a name="register-the-callback"></a>注册回叫

用户进行身份验证后，浏览器将用户重定向回应用程序。 按照下列步骤以注册此回叫：

1.  打开 `AppDelegate.swift` 并导入 MSAL：

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
将以下方法添加到 <code>AppDelegate</code> 类以处理回叫：
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```


