---
title: "Azure Mobile Engagement iOS SDK 概述 | Microsoft Docs"
description: "Azure Mobile Engagement 的 iOS SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/14/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8bfadc110b8b2e0de470185ec9d84343125c960d


---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK for Azure Mobile Engagement
从此处可以了解如何在 iOS 应用中集成 Azure Mobile Engagement 的所有详细信息。 如果想要先进行尝试，请确保完成我们的 [15 分钟教程](mobile-engagement-ios-get-started.md)。

单击查看 [SDK 内容](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>集成过程
1. 从此处开始：[如何在 iOS 应用中集成 Mobile Engagement](mobile-engagement-ios-integrate-engagement.md)
2. 有关通知：[如何在 iOS 应用中集成 Reach（通知）](mobile-engagement-ios-integrate-engagement-reach.md)
3. 标记计划实施：[如何在 iOS 应用中使用高级 Mobile Engagement 标记 API](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>发行说明
### <a name="400-09122016"></a>4.0.0 (09/12/2016)
* 修复了 iOS 10 设备上不起作用的通知。
* 弃用了 XCode 7。

对于较早版本，请参阅[完整的发行说明](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>升级过程
如果已将较旧版本的 Engagement 集成到应用程序中，则在升级 SDK 时必须考虑以下几点。

如果错过了几个版本的 SDK，则可能需要执行多个过程。请参阅完整的[升级过程](mobile-engagement-ios-upgrade-procedure.md)。

对于每个新版本的 SDK，你必须首先替换（移除并重新导入 xcode）EngagementSDK 和 EngagementReach 文件夹。

### <a name="from-300-to-400"></a>从 3.0.0 至 4.0.0
### <a name="xcode-8"></a>XCode 8
从 SDK 的版本 4.0.0 开始，XCode 8 就是必需的。

> [!NOTE]
> 如果确实需要使用 XCode 7，则可以使用 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)。 在 iOS 10 设备上运行时，此早期版本的市场宣传模块上存在一个已知 bug：无法操作系统通知。 要修复此问题，必须在应用委派中实现否决的 API `application:didReceiveRemoteNotification:`，如下所示：
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **我们不建议此解决方法**，因为此 iOS API 已被否决，此行为在任何即将发布的（即使再小）iOS 版本升级过程中会有所更改。 你应尽快改用 XCode 8。
> 
> 

#### <a name="usernotifications-framework"></a>UserNotifications 框架
你需要在构建阶段添加 `UserNotifications` 框架。

在项目资源管理器中，打开你的项目窗格，并选择正确的目标。 然后，打开“**构建阶段**”选项卡，在“**将二进制文件链接到库**”菜单中添加框架 `UserNotifications.framework` - 将链接设置为 `Optional`

#### <a name="application-push-capability"></a>应用程序推送功能
XCode 8 可能会重置你的应用推送功能，请在你选定目标的 `capability` 选项卡中再核实一下。

#### <a name="add-the-new-ios-10-notification-registration-code"></a>添加新的 iOS 10 通知注册代码
将应用注册到通知的较旧代码片段仍可使用，但在 iOS 10 上运行时会使用已弃用的 API。 

导入 `User Notification` 框架：

        #import <UserNotifications/UserNotifications.h>

在你的应用程序中，委托 `application:didFinishLaunchingWithOptions` 方法替换︰

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

用：

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

#### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>如果你已经有自己的 UNUserNotificationCenterDelegate 实现
SDK 具有其自身的 UNUserNotificationCenterDelegate 协议实现。 SDK 使用它来监视运行 iOS 10 或更高版本的设备上的 Engagement 通知的生命周期。 如果 SDK 检测到委派，它将不使用其自身的实现，因为每个应用程序只有一个 UNUserNotificationCenter 委派。 这意味着你需要将 Engagement 逻辑添加到自己的委派。

可通过两种方式实现此目的：

只需将委派调用转发到 SDK：

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

或通过继承自 `AEUserNotificationHandler` 类

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
> 
> 




<!--HONumber=Nov16_HO3-->


