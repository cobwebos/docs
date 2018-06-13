---
title: 使用 Azure 通知中心向 iOS 设备推送本地化通知 | Microsoft Docs
description: 了解如何使用 Azure 通知中心向 iOS 设备推送本地化通知。
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b3d74086ee233da50138aff00d8da78aa0243a75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776492"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>教程：使用 Azure 通知中心向 iOS 设备推送本地化通知 
> [!div class="op_single_selector"]
> * [Windows 应用商店 C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

本教程介绍如何使用 Azure 通知中心的[模板](notification-hubs-templates-cross-platform-push-messages.md)功能广播已按语言和设备本地化的突发新闻通知。 在本教程中，将从在[使用通知中心发送突发新闻]中创建的 iOS 应用开始操作。 完成后，可注册感兴趣的突发新闻类别，指定要接收通知的语言并仅接收采用该语言的所选类别的推送通知。

此方案包含两个部分：

* iOS 应用程序允许客户端设备指定一种语言并订阅不同的突发新闻类别；
* 后端使用 Azure 通知中心的“标记”和“模板”功能广播通知。

在本教程中，会执行以下步骤：

> [!div class="checklist"]
> * 更新应用用户界面
> * 生成 iOS 应用
> * 通过 .NET 控制台应用发送本地化模板通知
> * 通过设备发送本地化模板通知


## <a name="overview"></a>概述
在[使用通知中心发送突发新闻]中，生成了一个使用“标记”订阅不同新闻类别通知的应用。 但是，很多应用程序针对多个市场，需要本地化。 这意味着通知内容本身必须本地化且传递到正确的设备组。 本教程介绍如何使用通知中心的“模板”功能轻松传递本地化突发新闻通知。

> [!NOTE]
> 发送本地化通知的一种方式是创建每个标签的多个版本。 例如，要支持英语、法语和汉语，需要对世界新闻使用三种不同的标签：“world_en”、“world_fr”和“world_ch”。 然后必须将本地化版本的世界新闻分别发送到这些标签。 在本主题中，会使用模板来避免增生标签和发送多个消息的要求。

在较高级别上，模板是指定特定设备应如何接收通知的一种方法。 模板通过引用作为应用程序后端所发消息的一部分的属性，指定确切的负载格式。 在此示例中，将发送包含所有支持语言的区域设置未知的消息：

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

然后确保设备注册到引用正确属性的模板。 例如，要注册法语新闻的 iOS 应用会使用以下语法注册：

    {
        aps:{
            alert: "$(News_French)"
        }
    }

有关模板的详细信息，请参阅[模板](notification-hubs-templates-cross-platform-push-messages.md)一文。

## <a name="prerequisites"></a>先决条件

- 完成[向特定 iOS 设备推送通知](notification-hubs-ios-xplat-segmented-apns-push-notification.md)教程，并具有可用代码，因为本教程直接基于该代码。
- 可以选用 Visual Studio 2012 或更高版本。

## <a name="update-the-app-user-interface"></a>更新应用用户界面
本部分会修改在[使用通知中心发送突发新闻]主题中创建的“突发新闻”应用，以便使用模板发送本地化突发新闻。

在 MainStoryboard_iPhone.storyboard 中，添加使用以下三种语言的分段控件：英语、法语和汉语。

![][13]

然后确保在 ViewController.h 中添加 IBOutlet，如下图所示：

![][14]

## <a name="build-the-ios-app"></a>生成 iOS 应用
1. 在 Notification.h 中，添加 retrieveLocale 方法，并修改 store 和 subscribe 方法，如以下代码中所示：
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    在 Notification.m 中，通过添加区域设置参数并将它存储在用户默认值中，修改 *storeCategoriesAndSubscribe* 方法：
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    然后修改 subscribe 方法以包括该区域设置：
   
    ```obj-c
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
       ```
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. 现在已修改 Notifications 类，必须确保 ViewController 使用新的 UISegmentControl。 在 *viewDidLoad* 方法中添加以下行，以确保显示当前选择的区域设置：
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    然后，在 subscribe 方法中，将对 storeCategoriesAndSubscribe 的调用更改为以下代码：
   
    ```obj-c
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
    ```
3. 最后，必须在 AppDelegate.m 中更新 *didRegisterForRemoteNotificationsWithDeviceToken* 方法，以便在应用启动时正确刷新注册信息。 将对通知的 subscribe 方法的调用更改为以下代码：
   
    ```obj-c
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>（可选）通过 .NET 控制台应用发送本地化模板通知
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>（可选）通过设备发送本地化的模板通知
如果无权访问 Visual Studio，或者只是想要试一试直接从设备上的应用发送本地化的模板通知。 那么，可将本地化模板参数添加到在前一教程中定义的 `SendNotificationRESTAPI` 方法。

    ```obj-c
        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }
    ```


## <a name="next-steps"></a>后续步骤
在本教程中，会向 iOS 设备发送本地化通知。 若要了解如何向特定的 iOS 应用用户推送通知，请转到以下教程： 

> [!div class="nextstepaction"]
>[向特定用户推送通知](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[使用通知中心发送突发新闻]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
