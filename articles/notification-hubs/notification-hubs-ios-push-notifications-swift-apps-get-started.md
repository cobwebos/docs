---
title: 将通知推送到 Swift iOS 应用使用 Azure 通知中心 |Microsoft Docs
description: 了解如何将推送通知到 Swift iOS 应用程序使用 Azure 通知中心。
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994761"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>教程：推送通知到 Swift iOS 应用程序使用通知中心 REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

将通知推送到基于 Swift 的 iOS 应用程序使用在本教程中，使用 Azure 通知中心[REST API](/rest/api/notificationhubs/)。 你将创建一个空白 iOS 应用，它使用 [Apple Push Notification 服务 (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) 接收推送通知。

在本教程中，你将执行以下步骤：

> [!div class="checklist"]
> * 生成证书签名请求文件
> * 请求你的应用推送通知
> * 为应用程序创建配置文件
> * 创建通知中心
> * 使用 APNS 信息配置通知中心
> * 将 iOS 应用连接到通知中心
> * 测试解决方案

## <a name="prerequisites"></a>必备组件
若要按照文中内容操作，需要：

- 请通读[Azure 通知中心概述](notification-hubs-push-notification-overview.md)如果您不熟悉该服务。 
- 了解有关注册和安装：[注册管理](notification-hubs-push-notification-registration-management.md)
- 活动[Apple 开发人员帐户](https://developer.apple.com) 
- 与 Xcode 以及有效的开发人员证书安装到密钥链 Mac
- 可以运行和调试使用的物理 iPhone 设备 （不可能使用模拟器测试推送通知）
- 在中注册你的物理 iPhone 设备[Apple 门户](https://developer.apple.com)和与证书相关联
- [Azure 订阅](https://portal.azure.com)可以在其中创建和管理资源

它应为可能要跟着介绍一起操作来创建以前没有此第一个原则示例。 但是，熟悉以下概念将很有用：

- 生成了 Xcode 和 Swift iOS 应用
- 配置[Azure 通知中心](notification-hubs-ios-apple-push-notification-apns-get-started.md)适用于 iOS
- 熟悉[Apple 开发人员门户](https://developer.apple.com)和[Azure 门户](https://portal.azure.com)

> [!NOTE]
> 通知中心将配置为使用*沙盒*仅身份验证模式。 对于生产工作负荷，你不应使用此身份验证模式。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>将 iOS 应用连接到通知中心
在本部分中，将构建 iOS 应用，将连接到通知中心。  

### <a name="create-an-ios-project"></a>创建 iOS 项目
1. 在中**Xcode**，创建新的 iOS 项目并选择**单视图应用程序**模板。
2. 设置新的项目选项时，请执行以下步骤：
    1. 使用相同**产品名称**(即**PushDemo**) 和**组织标识符**(即`com.<organization>`) 时使用**捆绑包标识符**在中设置了**Apple 开发人员门户**。 
    2. 选择**团队**的**应用程序 ID**已设置了。
    3. 设置**语言**到**Swift**。
    4. 点击“下一步”
3. 创建一个名为的新文件夹**SupportingFiles**。
4. 创建一个新**plist**名为的文件**devsettings.plist**下**SupportingFiles**文件夹。 请确保添加到此文件夹应用**gitignore**文件，这样使用时，它不提交**git 存储库**。 在生产应用中，您将很可能是有条件地设置这些机密作为自动的生成过程的一部分。 但是，它不包含本演练的一部分。
5. 更新**devsettings.plist**包括以下配置条目 (使用你自己的值从**通知中心**你预配):

   | 密钥                            | Type                     | 值                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   可以通过导航到查找必要的值**通知中心**中的资源**Azure 门户**。 您可以找到**notificationHubName**并**notificationHubNamespace**右上角的值**Essentials**摘要中**概述**页。

   ![通知中心 Essentials 摘要](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   您可以找到**notificationHubKeyName**并**notificationHubKey**通过导航到的值**访问策略**，单击相应**访问策略**。 例如，`DefaultFullSharedAccessSignature`。 然后将从复制**主连接字符串**值带有前缀`SharedAccessKeyName=`有关`notificationHubKeyName`且值带有前缀`SharedAccessKey=`为`notificationHubKey`。 连接字符串应采用以下格式：
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   为了简单起见，将使用*DefaultFullSharedAccessSignature*因此可以使用该令牌将发送通知以及。 但是，在实践中`DefaultListenSharedAccessSignature`会更好的选择的情况下您只想要接收通知。       
6. 下**项目导航器**，单击**项目名称**，然后单击**常规**选项卡
7. 查找**标识**，然后设置**捆绑包标识符**值使其匹配用于**应用程序 ID** （来自前面的步骤），即 `'com.<organization>.PushDemo'`
8. 查找**签名**，然后确保选择相应**Team**为您**Apple 开发人员帐户**（即在其下创建证书和配置文件之前的版本）。  **Xcode**应自动拉取相应**预配配置文件**基于**捆绑包标识符**。 如果看不到新**预配配置文件**，请尝试刷新的配置文件**签名标识**(*Xcode > 首选项 > 帐户 > 查看详细信息*)。 单击**签名标识**，然后单击**刷新**右下角中的按钮应下载配置文件。
9. 选择**功能**选项卡并确保**推送通知**已启用。
10. 打开你**AppDelegate.swift**文件，实现*UNUserNotificationCenterDelegate*协议并将以下代码添加到类的顶部：
    
    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
        
        ...
    }
    ```

    稍后会用这些成员。 *标记*并*genericTemplate*将用作在注册过程。 有关标记的详细信息，请参阅[注册标记](notification-hubs-tags-segment-push-message.md)并[模板注册](notification-hubs-templates-cross-platform-push-messages.md)。
 
11. 在同一文件中，添加下面的代码*didFinishLaunchingWithOptions*函数：

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }
        
    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in
                
            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    此代码检索中的设置值**devsettings.plist**，设置**AppDelegate**声明为类*UNUserNotificationCenter*请求授权委托为推送通知，然后调用*registerForRemoteNotifications*。
    
    为了简单起见，该代码支持**iOS 10 及更高版本仅**。 通常情况下将进行有条件地使用相应的 Api 和方法，可以添加对较旧操作系统版本的支持。

12. 在同一文件中，添加以下函数：

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    该代码使用*installationId*并*pushChannel*值，以向注册**通知中心**。 在这种情况下，使用*UIDevice.current.identifierForVendor*向我们提供唯一的值标识设备，然后设置格式*deviceToken*向我们提供所需*pushChannel*值。 *ShowAlert*函数是直接显示用于演示一些消息文本。

13. 仍在**AppDelegate.swift**，添加*willPresent*并*didReceive* **UNUserNotificationCenterDelegate**到函数通知到达时分别在前台和后台中运行应用时显示警报
    
    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }
    
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```    

14. 将打印语句添加到底部*didRegisterForRemoteNotificationsWithDeviceToken*函数来验证是否*installationId*并*pushChannel*正在分配的值
15. 创建文件夹 (**模型**， **Services**，并**实用程序**) 您将更高版本添加到项目的基础组件
16. 检查该项目生成，并且在物理设备 （推送通知不能使用模拟器进行测试） 上运行

### <a name="create-models"></a>创建模型
在此步骤中，您将创建的模型来表示一组[通知中心 REST API](/rest/api/notificationhubs/)有效负载并将其存储所需**SAS 令牌**数据。


1.  添加到新的 swift 文件**模型**称为**PushTemplate.swift**。 此模型提供了一个结构，表示**正文**的个别模板作为的一部分**DeviceInstallation**有效负载：
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. 添加到新的 swift 文件**模型**名为文件夹**DeviceInstallation.swift**。 此文件定义结构表示用于创建或更新的有效负载**设备安装**。 将以下代码添加到该文件：
    
    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>
    
        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

3.  添加新的 swift 文件下**模型**称为**TokenData.swift**。 此模型将用于存储**SAS 令牌**以及过期日期

    ```swift
    import Foundation

    struct TokenData {
    
        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>生成的 SAS 令牌
**通知中心**使用相同的安全基础结构**Azure 服务总线**。 若要调用 REST API，你将需要[以编程方式生成 SAS 令牌](/rest/api/eventhub/generate-sas-token)，可在**授权**的请求标头。  

生成的令牌将采用以下格式： 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

过程本身涉及相同的六个主要步骤：  

1.  计算在到期[UNIX epoch 起经过的时间](https://en.wikipedia.org/wiki/Unix_time)格式 （00:00:00 UTC 1970 年 1 月 1 以来经过秒）
2.  格式设置**ResourceUrl** (表示您正在尝试访问，也就是说，资源`'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`) 因此百分比编码形式和小写
3.  正在准备**StringToSign**，这是组成 `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  计算 （和基 64 编码）**签名**使用**HMAC-SHA256**的**StringToSign**值替换**密钥**的一部分**连接字符串**(为相应**授权规则**)
5.  格式设置 base 64 编码**签名**因此百分比编码
6.  构造**令牌**在预期的格式中使用**UrlEncodedSignature**， **ExpiryEpoch**， **KeyName**，和**UrlEncodedResourceUrl**值

请参阅[Azure 服务总线文档](../service-bus-messaging/service-bus-sas.md)有关的更全面概述**共享访问签名**和使用的方式**Azure 服务总线**和**通知中心**。

出于此 Swift 示例中，您将使用 Apple 的开放源代码**CommonCrypto**库来协助对签名的哈希处理。 由于它是 C 库，它不是在 Swift--现成的可访问。 但是，您可以使其可以使用桥接标头。 若要添加和配置桥接标头：

1. 在中**Xcode**，请转到 **文件**，然后**新建**，然后**文件** ，然后选择**标头文件**其命名为*BridgingHeader.h*
2. 编辑文件以导入**CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. 更新的目标**生成设置**引用桥接标头。 打开 **构建设置** 选项卡并向下滚动到 **Swift 编译器** 部分。 絋粄 **安装 Objective C 的兼容性标头** 选项设置为 **是**，并输入到我们桥接标头文件路径**OBJECTIVE-C 桥接标头** 选项，它是`'\<ProjectName\>/BridgingHeader.h'`。 如果找不到这些选项，请确保您具有**所有**选定视图 (而非**基本**或**自定义**)。
    
   有许多第三方开放源代码包装库，这可能会使使用**CommonCrypto**更容易。 它不在本文的范围。

4. 添加新的 Swift 文件下**实用工具**名为的文件夹**TokenUtility.swift**并添加以下代码：

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>
    
        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)
        
            return tokenData
        }
    
        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)
        
            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()
        
            return result as Data
        }
    
        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)
        
            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }
        
            for i in 0..<length {
                let currentChar = sourceUtf8![i]
            
                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }
        
            return encodedString
        }
    }
   ```
    
    此实用程序封装负责生成逻辑**SAS 令牌**。 *GetSasToken*函数协调准备所需标记，与前面，所述的高级步骤，并将在本教程后面的步骤中安装服务调用。 其他两个函数调用*getSasToken 函数*;*sha256HMac*用于计算签名并*urlEncodedString*编码各自的 Url 字符串。 *UrlEncodedString*函数是必需的因为这不是可能实现所需的输出，使用内置*addingPercentEncoding*函数。 [Azure 存储空间 iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)担任如何着手在 Objective-c。 尽管这些操作的极好示例 在详细信息**Azure 服务总线 SAS 令牌**中可以找到[Azure 服务总线文档](../service-bus-messaging/service-bus-sas.md)。 

### <a name="verify-the-sas-token"></a>验证 SAS 令牌
在客户端中实现安装服务之前，你可以检查我们的应用程序正确生成**SAS 令牌**使用所选的 http 实用程序。 对于这篇文章的目的，我们的工具的选择将**Postman**。

记下*installationId*并*令牌*生成的应用程序使用适当放置值打印语句或断点。 

请按照下列步骤来调用*安装*API:

1. 在中**Postman**，打开新选项卡
2. 将请求设置为**获取**和以下地址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. 配置请求标头，如下所示：
    
   | 密钥           | 值            |
   | ------------- | ---------------- |
   | 内容类型  | application/json |
   | 授权 | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. 单击**代码**按钮 (右上方下**保存**按钮)。 请求应类似于下面的示例所示：

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. 单击**发送**按钮

指定不存在任何注册*installationId*此时，但是它应导致**找不到 404**响应而非**401 未授权**。 此结果应确认**SAS 令牌**已被接受。

### <a name="implement-the-installation-service-class"></a>实现安装服务类
接下来你将实现我们基本包装[安装 REST API](/rest/api/notificationhubs/create-overwrite-installation)。  

添加新的 Swift 文件下**Services**名为的文件夹**NotificationRegistrationService.swift**，然后将以下代码添加到此文件：

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```
 
作为初始化的一部分提供必要的详细信息。 （可选） 传递到标记和模板*注册*函数的窗体一部分**设备安装**JSON 有效负载。  

*注册*函数会调用其他私有函数来准备证书请求。 一旦收到响应，完成调用，该值指示是否注册是成功还是失败。  

通过构造请求终结点*getBaseAddress*函数使用**通知中心**参数**命名空间**和**名称**在初始化过程中提供。  

*GetSasToken*函数将检查当前存储的令牌是否有效，否则它将调用**TokenUtility**生成新令牌并将其存储在返回值之前。 

最后*encodeToJson*将对象转换为相应的模型 JSON 请求正文的一部分使用。

### <a name="invoke-the-notification-hubs-rest-api"></a>调用通知中心 REST API
最后一步是更新**AppDelegate**若要使用**NotifiationRegistrationService**注册我们**NotificationHub**。 

1. 打开**AppDelegate.swift**并添加类级别变量来存储对引用**NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. 在同一文件中，更新*didRegisterForRemoteNotificationsWithDeviceToken*函数来初始化**NotificationRegistrationService**使用必要的参数，然后调用*注册*函数。

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    为了简单起见，您将使用几个 print 语句的结果与更新输出窗口*注册*操作。 

3. 现在生成并运行应用程序 （在上的物理设备）。 应会看到 **"注册"** 输出窗口中。

## <a name="test-the-solution"></a>测试解决方案
在此阶段，我们的应用程序注册与**NotificationHub** ，并且它可以接收推送通知。 在中**Xcode**、 停止调试程序并关闭应用程序 （如果当前正在运行）。 接下来，您将检查**设备安装**按预期方式的详细信息和我们的应用程序现在确实可以接收推送通知。  

### <a name="verify-the-device-installation"></a>验证设备安装
和前面使用操作一样，现在可以通过相同的请求**Postman**有关[验证 SAS 令牌](#verify-the-sas-token)。 假设**SAS 令牌**尚未过期，响应中应该现在包含如模板和标记提供的安装详细信息。  

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>发送测试通知 （Azure 门户）
测试，现在可以接收通知的最快方法是导航到**通知中心**中**Azure 门户**。

1. 在中**Azure 门户**，导航到**概述**选项卡上将**通知中心**
2. 单击**测试发送**（左上方） 上面**Essentials**摘要

    ![通知中心 Essentials 摘要测试发送按钮](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. 选择**自定义模板**从列表中的**平台**
4. 输入**12345**有关**发送到标记表达式**（必须在安装中指定此标记）
5. （可选） 编辑**消息**JSON 有效负载中
    
    ![通知中心测试发送](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. 单击**发送**，门户应指示是否通知发送到该设备已成功

    ![通知中心测试发送结果](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    您还会看到通知**通知中心**（假设该应用程序不在前台运行） 在设备上。 点击该通知应打开应用，并显示警报。

    ![收到通知的示例](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>发送测试通知 (Postman)
您可以通过发送通知的各自[REST API](/rest/api/notificationhubs/)通过**Postman** ，并且它可能会更方便的方式来测试。 

1. 在中**Postman**，打开新选项卡
2. 将请求设置为**POST**并输入以下地址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. 配置请求标头，如下所示：
    
   | 密钥                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | 内容类型                   | application/json;charset=utf-8 |
   | 授权                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | 模板                       |
   | 标记                           | "12345"                        |

4. 配置请求**正文**若要使用**RAW-JSON (application.json)** 使用以下 JSON 有效负载：

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. 单击**代码**按钮 (右上方下**保存**按钮)。 请求应类似于下面的示例所示：

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

5. 单击**发送**按钮

应获取成功状态代码，并接收有关客户端设备的通知。

## <a name="next-steps"></a>后续步骤
现可连接到的基本的 iOS Swift 应用**通知中心**通过[REST API](/rest/api/notificationhubs/)和可以发送和接收通知。 有关详细信息，请参阅以下文章： 

- [Azure 通知中心概述](notification-hubs-push-notification-overview.md)
- [通知中心 REST API](/rest/api/notificationhubs/)
- [后端操作的的通知中心 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [通知中心 GitHub 上的 SDK](https://github.com/Azure/azure-notificationhubs)
- [向应用程序后端注册](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [注册管理](notification-hubs-push-notification-registration-management.md)
- [使用标记](notification-hubs-tags-segment-push-message.md) 
- [使用自定义模板](notification-hubs-templates-cross-platform-push-messages.md)
- [使用共享访问签名进行服务总线访问控制](../service-bus-messaging/service-bus-sas.md)
- [以编程方式生成 SAS 令牌](/rest/api/eventhub/generate-sas-token)
- [Apple 安全： 通用加密](https://developer.apple.com/security/)
- [UNIX epoch 起经过的时间](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
