---
title: Azure Active Directory B2C：使用第三方库从 iOS 应用程序调用 Web API | Microsoft Docs
description: 本文说明如何创建一个 iOS“待办事项列表”应用，该应用可以使用第三方库和 OAuth 2.0 持有者令牌调用 Node.js Web API
services: active-directory-b2c
documentationcenter: ios
author: brandwe
manager: mbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: objectivec
ms.topic: hero-article
ms.date: 07/26/2016
ms.author: brandwe

---
# Azure AD B2C：使用第三方库从 iOS 应用程序调用 Web API
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Microsoft 标识平台使用开放式标准，例如 OAuth2 和 OpenID Connect。这样，开发人员便利用他们想要与服务集成的任何库。为了帮助开发人员将平台与其他库结合使用，我们编写了多份演练（例如本演练），演示如何配置第三方库，使其连接到 Microsoft 标识平台。大部分实施 [RFC6749 OAuth2 规范](https://tools.ietf.org/html/rfc6749)的库都可连接到 Microsoft 标识平台。

对于 OAuth2 或 OpenID Connect 的新手，该示例配置中的大部分内容可能较难理解。建议查看[此处所述的简要协议概述](active-directory-b2c-reference-protocols.md)。

> [!NOTE]
> 平台中有些功能（例如条件访问和 Intune 策略管理）采用这些标准中的表达式，因此要求使用开放源代码 Microsoft Azure 标识库。
> 
> 

B2C 平台不一定支持所有 Azure Active Directory 方案和功能。若要确定是否应使用 B2C 平台，请阅读 [B2C limitations](active-directory-b2c-limitations.md)（B2C 限制）。

## 获取 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。目录是所有用户、应用、组等对象的容器。如果没有容器，请先[创建 B2C 目录](active-directory-b2c-get-started.md)，然后再继续。

## 创建应用程序
接下来，需要在 B2C 目录中创建应用。此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。在本例中，由于应用与 Web API 构成一个逻辑应用，因此由单个**应用程序 ID** 表示。若要创建应用，请遵循[这些说明](active-directory-b2c-app-registration.md)。请务必：

* 在应用程序中包含**移动设备**。
* 复制分配给应用的**应用程序 ID**。稍后也需要用到此信息。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 创建策略
在 Azure AD B2C 中，每个用户体验由[策略](active-directory-b2c-reference-policies.md)定义。此应用包含一个标识体验：合并的登录和注册。需要按照[策略参考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)中所述，为每个类型创建此策略。创建策略时，请务必：

* 在策略中选择“显示名称”和注册属性。
* 在每个策略中，选择“显示名称”和“对象 ID”应用程序声明。也可以选择其他声明。
* 创建每个策略后，请复制策略的**名称**。其前缀应为 `b2c_1_`。稍后需要用到策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

创建策略后，可以开始构建应用。

## 下载代码
本教程的代码[在 GitHub 上](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)维护。若要遵循该代码，可以[下载 .zip 格式的应用](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c) (/archive/master.zip) 或克隆该应用：

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

也可以下载完成的代码并立即开始着手使用：

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## 下载第三方库 nxoauth2 并启动工作区
在本演练中，将使用 GitHub 中的 OAuth2Client，这是适用于 Mac OS X 和 iOS 的 OAuth2 库（Cocoa 和 Cocoa Touch）。此库以 OAuth2 规范的第 10 版草稿为基础。它将实现本机应用程序配置文件，并支持最终用户授权终结点。需要上述各项才能与 Microsoft 标识平台集成。

### 使用 CocoaPods 将库添加到项目
CocoaPods 是 Xcode 项目的依赖关系管理器。它会自动管理上述安装步骤。

```
$ vi Podfile
```
将以下内容添加到 podfile：

```
 platform :ios, '8.0'

 target 'SampleforB2C' do

 pod 'NXOAuth2Client'

 end
```

现在，请使用 cocoapods 加载 podfile。这会创建你要加载的新 XCode 工作区。

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## 项目结构
已在骨干中为项目设置以下结构：

* 具有任务窗格的“主视图”
* 用于添加有关所选任务的数据的“添加任务视图”
* 可让用户登录应用的“登录视图”。

我们将转到项目中的各个文件来添加身份验证。代码的其他部分（如可视代码）虽然与标识无关，但也已提供。

## 创建应用程序的 `settings.plist` 文件
如果有一个集中位置可以放置配置值，则就很容易配置应用程序。这也可帮助理解每项设置在应用程序中的作用。我们利用“属性列表”将这些值提供给应用程序。

* 在应用程序工作区中创建/打开 `Supporting Files` 下面的 `settings.plist` 文件
* 输入以下值（稍后将逐一详细说明）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

让我们详细了解这些值。

对于 `authURL`、`loginURL`、`bhh` 和 `tokenURL`，需要填入租户名称。这是分配的 B2C 租户名称。例如 `kidventusb2c.onmicrosoft.com`。如果使用开放源代码 Microsoft Azure 标识库，将使用元数据终结点提取此数据。我们已努力完成为你提取这些值的工作。

有关 B2C 租户名称的详细信息，请参阅 [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)

`keychain` 值是一个容器，NXOAuth2Client 库将用来创建密钥链以存储你的令牌。如果想要跨多个应用实现 SSO，可以在每个应用程序中指定相同的密钥链，并请求在 XCode 权利中使用该密钥链。Apple 文档中对此做了说明。

每个 URL 末尾的 `<policy name>` 是放置以前创建的策略的位置。应用程序根据流程调用这些策略。

`taskAPI` 是 REST 终结点，将使用 B2C 令牌调用它，以便添加任务或查询现有任务。此终结点已专门针对本示例进行设置，不再需要更改即可正常运行。

需要其余的值才能使用此库，才能将值应用于上下文。

现已创建 `settings.plist` 文件，我们需要代码来读取它。

## 将 AppData 类设置为读取我们的设置
让我们创建一个简单的文件，它只分析前面创建的 `settngs.plist` 文件，并且让这些设置可在将来用于任何类。由于我们不想在每次类别请求时创建一份新数据，因此使用单一实例模式，并且只返回每次对设置发出请求时创建的同一实例

* 创建 `AppData.h` 文件：

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* 创建 `AppData.m` 文件：

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

现在只要在任何类中调用 `  AppData *data = [AppData getInstance];`（如下所示），即可轻松获取数据。

## 在 AppDelegate 中设置 NXOAuth2Client 库
NXOAuthClient 库要求设置一些值。完成后，可以使用获取的令牌来调用 REST API。由于我们知道在加载应用程序时随时调用 `AppDelegate`，因此将配置值放入该文件是很合理的。

* 打开 `AppDelegate.m` 文件
* 导入稍后要使用的一些标头文件。

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* 在 AppDelegate 中添加 `setupOAuth2AccountStore` 方法

需要创建 AccountStore，然后将刚刚从 `settings.plist` 文件读入的数据馈送给它。

此时应注意一些有关 B2C 服务的事项，使此代码更容易了解：

1. Azure AD B2C 使用查询参数提供的*策略*来为请求提供服务。这样可以让 Azure Active Directory 仅充当应用程序的独立服务。为了提供这些额外的查询参数，必须结合自定义策略参数提供 `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` 方法。
2. Azure AD B2C 使用范围的方式非常类似于其他 OAuth2 服务器。但是，由于使用 B2C 的关键在于验证用户与访问资源，因此绝对需要某些范围才能让流程正确运行。这是 `openid` 范围。Microsoft 标识 SDK 自动提供 `openid` 范围，因此 SDK 配置中不会显示该范围。但是，由于我们使用第三方库，因此需要指定此范围。

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
接下来，请务必在 AppDelegate 中的 `didFinishLaunchingWithOptions:` 方法下调用该范围。

```
[self setupOAuth2AccountStore];
```


## 创建用于处理身份验证请求的 `LoginViewController` 类
使用 Web 视图进行帐户登录。这样，便可以提示用户提供其他因素（例如已配置的短信），或者将错误消息返回给用户。将在此处设置 Web 视图，然后编写代码，以便从 Microsoft 标识服务处理 Web 视图中发生的回调。

* 创建 `LoginViewController.h` 类

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

将创建以下每个方法。

> [!NOTE]
> 确保将 `loginView` 绑定到脚本中的实际 Web 视图。否则，在身份验证时不会弹出 Web 视图。
> 
> 

* 创建 `LoginViewController.m` 类
* 添加一些变量以便在身份验证时传递状态

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* 重写 Web 视图方法以处理身份验证

当用户需要按如上所述登录时，我们需要让 Web 视图知道所需的行为。只需剪切并粘贴以下代码。

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* 编写代码以处理 OAuth2 请求的结果

需要使用代码处理 Web 视图返回的重定向 URL。如果不成功，则再试一次。同时，库会提供错误，以便可以在控制台中查看或以异步方式处理。

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* 设置通知工厂。

像在上面 `AppDelegate` 中一样创建相同的方法，但这次添加一些 `NSNotification` 来告知服务要发生什么情况。设置观察器，它会在令牌发生任何更改时发出通知。获取令牌后，将用户返回到 `masterView`。

```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* 添加代码以便在针对 sign-native 启动请求时处理用户

创建每当发出身份验证请求时要调用的方法。这是实际创建 Web 视图的方法

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* 最后，在每次加载 `LoginViewController` 时调用上面编写的所有方法。做法是将这些方法添加到 Apple 提供的 `viewDidLoad` 方法

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

现已建立与应用程序交互以便登录的主要方式。登录后需要使用收到的令牌。因此，要创建一些调用 REST API 的帮助器代码，以便使用此库。

## 创建 `GraphAPICaller` 类来处理对 REST API 的请求
每次加载应用时将加载一个配置。获取令牌后，现在需要对它执行一些操作。

* 创建 `GraphAPICaller.h` 文件

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

从此代码中可以看到，将要创建两个方法：一个从 API 获取任务，另一个将任务添加到 API。

现已设置好接口，让我们添加实际的实现：

* 创建 `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## 运行示例应用
最后，在 Xcode 中生成并运行应用。注册或登录应用，并为登录的用户创建任务。注销后，以不同的用户身份重新登录，然后为该用户创建任务。

请注意，这些任务按用户存储在 API 中，因为 API 从它收到的访问令牌中提取用户的标识。

## 后续步骤
现在，可以转到更高级的 B2C 主题。可以尝试：

[Call a Node.js web API from a Node.js web app]()（从 Node.js Web 应用调用 Node.js Web API）

[Customize the UX for a B2C app]()（自定义 B2C 应用的 UX）

<!---HONumber=AcomDC_0921_2016-->