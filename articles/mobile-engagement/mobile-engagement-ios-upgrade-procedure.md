---
title: "Azure Mobile Engagement iOS SDK 升级过程 |Microsoft 文档"
description: "最新的更新和过程进行 Azure Mobile Engagement 的 iOS SDK"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: 37c7f133d079186f828d58cabce0d2a259efd085
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="upgrade-procedures"></a>升级过程
如果你已具有集成到你的应用程序较旧版本的用户参与策略，你必须升级 SDK 时，请考虑以下几点。

每个新版本的 sdk，你必须首先将 （删除并重新导入在 xcode 中） 的 EngagementSDK 和 EngagementReach 文件夹。

## <a name="from-300-to-400"></a>从到 4.0.0 3.0.0
### <a name="xcode-8"></a>XCode 8
XCode 8 是必需的 SDK 版本 4.0.0 从开始。

> [!NOTE]
> 如果你实际上取决于 XCode 7，则可以使用[iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)。 是一个已知的 bug，此旧版本的模块上 iOS 10 的设备上运行时： 系统通知不是操作。 若要解决此你需要实现不推荐使用的 API`application:didReceiveRemoteNotification:`在你的应用程序委托，如下所示：
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **我们不建议此解决方法**如此行为可以在任何即将发布的 （即使次要） iOS 版本升级过程中更改，因为此 iOS API 已弃用。 应尽可能快地切换到 XCode 8。
> 
> 

### <a name="usernotifications-framework"></a>UserNotifications framework
你需要添加`UserNotifications`框架在你生成阶段。

在项目资源管理器，打开你项目窗格，然后选择正确的目标。 然后，打开**"生成阶段"**选项卡并在**"二进制文件链接与库"**菜单上，添加框架`UserNotifications.framework`-设置作为链接`Optional`

### <a name="application-push-capability"></a>应用程序推送功能
XCode 8 可以重置你的应用程序推送功能，请再次确认`capability`的所选目标的选项卡。

### <a name="add-the-new-ios-10-notification-registration-code"></a>添加新的 iOS 10 通知注册代码
较旧的代码段，以注册到通知应用程序仍可用于，但在 iOS 10 上运行时使用弃用的 Api。

导入`User Notification`framework:

        #import <UserNotifications/UserNotifications.h> 

在你的应用程序委托中`application:didFinishLaunchingWithOptions`方法替换：

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

通过：

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

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>解决 UNUserNotificationCenter 委托冲突

*如果你的应用程序或第三方库的一个既不实现`UNUserNotificationCenterDelegate`然后则可以跳过此部分。*

A `UNUserNotificationCenter` SDK 使用委托来监视在 iOS 10 或更高版本上运行的设备上的用户参与策略通知生命周期。 SDK 具有其自己的实现的`UNUserNotificationCenterDelegate`协议，但可能只有一个`UNUserNotificationCenter`委托每个应用程序。 添加到的任何其他委托`UNUserNotificationCenter`对象将与参与其中一个冲突。 如果 SDK 检测到你的或任何其他第三方的委托，则它将不使用其自己的实现使你有机会来解决此冲突。 必须将参与逻辑添加到您自己的委托，以解决冲突。

有两种方法来实现此目的。

建议 1，只需通过委托调用转发到 SDK:

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

或通过继承方案 2，`AEUserNotificationHandler`类

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
> 你可以确定传入从用户参与策略或未通过传递通知其`userInfo`到代理的字典`isEngagementPushPayload:`类方法。

请确保`UNUserNotificationCenter`对象的委托设置为一个内部委托`application:willFinishLaunchingWithOptions:`或`application:didFinishLaunchingWithOptions:`的应用程序委托的方法。
例如，如果实现上述方案 1:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code
  
        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="from-200-to-300"></a>从到 3.0.0 2.0.0
删除适用于 iOS 支持 4.X。 从此版本开始你的应用程序的部署目标必须至少为 iOS 6。

如果你将到达应用程序中，你必须添加`remote-notification`值赋给`UIBackgroundModes`为了接收远程通知 Info.plist 文件中的数组。

该方法`application:didReceiveRemoteNotification:`需要通过替换`application:didReceiveRemoteNotification:fetchCompletionHandler:`中您的应用程序代理。

已弃用"AEPushDelegate.h"接口，并且您需要删除所有引用。 这包括删除`[[EngagementAgent shared] setPushDelegate:self]`以及从您的应用程序代理的委托方法：

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>从到 2.0.0 1.16.0
以下介绍如何从 Capptain 服务 Capptain sas 提供到应用由 Azure Mobile Engagement 提供支持迁移 SDK 集成。
如果你要从早期版本迁移，请查看 Capptain web 站点以迁移到 1.16 第一次，然后将应用以下过程。

> [!IMPORTANT]
> Capptain 和 Mobile Engagement 不相同的服务，下面给出的过程只重点介绍如何迁移客户端应用程序。 迁移应用程序中的 SDK 将不会迁移你的数据从 Capptain 服务器到 Mobile Engagement 服务器
> 
> 

### <a name="agent"></a>代理
该方法`registerApp:`新方法已替换为`init:`。 您的应用程序代理必须相应地更新，并使用连接字符串：

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

已从您只需删除的所有实例的 SDK 中删除 SmartAd 跟踪`AETrackModule`类

### <a name="class-name-changes"></a>类名更改
作为添加品牌名称的一部分，有几个需要更改的类/文件名称。

以"遍历"前缀，前缀为"CP"的所有类将重都命名。

例如：

* `CPModule.h`已重命名为`AEModule.h`。

具有"参与"前缀，前缀为"Capptain"的所有类将重都命名。

例如：

* 类`CapptainAgent`已重命名为`EngagementAgent`。
* 类`CapptainTableViewController`已重命名为`EngagementTableViewController`。
* 类`CapptainUtils`已重命名为`EngagementUtils`。
* 类`CapptainViewController`已重命名为`EngagementViewController`。

