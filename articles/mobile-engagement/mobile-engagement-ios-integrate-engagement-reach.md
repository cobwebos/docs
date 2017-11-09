---
title: "Azure Mobile Engagement iOS SDK Reach 集成 | Microsoft Docs"
description: "Azure Mobile Engagement 的 iOS SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ba74e0c442ac10f096d465f989e03d2ceae8cd88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>如何在 iOS 上集成 Engagement Reach
在遵循本指南操作之前，必须先按照[如何在 iOS 上集成 Engagement 文档](mobile-engagement-ios-integrate-engagement.md)中所述的集成步骤操作。

本文档要求使用 XCode 8。 如果确实需要使用 XCode 7，则可以使用 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)。 此早期版本在 iOS 10 设备上运行时存在一个已知 bug：无法操作系统通知。 要修复此问题，必须在应用委派中实现弃用的 API `application:didReceiveRemoteNotification:`，如下所示：

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **我们不建议此解决方法**，因为此 iOS API 已被否决，此行为在任何即将发布的（即使再小）iOS 版本升级过程中会有所更改。 你应尽快改用 XCode 8。
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>使应用程序能够接收无提示的推送通知
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>集成步骤
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>将 Engagement Reach SDK 嵌入 iOS 项目
* 在 Xcode 项目中添加 Reach SDK。 在 Xcode 中，转到“**项目 \> 添加到项目**”，并选择 `EngagementReach` 文件夹。

### <a name="modify-your-application-delegate"></a>修改应用程序代理
* 在实现文件的顶部，导入 Engagement Reach 模块：

      [...]
      #import "AEReachModule.h"
* 在 `applicationDidFinishLaunching:` 或 `application:didFinishLaunchingWithOptions:` 方法中，创建 Reach 模块，并将其添加到现有 Engagement 初始化行中：

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* 将 **'icon.png'** 字符串修改为希望用作通知图标的图像名称。
* 如果要在市场宣传活动中使用“*更新锁屏提醒值*”选项，或要使用原生推送 \</SaaS/Reach API/Campaign format/Native Push\> 市场活动，则必须让 Reach 模块自行管理锁屏提醒图标（它会在每次应用程序启动或前置时自动清除应用程序锁屏提醒，并重设由 Engagement 存储的值）。 具体是通过在 Reach 模块初始化代码后添加以下代码行来实现：

      [reach setAutoBadgeEnabled:YES];
* 如果想处理市场宣传数据推送，则必须使应用程序委托符合 `AEReachDataPushDelegate` 协议。 Reach 模块初始化后请添加以下行：

      [reach setDataPushDelegate:self];
* 然后便可以在应用程序委派中实现 `onDataPushStringReceived:` 和 `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` 方法：

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>类别
创建数据推送活动时，类别参数是可选的，并允许筛选数据推送。 如果想推送不同种类的 `Base64` 数据，并希望在解析这些数据之前确定其类型，那么这种做法很有用。

**现在应用程序已准备好接收和显示市场宣传内容！**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>如何随时接收公告和投票
通过使用 Apple Push Notification 服务，Engagement 可随时会市场宣传通知发送给最终用户。

要启用此功能，必须准备好应用程序以使用 Apple 推送通知，并修改应用程序委派。

### <a name="prepare-your-application-for-apple-push-notifications"></a>准备好应用程序以使用 Apple 推送通知
请按照指南操作：[如何准备应用程序以使用 Apple 推送通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>添加必要的客户端代码
*目前，应用程序应该在 Engagement 前端有一个已注册的 Apple 推送证书。*

如果尚未完成，需要注册应用程序以接收推送通知。

* 导入 `User Notification` 框架：

        #import <UserNotifications/UserNotifications.h>
* 启动应用程序时，请添加以下行（通常添加到 `application:didFinishLaunchingWithOptions:` 中）：

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

然后，需要向 Engagement 提供 Apple 服务器返回的设备令牌。 该操作会在应用程序委派中的名为 `application:didRegisterForRemoteNotificationsWithDeviceToken:` 方法中完成：

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

最后，在应用程序接收远程通知时，需要通知 Engagement SDK。 要做到这一点，请调用应用程序委派中的 `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` 方法：

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> 默认情况下，Engagement Reach 控制 completionHandler。 如果想手动响应在代码中的 `handler` 块，可以针对 `handler` 参数传递 nil，并自行控制 completion 块。 请参阅 `UIBackgroundFetchResult` 类型，获取可能的值列表。
>
>

### <a name="full-example"></a>完整示例
下面是集成的完整示例：

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>解决 UNUserNotificationCenter 委托冲突

如果应用程序或其中一个第三方库实现了 `UNUserNotificationCenterDelegate`，则可以跳过此部分。

SDK 使用 `UNUserNotificationCenter` 委托来监视运行 iOS 10 或更高版本的设备上的 Engagement 通知的生命周期。 SDK 具有其自己实现的 `UNUserNotificationCenterDelegate` 协议，但每个应用程序只能有一个 `UNUserNotificationCenter` 委托。 任何其他添加到 `UNUserNotificationCenter` 对象的委托将与 Engagement 委托冲突。 如果 SDK 检测到你或任何其他第三方的委托，则不会使用其自己的实现，从而让你有机会解决此冲突。 需要将 Engagement 逻辑添加到自己的委托中来解决此冲突。

可通过两种方式实现此目的：

方案 1：只需将委托调用转发到 SDK；

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

方案 2：通过继承自 `AEUserNotificationHandler` 类

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> 通过将通知的 `userInfo` 字典传递到代理 `isEngagementPushPayload:` 类方法，你可以确定该通知是否来自于 Engagement。

请确保 `UNUserNotificationCenter` 对象的委托在你的应用程序委托的 `application:willFinishLaunchingWithOptions:` 或 `application:didFinishLaunchingWithOptions:` 方法内设置为你的委托。
例如，如果已实现上述协议 1：

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="how-to-customize-campaigns"></a>如何自定义市场活动
### <a name="notifications"></a>通知
有两种类型的通知：系统和应用内通知。

系统通知由 iOS 处理，且无法进行自定义。

应用内通知，是由以动态添加到当前应用程序窗口的视图组成。 这称为通知覆盖。 通知覆盖非常适用于快速集成，因为它们不需要你修改应用程序中的任何视图。

#### <a name="layout"></a>布局
要修改应用内通知的外观，可以按自己需求直接修改 `AENotificationView.xib` 文件，只要保留标记值和现有的子视图类型即可。

默认情况下，应用内通知出现在屏幕底部。 如果想要将它们显示在屏幕的顶部，请编辑所提供的 `AENotificationView.xib` 并更改主视图的 `AutoSizing` 属性，这样通知便可以位于其超视图的顶部了。

#### <a name="categories"></a>Categories
当修改提供的布局时，也会修改所有通知的外观。 类别允许定义通知的各种目标外观（可能是行为）。 创建市场宣传活动时可以指定类别。 请记住，使用类别还可以自定义公告和投票，本文档稍后会介绍这部分内容。

若要注册通知类别处理程序，需要在市场宣传模块初始化后添加调用。

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier` 必须是符合 `AENotifier` 协议的对象实例。

可以自己实现此协议方法，也可以选择要重新实现已执行的大部分工作的现有类 `AEDefaultNotifier`。

例如，如果想为特定类别重新定义通知视图，则可以按照以下示例操作：

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

此简单的类别示例假定你在主应用程序捆绑包中有一个名为 `MyNotificationView.xib` 的文件。 如果该方法无法找到相应的 `.xib`，则通知将无法显示，Engagement 会在控制台中输出一条消息。

提供的 nib 文件应遵循以下规则：

* 它应只包含一个视图。
* 子视图的类型应与名为 `AENotificationView.xib` 的 nib 文件内提供的类型相同。
* 子视图具有的标记应与名为 `AENotificationView.xib` 的 nib 文件内提供的标记相同。

> [!TIP]
> 只需复制提供的 nib 文件（名为 `AENotificationView.xib`），并从该处开始工作。 但请注意，此 nib 文件内的视图与 `AENotificationView` 类相关联。 此类重新定义了方法 `layoutSubViews`，因而可根据上下文移动和调整其子视图大小。 可能要将其替换为 `UIView` 或自定义视图类。
>
>

如果需要进一步自定义通知（比方说希望直接从代码加载视图），建议看一看 `Protocol ReferencesDefaultNotifier` 和 `AENotifier` 的源代码以及类文档。

请注意，可以针对多个类别使用相同的通知程序。

也可以重新定义默认通知程序，如下所示：

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>通知处理
使用默认类别时，会在 `AEReachContent` 对象上调用某些生命周期方法，以报告统计信息并更新市场活动状态：

* 在应用程序中显示通知时，如果 `handleNotification:` 返回 `YES`，则 `displayNotification` 方法（该方法报告统计信息）将通过 `AEReachModule` 调用。
* 如果关闭通知，系统将调用 `exitNotification` 方法、报告统计信息，并且立即处理后续市场活动。
* 如果单击该通知，则系统将调用 `actionNotification`、报告统计信息并执行相关联的操作。

如果 `AENotifier` 实现绕过默认行为，则必须自行调用这些生命周期方法。 下面的示例阐释了绕过默认行为的一些情况：

* 不扩展 `AEDefaultNotifier`，例如，从头开始实现类别处理。
* 重写了 `prepareNotificationView:forContent:`，请务必至少将 `onNotificationActioned` 或 `onNotificationExited` 映射到其中一个 U.I 控件。

> [!WARNING]
> 如果 `handleNotification:` 抛出异常，则内容会被删除并调用 `drop`，这种情况会在统计信息中报告，且可以立即处理后续活动。
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>在现有视图中包含通知
覆盖非常适合用于快速集成，但有时不太方便或产生负面影响。

如果不满意某些视图中的覆盖系统，可以为这些视图进行自定义。

可以决定是否在现有视图中包括我们的通知布局。 如果要包括，有两种实现方式：

1. 使用界面生成器添加通知视图

   * 打开 *Interface Builder*
   * 添加一个大小为 320x60（iPad 上为 768x60）的 `UIView`。通知即显示在视图控件中。
   * 将此视图的标记值设置为：**36822491**
2. 以编程方式添加通知视图。 只需在初始化视图后添加以下代码即可：

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

可以在 `AEDefaultNotifier.h` 中找到 `NOTIFICATION_AREA_VIEW_TAG` 宏。

> [!NOTE]
> 默认通知程序会自动检测通知布局是否包含在此视图中，并且不会为其添加覆盖。
>
>

### <a name="announcements-and-polls"></a>公告和投票
#### <a name="layouts"></a>布局
可以修改 `AEDefaultAnnouncementView.xib` 和 `AEDefaultPollView.xib` 文件，只要保留标记值和现有子视图类型即可。

#### <a name="categories"></a>类别
##### <a name="alternate-layouts"></a>备用布局
与通知相似，活动类别可用于公告和投票的备用布局。

要为通知创建类别，则必须在市场宣传模块初始化后扩展 **AEAnnouncementViewController** 并将其注册：

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> 每当用户单击通知以获取 "my\_category" 类的公告时，则会调用 `initWithAnnouncement:` 方法来初始化已注册的视图控制器（此例中是 `MyCustomAnnouncementViewController`），并将该视图添加到当前应用程序窗口中。
>
>

在 `AEAnnouncementViewController` 类的实现过程中，必须读取 `announcement` 属性以初始化子视图。 请考虑下面的示例，其中两个标签会使用 `AEReachAnnouncement` 类的 `title` 和 `body` 属性进行初始化：

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

如果不想自行加载视图，而希望重复使用默认公告视图布局，则只需使自定义视图控制器扩展提供的 `AEDefaultAnnouncementViewController` 类。 在这种情况下，复制 `AEDefaultAnnouncementView.xib` nib 文件并将其重命名，这样它便可以通过自定义视图控制器加载（如果是名为 `CustomAnnouncementViewController` 的控制器，则应调用 `CustomAnnouncementView.xib` nib 文件）。

要替换公告的默认类别，只需针对 `kAEReachDefaultCategory` 中定义的类别注册自定义视图控制器即可：

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

也可以通过相同的方式自定义投票：

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

这时，提供的 `MyCustomPollViewController` 必须扩展 `AEPollViewController`。 或者也可以选择从默认控制器 `AEDefaultPollViewController` 进行扩展。

> [!IMPORTANT]
> 记住，在关闭视图控制器前调用 `action`（对于自定义的投票视图控制器，为 `submitAnswers:`）或 `exit` 方法。 否则，不会发送统计信息（即没有市场活动分析数据），更严重的是，在重新启动应用程序进程之前，系统不会通知后续市场活动。
>
>

##### <a name="implementation-example"></a>实现示例
在此实现中，自定义公告视图将从外部 xib 文件中加载。

就像高级通知自定义一样，也建议查看标准实现的源代码。

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
