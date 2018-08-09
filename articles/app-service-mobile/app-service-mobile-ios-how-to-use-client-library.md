---
title: 如何使用适用于 Azure 移动应用的 iOS SDK
description: 如何使用适用于 Azure 移动应用的 iOS SDK
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
ms.openlocfilehash: 683261ce9ecaa15f5849142cd25aa9b7c77a6867
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505791"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>如何使用适用于 Azure 移动应用的 iOS 客户端库
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南介绍如何使用最新的 [Azure 移动应用 iOS SDK][1] 执行常见任务。 对于 Azure 移动应用的新手，请先完成 [Azure 移动应用快速入门]，创建后端、创建表并下载预先生成的 iOS Xcode 项目。 本指南侧重于客户端 iOS SDK。 有关用于后端的服务器端 SDK，请参阅 Server SDK 操作方法。

## <a name="reference-documentation"></a>参考文档
iOS 客户端 SDK 的参考文档位于：[Azure 移动应用 iOS 客户端参考][2]。

## <a name="supported-platforms"></a>支持的平台
对于 iOS 8.0 版本或更高版本，iOS SDK 支持 Objective-C 项目、Swift 2.2 项目和 Swift 2.3 项目。

“服务器流”身份验证使用 WebView 显示 UI。  如果设备无法显示 WebView UI，则需要产品范围以外的另一种身份验证方法。  
因此，此 SDK 不适用于手表类型或类似的受限设备。

## <a name="Setup"></a>安装与先决条件
本指南假设已创建了包含表的后端。 本指南假设该表的架构与这些教程中的表相同。 本指南还假设在代码中引用了 `MicrosoftAzureMobile.framework` 并导入了 `MicrosoftAzureMobile/MicrosoftAzureMobile.h`。

## <a name="create-client"></a>如何创建客户端
若要在项目中访问 Azure 移动应用后端，请创建 `MSClient`。 将 `AppUrl` 替换为应用 URL。 可以将 `gatewayURLString` 和 `applicationKey` 留空。 如果设置了用于身份验证的网关，请使用网关 URL 填充 `gatewayURLString`。

**Objective-C**：

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**：

```
let client = MSClient(applicationURLString: "AppUrl")
```


## <a name="table-reference"></a>如何创建表引用
若要访问或更新数据，请创建到后端表的引用。 将 `TodoItem` 替换为表名称

**Objective-C**：

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**：

```
let table = client.tableWithName("TodoItem")
```


## <a name="querying"></a>如何查询数据
若要创建数据库查询，请查询 `MSTable` 对象。 以下查询将获取 `TodoItem` 中的所有项，并记录每个项的文本。

**Objective-C**：

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**：

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>如何筛选返回的数据
可以使用许多可用选项来筛选结果。

若要使用谓词进行筛选，请使用 `NSPredicate` 和 `readWithPredicate`。 以下筛选器返回的数据只用于查找未完成的待办事项。

**Objective-C**：

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**：

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>如何使用 MSQuery
若要执行复杂查询（包括排序和分页），请使用谓词直接创建 `MSQuery` 对象：

**Objective-C**：

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**：

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` 可控制一些查询行为。

* 指定结果顺序
* 限制要返回的字段
* 限制要返回的记录数
* 指定响应中的总计数
* 指定请求中的自定义查询字符串参数
* 应用其他函数

通过对对象调用 `readWithCompletion` 来执行 `MSQuery` 查询。

## <a name="sorting"></a>如何使用 MSQuery 对数据排序
让我们先看一个示例，来了解如何对结果排序。 若要先按“文本”字段升序排列，再按“完成”降序排列，请调用 `MSQuery`，如下所示：

**Objective-C**：

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**：

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>如何使用 MSQuery 限制字段和展开查询字符串参数
若要限制在查询中返回的字段，请在 **selectFields** 属性中指定字段的名称。 此示例仅返回文本和已完成的字段：

**Objective-C**：

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**：

```
query.selectFields = ["text", "complete"]
```

若要在服务器请求中包含其他查询字符串参数（例如，某个自定义服务器端脚本要使用这些参数），请按如下所示填充 `query.parameters`：

**Objective-C**：

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**：

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>如何配置页面大小
凭借 Azure 移动应用，页面大小可以控制从后端表中一次所拉取的记录数量。 对 `pull` 数据的调用随之会根据此页面大小来批处理数据，直到没有记录可拉取为止。

可以使用 **MSPullSettings** 配置页面大小，如下所示。 默认页面大小为 50，以下示例中则改为 3。

可以根据性能需求配置不同的页面大小。 如果具有大量小型数据记录，较大的页面大小可减少服务器往返的次数。

此设置仅控制客户端上的页面大小。 如果客户端所需的页面大小大于移动应用后端支持的页面大小，则页面大小的上限为后端配置所支持的最大大小。

此设置也是数据记录的数目，而不是字节大小。

如果增加客户端页面大小，也应增加服务器上的页面大小。 请参阅[“如何：调整表分页大小”](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)中的步骤以执行此操作。

**Objective-C**：

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```


**Swift**：

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>如何插入数据
若要插入新的表行，请创建 `NSDictionary` 并调用 `table insert`。 如果启用[动态架构]，Azure 应用服务移动后端会根据 `NSDictionary` 自动生成新列。

如果未提供 `id`，后端会自动生成新的唯一 ID。 提供自己的 `id`，以使用电子邮件地址、用户名或自己的自定义值作为 ID。 提供自己的 ID 可以让联接和业务导向型数据库逻辑变得更容易。

`result` 包含插入的新项。 根据服务器逻辑，与传递给服务器的数据相比，它可能包含其他或已修改的数据。

**Objective-C**：

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**：

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>如何修改数据
若要更新现有的行，请修改项并调用 `update`：

**Objective-C**：

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**：

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

或者，提供行 ID 和更新的字段：

**Objective-C**：

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**：

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

进行更新时，至少必须设置 `id` 属性。

## <a name="deleting"></a>如何删除数据
若要删除某个项，请对该项调用 `delete`：

**Objective-C**：

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**：

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

或者，提供行 ID 来进行删除：

**Objective-C**：

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**：

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

进行删除时，至少必须设置 `id` 属性。

## <a name="customapi"></a>如何调用自定义 API
使用自定义 API 可以公开任何后端功能。 无需映射到表操作。 不仅能进一步控制消息，甚至还可以读取或设置标头，并更改响应正文格式。 若要了解如何在后端上创建自定义 API，请阅读[自定义 API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

若要调用自定义 API，请调用 `MSClient.invokeAPI`。 请求和响应内容被视为 JSON。 若要使用其他媒体类型，[请使用 `invokeAPI` 的其他重载][5]。  要发出 `GET` 请求而不是 `POST` 请求，请将参数 `HTTPMethod` 设置为 `"GET"`，将参数 `body` 设置为 `nil`（因为 GET 请求没有消息正文）。如果自定义 API 支持其他 HTTP 谓词，请相应地更改 `HTTPMethod`。

**Objective-C**：

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**：

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>如何注册推送模板以发送跨平台通知
若要注册模板，请在客户端应用中使用 **client.push registerDeviceToken** 方法传递模板。

**Objective-C**：

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**：

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

模板类型为 NSDictionary，并且可以包含以下格式的多个模板：

**Objective-C**：

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**：

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

出于安全性考虑，将去除所有请求的标记。  要将标记添加到安装或安装中的模板，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK][4]。  若要使用这些注册的模板发送通知，请参阅[通知中心 API][3]。

## <a name="errors"></a>如何处理错误
在调用 Azure 应用服务移动后端时，完成块包含 `NSError` 参数。 如果出错，此参数为非 nil 值。 在代码中，应检查此参数，并根据需要处理错误，如上面代码片段中所示。

文件 [`<WindowsAzureMobileServices/MSError.h>`][6] 定义常量 `MSErrorResponseKey`、`MSErrorRequestKey` 和 `MSErrorServerItemKey`。 获取更多与错误相关的数据：

**Objective-C**：

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**：

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

此外，文件还定义每个错误代码的常量：

**Objective-C**：

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**：

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>如何使用 Active Directory 身份验证库对用户进行身份验证
可以借助 Active Directory 身份验证库 (ADAL) 使用 Azure Active Directory 将用户登录到应用程序。 使用标识提供者 SDK 的客户端流身份验证会首选使用 `loginWithProvider:completion:` 方法。  客户端流身份验证提供更直观的 UX 风格，并允许进行其他自定义。

1. 根据[如何为 Active Directory 登录配置应用服务][7]教程的说明，为 AAD 登录配置移动应用。 请务必完成注册本机客户端应用程序的可选步骤。 对于 iOS，我们推荐重定向 URI 的格式为 `<app-scheme>://<bundle-id>`。 有关详细信息，请参阅 [ADAL iOS 快速入门][8]。
2. 使用 Cocoapods 安装 ADAL。 编辑 Podfile 以包含以下定义，将 **YOUR-PROJECT** 替换为 Xcode 项目的名称：

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   Pod：

        pod 'ADALiOS'
3. 使用终端，从包含项目的目录运行 `pod install`，并打开生成的 Xcode 工作区（而不是项目）。
4. 根据使用的语言，将以下代码添加到应用程序。 在每个应用程序中，进行以下替换：

   * 将 **INSERT-AUTHORITY-HERE** 替换为在其中预配应用程序的租户的名称。 格式应为 https://login.microsoftonline.com/contoso.onmicrosoft.com。 可以在 [Azure 门户]中从 Azure Active Directory 的域选项卡复制此值。
   * 将 **INSERT-RESOURCE-ID-HERE** 替换移动应用后端的客户端 ID。 可以在门户中“Azure Active Directory 设置”下面的“高级”选项卡获取此客户端 ID。
   * 将 **INSERT-CLIENT-ID-HERE** 替换为从本机客户端应用程序复制的客户端 ID。
   * 使用 HTTPS 方案将 **INSERT-REDIRECT-URI-HERE** 替换为站点的 */.auth/login/done* 终结点。 此值应类似于 *https://contoso.azurewebsites.net/.auth/login/done*。

**Objective-C**：

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Swift**：

    // add the following imports to your bridging header:
    //        #import <ADALiOS/ADAuthenticationContext.h>
    //        #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>如何使用 Facebook SDK for iOS 对用户进行身份验证
可以使用 Facebook SDK for iOS 以用户的身份登录到使用 Facebook 的应用程序。  使用客户端流身份验证会优先使用 `loginWithProvider:completion:` 方法。  客户端流身份验证提供更直观的 UX 风格，并允许进行其他自定义。

1. 根据[如何为 Facebook 登录配置应用服务][9] 教程的说明，为 Facebook 登录配置移动应用后端。
2. 根据 [Facebook SDK for iOS - 入门][10] 文档中的说明，安装 Facebook SDK for iOS。 不需创建应用，可以将 iOS 平台添加到现有注册。
3. Facebook 的文档包括 App Delegate 中的一些 Objective-C 代码。 如果使用 **Swift**，则可以使用以下 AppDelegate.swift 的转换：

        // Add the following import to your bridging header:
        //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }
4. 除了将 `FBSDKCoreKit.framework` 添加到项目之外，也可以以相同方式向 `FBSDKLoginKit.framework` 添加引用。
5. 根据使用的语言，将以下代码添加到应用程序。

**Objective-C**：

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {        
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**：

    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>如何使用 Twitter Fabric for iOS 对用户进行身份验证
可以使用 Fabric for iOS 将用户登录到使用 Twitter 的应用程序。 客户端流身份验证会首选使用 `loginWithProvider:completion:` 方法，因为它提供更直观的 UX 风格，并允许进行其他自定义。

1. 根据[如何为 Twitter 登录配置应用服务](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md)教程的说明，为 Twitter 登录配置移动应用后端。
2. 根据 [Facebook SDK for iOS - 入门]文档中的说明操作并设置 TwitterKit，将 Fabric 添加到项目。

   > [!NOTE]
   > 默认情况下，Fabric 会创建 Twitter 应用程序。 可以通过注册之前使用以下代码片段创建的使用者密钥和使用者机密来避免创建应用程序。    此外，还可以将提供给应用服务的使用者密钥和使用者机密值替换为 [Fabric 仪表板] 中所见的值。 如果选择此选项，请确保将回调 URL 设置为占位符值，如 `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`。
   >
   >

    如果选择使用之前创建的机密，请将以下代码添加到应用委托中：

    **Objective-C**：

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }

    **Swift**：

        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
3. 根据使用的语言，将以下代码添加到应用程序。

**Objective-C**：

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**：

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>如何使用 Google Sign-In SDK for iOS 对用户进行身份验证
可以使用 Google Sign-In SDK for iOS 将用户登录到使用 Google 帐户的应用程序。  Google 最近已宣布要对 OAuth 安全策略进行更改。  将来这些策略更改将要求使用 Google SDK。

1. 根据[如何为 Google 登录配置应用服务](../app-service/app-service-mobile-how-to-configure-google-authentication.md)教程的说明，为 Google 登录配置移动应用后端。
2. 按照 [Google Sign-In for iOS - 开始集成](https://developers.google.com/identity/sign-in/ios/start-integrating)文档中的说明，安装 Google SDK for iOS。 可以跳过“使用后端服务器进行身份验证”部分。
3. 根据使用的语言，将以下内容添加到委派的 `signIn:didSignInForUser:withError:` 方法。

**Objective-C**：

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };

        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**：

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

1. 请确保也将以下内容添加到应用委托中的 `application:didFinishLaunchingWithOptions:`，将“SERVER_CLIENT_ID”替换为在步骤 1 中用于配置应用服务的同一 ID。

**Objective-C**：

         [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";

 **Swift**：

        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"


1. 根据使用的语言，将以下代码添加到 UIViewController 中实现 `GIDSignInUIDelegate` 协议的应用程序。  再次登录之前需要注销，虽然不需要再次输入凭据，但还是会看到同意对话框。  仅当会话令牌过期时调用此方法。

   **Objective-C**：

       #import <Google/SignIn.h>
       // ...
       - (void)authenticate
       {
               [GIDSignIn sharedInstance].uiDelegate = self;
               [[GIDSignIn sharedInstance] signOut];
               [[GIDSignIn sharedInstance] signIn];
        }

   **Swift**：

       // ...
       func authenticate() {
           GIDSignIn.sharedInstance().uiDelegate = self
           GIDSignIn.sharedInstance().signOut()
           GIDSignIn.sharedInstance().signIn()
       }

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure 移动应用快速入门]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure 门户]: https://portal.azure.com/
[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[动态架构]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Fabric 仪表板]: https://www.fabric.io/home
[Facebook SDK for iOS - 入门]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
