---
title: Azure AD iOS 入门 | Microsoft Docs
description: 如何构建与 Azure AD 相集成以方便登录，并使用 OAuth 调用 Azure AD 保护 API 的 iOS 应用程序。
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: jmprieur
ms.openlocfilehash: d8472ee85cf65d8006a64e6901cf8e87d66057d7
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318205"
---
# <a name="azure-ad-ios-getting-started"></a>Azure AD iOS 入门
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

对于需要访问受保护资源的 iOS 客户端，Azure Active Directory (Azure AD) 提供了 Active Directory 身份验证库 (ADAL)。 ADAL 简化了应用用来获取访问令牌的过程。 为了演示这种简便性，我们在本文中构建了一个“目标 C 待办事项列表”应用程序，它可以：

* 使用 [OAuth 2.0 身份验证协议](https://msdn.microsoft.com/library/azure/dn645545.aspx)获取调用 Azure AD 图形 API 的访问令牌。
* 在目录中搜索具有给定别名的用户。

若要构建完整的工作应用程序，需要执行以下操作：

1. 将应用程序注册到 Azure AD。
2. 安装并配置 ADAL。
3. 使用 ADAL 从 Azure AD 获取令牌。

若要开始，请[下载应用框架](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)或[下载已完成的示例](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。 还需要一个可在其中创建用户和注册应用程序的 Azure AD 租户。 如果还没有租户，请[了解如何获取租户](active-directory-howto-tenant.md)。


> [!TIP]
> 试用新的[开发人员门户](https://identity.microsoft.com/Docs/iOS)预览版，只需花费几分钟时间，它就能帮助你开始使用 Azure AD。 开发人员门户可指导完成注册应用并将 Azure AD 集成到代码的整个过程。 完成上述过程后，会获得一个可对租户中的用户进行身份验证的简单应用程序，以及一个可以接受令牌并执行验证的后端。 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1.确定用于 iOS 的重定向 URI
若要安全地在特定 SSO 方案中启动应用程序，必须以特定格式创建重定向 URI。 重定向 URI 用于确保将令牌返回给需要它们的正确应用程序。


重定向 URI 的 iOS 格式为：

```
<app-scheme>://<bundle-id>
```

* **aap-scheme** - 已在 XCode 项目中注册。 它是其他应用程序与你联系的方式。 可以在 Info.plist -> URL types -> URL Identifier 下找到此信息。 如果尚未配置一个或多个重定向 URI，则应该创建一个。
* **bundle-id** - 这是在 XCode 项目设置中的“标识符”下找到的捆绑标识符。

此快速入门代码的一个示例：***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2.注册 DirectorySearcher 应用程序
若要设置应用来获取令牌，首先需要在 Azure AD 租户中注册该应用，并授予其访问 Azure AD 图形 API 的权限：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部栏上，单击帐户。 在“目录”列表下选择要注册应用程序的 Active Directory 租户。
3. 在最左侧的导航窗格中单击“所有服务”，并选择“Azure Active Directory”。
4. 单击“应用注册”，并选择“添加”。
5. 根据提示创建新的“本机客户端应用程序”。
  * 应用程序的**名称**向最终用户描述该应用程序。
  * **重定向 URI** 是 Azure AD 用来返回令牌响应的方案与字符串组合。 请输入特定于应用程序并基于之前的重定向 URI 信息的一个值。
6. 完成注册后，Azure AD 将为应用分配一个唯一的应用程序 ID。 在后面的部分中会用到此值，因此，请从应用程序选项卡中复制此值。
7. 在“设置”页上，选择“所需权限”，并选择“添加”。 选择“Microsoft Graph”作为 API，并在“委派的权限”下添加“读取目录数据”权限。 这会设置应用程序以在 Azure AD Graph API 中查询用户。

## <a name="3-install-and-configure-adal"></a>3.安装并配置 ADAL
将应用程序注册到 Azure AD 后，可以安装 ADAL 并编写标识相关的代码。 为了使 ADAL 能够与 Azure AD 进行通信，需要为 ADAL 提供一些有关应用注册的信息。

1. 首先，使用 CocoaPods 将 ADAL 添加到 DirectorySearcher 项目。

    ```
    $ vi Podfile
    ```
2. 将以下内容添加到 podfile：

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. 现在，使用 CocoaPods 加载 podfile。 此步骤将创建你加载的新 XCode 工作区。

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. 在快速入门项目中，打开 plist 文件 `settings.plist`。 替换节中的元素值，以反映你在 Azure 门户中输入的值。 每当使用 ADAL 时，代码都会引用这些值。
  * `tenant` 是 Azure AD 租户的域，例如 contoso.onmicrosoft.com。
  * `clientId` 是从门户复制的应用程序的客户端 ID。
  * `redirectUri` 是你在门户中注册的重定向 URL。

## <a name="4-use-adal-to-get-tokens-from-azure-ad"></a>4.使用 ADAL 从 Azure AD 获取令牌
ADAL 遵守的基本原理是，每当应用程序需要访问令牌时，它只需调用 completionBlock `+(void) getToken : `，ADAL 就会负责其余的工作。 

1. 在 `QuickStart` 项目中，打开 `GraphAPICaller.m` 并找到靠近顶部位置的 `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` 注释。 将在此处通过 CompletionBlock 传递 ADAL 与 Azure AD 通信时所需的坐标，并告诉 ADAL 如何缓存令牌。

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. 现在，我们需要使用此令牌在图形中搜索用户。 找到 `// TODO: implement SearchUsersList` 注释。 此方法将向 Azure AD Graph API 发出 GET 请求，以查询其 UPN 以给定搜索词开头的用户。 要查询 Azure AD Graph API，需要在请求的 `Authorization` 标头中包含 access_token。 这是 ADAL 的进入位置。

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```


3. 当应用通过调用 `getToken(...)` 来请求令牌时，ADAL 会尝试返回一个令牌，而不要求用户输入凭据。 如果 ADAL 确定用户需要登录以获取令牌，它会显示一个用于登录的对话框，收集用户的凭据，并在身份验证成功后返回一个令牌。 如果 ADAL 出于任何原因无法返回令牌，则会引发 `AdalException`。

> [!Note] 
> `AuthenticationResult` 对象包含一个 `tokenCacheStoreItem` 对象，后者可用于收集应用可能需要的信息。 在此快速入门中，`tokenCacheStoreItem` 用于确定身份验证是否已发生。
>
>

## <a name="5-build-and-run-the-application"></a>5.构建并运行应用程序
祝贺你！ 现在，已有了一个可正常工作的 iOS 应用程序，它可以对用户进行身份验证，使用 OAuth 2.0 安全地调用 Web API，并获取有关用户的基本信息。 如果尚未这样做，可以在租户中填充一些用户。 启动快速入门应用，并使用这些用户之一进行登录。 根据用户的 UPN 搜索其他用户。 关闭应用，然后重新启动它。 请注意，用户的会话将保持不变。

使用 ADAL 可以方便地将所有这些常见标识功能合并到应用程序中。 它会负责所有的繁琐工作，例如缓存管理、OAuth 协议支持、向用户显示用于登录的 UI，以及刷新过期令牌。 只需要真正了解一个 API 调用，即 `getToken`。

[GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip) 上提供了一个完整示例（不包含配置值）供参考。 

## <a name="next-steps"></a>后续步骤
现在，可以转到其他方案。 可能想要尝试：

* [使用 Azure AD 保护 Node.JS Web API](active-directory-devquickstarts-webapi-nodejs.md)
* 了解[如何使用 ADAL 在 iOS 上启用跨应用 SSO](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

