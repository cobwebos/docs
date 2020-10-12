---
title: Azure 通知中心富推送
description: 了解如何从 Azure 将富推送通知发送到 iOS 应用。 代码示例是使用 .Objective-C 和 C# 编写的。
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 33626b7aee615d07ef88dd9fbca46e6512e2cafc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090357"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure 通知中心富推送

## <a name="overview"></a>概述

为了吸引用户使用即时丰富内容，除纯文本之外，应用程序可能还需要以其他形式进行推送。 这些通知将提升用户交互并显示 Url、声音、图像/优惠券等内容。 本教程以 [通知用户](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) 教程为基础，演示如何发送包含负载 (的推送通知，如) 的映像。

本教程与 iOS 7 和8兼容。

  ![三张屏幕截图：带有“发送推送”按钮的应用屏幕、设备上的开始屏幕以及带有“后退”按钮的 Windows 徽标。][IOS1]

在高级别中：

1. 应用后端：
   * 在这种情况下存储丰富的负载 (在这种情况下是后端数据库/本地存储中的) 映像。
   * 将此丰富通知的 ID 发送到设备。
2. 设备上的应用：
   * 联系后端，该后端请求带有接收的 ID 的丰富有效负载。
   * 数据检索完成后，在设备上发送用户通知，并在用户点击以了解详细信息时立即显示有效负载。

## <a name="webapi-project"></a>WebAPI 项目

1. 在 Visual Studio 中，打开在[通知用户](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教程中创建的 **AppBackend** 项目。
2. 获取用于通知用户的映像，并将其置于项目目录的 **img** 文件夹中。
3. 单击“解决方案资源管理器”中的“显示所有文件”****，右键单击该文件夹以“包括在项目中”****。
4. 选择图像后，将 "**属性**" 窗口中的 "**生成" 操作**更改为 "**嵌入的资源**"。

    ![解决方案资源管理器的屏幕截图。 图像文件处于选中状态，在其“属性”窗格中，嵌入的资源作为生成操作列出。][IOS2]
5. 在中 `Notifications.cs` ，添加以下 `using` 语句：

    ```csharp
    using System.Reflection;
    ```

6. 将 `Notifications` 类替换为以下代码。 请确保将占位符替换为通知中心凭据和图像文件名：

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

7. 在中 `NotificationsController.cs` ， `NotificationsController` 用以下代码重新定义。 这会将初始无提示的丰富通知 ID 发送到设备，并允许客户端检索映像：

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

8. 现在，请将此应用重新部署到 Azure 网站，以便可以从所有设备进行访问。 右键单击 **AppBackend** 项目，并选择“发布”****。
9. 选择 **Azure 网站** 作为发布目标。 使用 Azure 帐户登录，选择现有的或新的网站，并记下 "**连接**" 选项卡中的 "**目标 URL** " 属性。在本教程的后面部分，我们将此 URL 称为*后端终结点*。 选择“发布”。

## <a name="modify-the-ios-project"></a>修改 iOS 项目

现在，你已将应用后端修改为只发送通知的 *ID* ，更改 iOS 应用以处理该 id，并从后端检索富消息：

1. 打开 iOS 项目，并转到“目标”**** 部分的主应用目标来启用远程通知。
2. 选择 " **功能**"、"启用 **后台模式**"，并选中 " **远程通知** " 复选框。

    ![显示“功能”屏幕的 iOS 项目的屏幕截图。 后台模式已打开，远程通知复选框处于选中状态。][IOS3]
3. 打开 `Main.storyboard` ，并确保在本教程中 (称为 "主视图控制器" 的视图控制器) " [通知用户](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) " 教程。
4. 将 **导航控制器** 添加到情节提要，然后按住 ctrl 并拖动主页视图控制器，使其成为导航的 **根视图** 。 请确保仅为导航控制器选择了 "特性检查器" 中的 " **是初始视图控制器** "。
5. 将 **视图控制器** 添加到情节提要并添加 **图像视图**。 用户单击此通知选择了解详细信息后，会看到此页面。 Storyboard 应类似于：

    ![情节提要的屏幕截图。 可以看到三个应用屏幕：导航视图、主页视图和图像视图。][IOS4]
6. 单击情节提要中的 " **主页" 视图控制器** ，并确保它在标识检查器下已 **HomeViewController** 为其 **自定义类** 和 **情节提要 ID** 。
7. 对图像视图控制器执行相同的操作，如 **imageViewController**。
8. 然后，创建一个名为 **imageViewController** 的新视图控制器类来处理刚创建的 UI。
9. 在 **imageViewController**中，将以下代码添加到控制器的接口声明中。 请务必按住 Ctrl 键并从情节提要图像视图拖动到这些属性中，以链接两者：

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```

10. 在 `imageViewController.m` 中，在 `viewDidload` 的末尾添加以下内容：

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```

11. 在 `AppDelegate.m` 中，导入已创建的映像控制器：

    ```objc
    #import "imageViewController.h"
    ```

12. 使用以下声明添加接口部分：

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```

13. 在 `AppDelegate` 中，确保应用在 `application: didFinishLaunchingWithOptions` 中注册了无提示通知：

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. 替换为的以下实现， `application:didRegisterForRemoteNotificationsWithDeviceToken` 以使情节提要 UI 更改生效：

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```

15. 然后，将以下方法添加到 `AppDelegate.m`，以从终结点检索映像，并在检索完成后发送本地通知。 请确保将占位符 `{backend endpoint}` 替换为后端终结点：

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```

16. 通过使用以下方法打开中的图像视图控制器来处理以前的本地通知 `AppDelegate.m` ：

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>运行应用程序

1. 在 XCode 中，在物理 iOS 设备上运行此应用（推送通知将无法在模拟器中正常工作）。
2. 在 iOS 应用 UI 中，输入相同值的用户名和密码进行身份验证，并单击“登录”****。
3. 单击“发送推送”****，应看到应用内警报。 如果单击“详细信息”****，会转到选择要包括在应用后端中的映像。
4. 也可以单击“发送推送”**** 并立即按下设备的主页按钮。 几分钟后会收到推送通知。 如果点击推送通知或单击“详细信息”，会转到应用和富图像内容。

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
