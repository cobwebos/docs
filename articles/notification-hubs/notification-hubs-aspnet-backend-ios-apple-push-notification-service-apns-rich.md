---
title: "Azure 通知中心富推送"
description: "了解如何从 Azure 将富推送通知发送到 iOS 应用程序。 Objective C 和 C# 中编写的代码示例。"
documentationcenter: ios
services: notification-hubs
author: ysxu
manager: erikre
editor: 
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 394efdc2dfaff0666bc23d8a448b0a00d414da99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs-rich-push"></a>Azure 通知中心富推送
## <a name="overview"></a>概述
为了吸引用户使用即时丰富内容，应用程序可能想要推送超出纯文本。 这些通知将提升用户交互和存在内容 （如 url、 声音、 图像/优惠券等）。 本教程基于[通知用户](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)主题，并演示如何发送包含有效负载 （例如，图像） 的推送通知。

本教程适用于 iOS 7 和 8。

  ![][IOS1]

在高级别中：

1. 应用程序后端：
   * 将存储富有效负载 （在这种情况下，图像） 后端数据库/本地存储中
   * 向设备发送此富通知的 ID
2. 在设备上的应用程序：
   * 联系请求它收到的 ID 与富有效负载的后端
   * 在设备上发送用户通知时数据检索已完成，并且用户点击以了解详细信息时立即显示有效负载

## <a name="webapi-project"></a>WebAPI 项目
1. 在 Visual Studio 中，打开**AppBackend**中创建的项目[通知用户](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教程。
2. 获取你想要使用，通知用户并将其置于的图像**img**项目目录中的文件夹。
3. 单击**显示所有文件**在解决方案资源管理器，右键单击文件夹以**包括在项目**。
4. 选定的图像，更改到属性窗口在其生成操作**嵌入的资源**。
   
    ![][IOS2]
5. 在**Notifications.cs**，添加以下 using 语句：
   
        using System.Reflection;
6. 更新整个**通知**类替换为以下代码。 请确保将占位符替换为你的通知中心凭据和图像文件名称。
   
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
   
   > [!NOTE]
   > （可选）请参阅[如何嵌入并通过使用 Visual C# 访问资源](http://support.microsoft.com/kb/319292)有关如何添加和获取项目资源的详细信息。
   > 
   > 
7. 在**NotificationsController.cs**，重新定义**NotificationsController**使用以下代码段。 这将初始无提示的富通知 id 发送到设备，并允许客户端对图像进行检索：
   
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
8. 现在，我们将重新部署到 Azure 网站的此应用程序以使其可从所有设备访问。 右键单击**AppBackend**项目，然后选择**发布**。
9. 选择 Azure 网站作为发布目标。 登录你的 Azure 帐户，选择一个现有的或新的网站，并记下**目标 URL**中的属性**连接**选项卡。 我们将此 url 称为你*后端终结点*本教程后面。 单击**发布**。

## <a name="modify-the-ios-project"></a>修改 iOS 项目
现在，你已修改将应用后端发送仅*id*一个通知，您将更改你的 iOS 应用来处理该 id，并从后端检索富消息。

1. 打开您的 iOS 项目，并通过转到主应用目标中启用远程通知**目标**部分。
2. 单击**功能**，开启**后台模式**，并检查**远程通知**复选框。
   
    ![][IOS3]
3. 转到**Main.storyboard**，并确保从具有视图控制器 （主页视图控制器中称为本教程）[通知用户](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教程。
4. 添加**导航控制器**到情节提要，并控件拖动到主页视图控制器以使其**根视图**的导航。 请确保**是初始视图控制器**导航控制器仅在属性中选中检查器。
5. 添加**视图控制器**到情节提要并添加**图像视图**。 这是他们选择若要了解详细信息，请单击页面后，用户将看到的页。 将情节提要应如下所示：
   
    ![][IOS4]
6. 单击**主页视图控制器**情节提要，并确保它同时具有**homeViewController**作为其**自定义类**和**情节提要 ID**标识检查器下方。
7. 为作为图像视图控制器执行同样**imageViewController**。
8. 然后，创建标题为一个新视图控制器类**imageViewController**来处理你刚刚创建的 UI。
9. 在**imageViewController.h**，将以下代码添加到控制器的接口声明。 请务必控件拖动从情节提要图像视图对这些属性以链接两者：
   
        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;
10. 在**imageViewController.m**，在末尾添加以下**viewDidload**:
    
        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];
11. 在**AppDelegate.m**，导入你创建的图像控制器：
    
        #import "imageViewController.h"
12. 添加接口部分使用以下声明：
    
        @interface AppDelegate ()
    
        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;
    
        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;
    
        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;
    
        @end
13. 在**AppDelegate**，请确保你的应用程序中的无提示通知的注册**应用程序： didFinishLaunchingWithOptions**:
    
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

1. 在以下实现中的替代**应用程序： didRegisterForRemoteNotificationsWithDeviceToken**以考虑情节提要 UI 更改帐户：
   
       // Access navigation controller which is at the root of window
       UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
       // Get home view controller from stack on navigation controller
       homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
       hvc.deviceToken = deviceToken;
2. 然后，添加以下方法**AppDelegate.m**以从你的终结点检索图像，并在检索完成后发送本地通知。 请确保将占位符`{backend endpoint}`与后端终结点：
   
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
3. 通过打开图像视图控制器处理上述本地通知**AppDelegate.m**使用以下方法：
   
       // Helper: redirect users to image view controller
       - (void)redirectToImageViewWithImage: (UIImage *)img {
           UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
           UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                    bundle: nil];
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

## <a name="run-the-application"></a>运行应用程序
1. 在 XCode 中，运行在物理 iOS 设备 （推送通知将无法工作在模拟器中） 上的应用。
2. 在 iOS 应用 UI 中，输入用户名和密码相同的值进行身份验证和单击**Log In**。
3. 单击**发送推送**，你应看到应用内警报。 如果你单击**详细**，你将转到您选择要包含在应用后端中的图像。
4. 您也可以单击**发送推送**并立即按下你的设备的主页按钮。 在几分钟后，您将收到一条推送通知。 如果你在其上点击或单击的详细信息，你将转到你的应用和富图像内容。

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
