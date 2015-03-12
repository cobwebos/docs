<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="推送通知入门 (iOS) | 移动开发人员中心" metaKeywords="" description="了解如何使用 Azure 移动服务将推送通知发送到 iOS 应用程序。" metaCanonical="http://www.windowsazure.cn/zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="12/15/2014" ms.author="krisragh" />


# 向移动服务应用程序添加推送通知

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

本主题说明如何使用 Azure 移动服务向 iOS 应用程序发送推送通知。在本教程中，你将要使用 Apple 推送通知服务 (APNS) 向快速入门项目添加推送通知。完成本教程后，每次插入一条记录时，你的移动服务就会发送一条推送通知。


本教程将指导你完成启用推送通知的以下基本步骤：

1. [生成证书签名请求]
2. [注册应用程序和启用推送通知]
3. [为应用程序创建配置文件]
4. [更新服务器以发送推送通知](#update-server)
5. [将移动服务发布到 Azure]
6. [向应用程序添加推送通知]
7. [启用推送通知以进行本地测试](#local-testing)
8. [针对发布的移动服务测试应用程序]

本教程需要的内容如下：

+ [移动服务 iOS SDK]
+ [XCode 4.5][安装 Xcode]
+ 支持 iOS 6.0（或更高版本）的设备
+ iOS 开发人员计划成员身份

   > [AZURE.NOTE] 由于推送通知配置要求，你必须在支持 iOS 的设备（iPhone 或 iPad），而不是在模拟器上部署和测试推送通知。

本教程基于移动服务快速入门。在开始本教程之前，必须先完成[移动服务入门]或[将移动服务添加到应用程序][Get started with data]。


[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]


## 配置移动服务以发送推送请求

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]


## <a id="update-server"></a>更新服务器以发送推送通知

1. 在 Visual Studio 的解决方案资源管理器中，展开移动服务项目中的 **Controllers** 文件夹。打开 TodoItemController.cs。在该文件的顶部，添加以下 `using` 语句：


		using System;
		using System.Collections.Generic;

2. 使用以下代码更新 `PostTodoItem` 方法定义：  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    这段代码可在插入 Todo 项之后发送推送通知（包含所插入项的文本）。在发生错误的情况下，这段代码将添加一个错误日志条目，该条目可在管理门户中的移动服务的"日志"选项卡上查看。


<h2><a name="publish-the-service"></a>将移动服务发布到 Azure</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 向应用程序添加推送通知

1. 在 QSAppDelegate.m 中插入以下代码段，以导入移动服务 iOS SDK：

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. 在 QSAppDelegate.m 中，替换实现中的以下处理程序方法：

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. 在 QSAppDelegate.m 中，在实现中添加以下处理程序方法：请务必复制移动服务 URL 和应用程序密钥值，并用这些值替换占位符：

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
            MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. 在 QSAppDelegate.m 中，在实现中添加以下处理程序方法：

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. 在 QSAppDelegate.m 中，在实现中添加以下处理程序方法：  

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

   > [AZURE.NOTE] 必须在调用 <strong>addItem</strong> 方法之前添加此代码。

你的应用现已更新，可支持推送通知。

## <a id="local-testing"></a> 启用推送通知以进行本地测试

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## 在应用程序中测试推送通知

1. 在支持 iOS 的设备中按"运行"按钮以生成项目并启动应用程序，然后单击"确定"接受推送通知

  	![][23]

    > [AZURE.NOTE] 你必须显式接受来自应用程序的推送通知。此请求只会在首次运行应用程序时出现。

2. 在应用程序中键入有意义的文本（例如"A new Mobile Services task"），然后单击加号 (+) 图标。

  	![][24]

3. 检查是否已收到通知，然后单击"确定"以取消通知。

  	![][25]

4. 重复步骤 2 并立即关闭应用程序，然后检查是否已显示以下推送通知。

  	![][26]

你已成功完成本教程。

## 后续步骤

本教程演示了有关如何使 iOS 应用程序使用移动服务和通知中心发送推送通知的基础知识。建议你接下来完成下一篇教程[向经过身份验证的用户发送推送通知]，其中说明了如何使用标记来做到只将推送通知从移动服务发送到经过身份验证的用户。

<!--+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->
通过以下主题了解有关移动服务和通知中心的详细信息：

* [数据处理入门]
  <br/>了解有关使用移动服务存储和查询数据的详细信息。

* [身份验证入门]
  <br/>了解如何通过移动服务对使用不同帐户类型的应用程序用户进行身份验证。

* [什么是通知中心？]
  <br/>详细了解通知中心如何将通知传送到各个主要客户端平台中的应用程序。

* [调试通知中心应用程序](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>获取有关对通知中心解决方案进行故障排除和调试的指导。 

<!-- Anchors.  -->
[生成证书签名请求]: #certificates
[注册应用程序和启用推送通知]: #register
[为应用程序创建配置文件]: #profile
[配置移动服务]: #configure
[更新脚本以发送推送通知]: #update-scripts
[向应用程序添加推送通知]: #add-push
[插入数据以接收通知]: #test
[针对发布的移动服务测试应用程序]: #test-app
[后续步骤]:#next-steps
[在本地下载服务]: #download-the-service-locally
[测试移动服务]: #test-the-service
[将移动服务发布到 Azure]: #publish-mobile-service

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[安装 Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS 设置门户]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[移动服务 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple 推送通知服务]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Azure 管理门户]: https://manage.windowsazure.cn/
[apns 对象]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[数据处理入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users

[向经过身份验证的用户发送推送通知]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/

[什么是通知中心？]: /zh-cn/documentation/articles/notification-hubs-overview/
[将广播通知发送到订户]: /zh-cn/documentation/articles/notification-hubs-ios-send-breaking-news/
[将基于模板的通知发送到订户]: /zh-cn/documentation/articles/notification-hubs-ios-send-localized-breaking-news/
