<properties pageTitle="向经过身份验证的用户发送推送通知" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="了解如何向特定用户发送推送通知 " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 向经过身份验证的用户发送推送通知

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET backend" class="current">.NET 后端</a> | <a href="/zh-cn/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript backend">JavaScript 后端</a></div>

本主题说明如何向任何已注册 iOS 设备上已经过身份验证的用户发送推送通知。与前面的[推送通知][Get started with push notifications]教程不同，本教程将指导你更改移动服务，以要求先对用户进行身份验证，然后，才可以将客户端注册到通知中心以发送推送通知。此外，你还要修改注册，以根据分配的用户 ID 添加标记。最后，将要更新服务器代码，以便只将通知发送到已经过身份验证的用户而不是所有注册。

本教程将指导你完成以下过程：

+ [更新服务以要求对注册进行身份验证]
+ [更新应用程序以要求在注册之前登录]
+ [测试应用程序]

本教程支持 Windows 应用商店和 Windows Phone 应用商店应用程序。

## 先决条件

在开始本教程之前，必须已完成以下移动服务教程：

+ [身份验证入门][Get started with authentication]<br/>向 TodoList 示例应用程序添加登录要求。

+ [推送通知入门][Get started with push notifications]<br/>配置 TodoList 示例应用程序，以使用通知中心发送推送通知。

完成这两篇教程后，你可以防止未经身份验证的用户从你的移动服务注册推送通知。

## <a name="register"></a>更新服务以要求对注册进行身份验证

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

## <a name="update-app"></a>更新应用程序以要求在注册之前登录

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a>测试应用

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[更新服务以要求对注册进行身份验证]: #register
[更新应用程序以要求在注册之前登录]: #update-app
[测试应用程序]: #test
[后续步骤]:#next-steps


<!-- URLs. -->
[Get started with authentication]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Get started with push notifications]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/

[Azure 管理门户]: https://manage.windowsazure.cn/
[移动服务 .NET 操作方法概念性参考]: /zh-cn/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
