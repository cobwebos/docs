---
title: 使用 Azure 通知中心通过后端服务向 Flutter 应用发送推送通知 | Microsoft Docs
description: 了解如何使用 Azure 通知中心通过后端服务向 Flutter 应用推送通知。
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: miparker
ms.openlocfilehash: 5fa753a6b8b1284c4f8fcd046f74fabcbae3f8fb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171000"
---
# <a name="tutorial-send-push-notifications-to-flutter-apps-using-azure-notification-hubs-via-a-backend-service"></a>教程：使用 Azure 通知中心通过后端服务向 Flutter 应用发送推送通知  

[![下载示例](./media/notification-hubs-backend-service-flutter/download.png)下载示例](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

本教程将使用 [Azure 通知中心](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview)将推送通知发送到适用于 Android 和 iOS 的 [Flutter](https://flutter.dev) 应用程序 。  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

本教程将指导你完成以下步骤：

> [!div class="checklist"]
>
> * [设置推送通知服务和 Azure 通知中心。](#set-up-push-notification-services-and-azure-notification-hub)
> * [创建 ASP.NET Core Web API 后端应用程序。](#create-an-aspnet-core-web-api-backend-application)
> * [创建跨平台 Flutter 应用程序。](#create-a-cross-platform-flutter-application)
> * [针对推送通知配置本地 Android 项目。](#configure-the-native-android-project-for-push-notifications)
> * [针对推送通知配置本地 iOS 项目。](#configure-the-native-ios-project-for-push-notifications)
> * [测试解决方案。](#test-the-solution)

## <a name="prerequisites"></a>先决条件

若要继续操作，需要：

* [Azure 订阅](https://portal.azure.com)，可以在其中创建和管理资源。
* [Flutter](https://flutter.dev/docs/get-started/install) 工具包（及其必备组件）。
* 已安装 [Flutter 和 Dart 插件](https://flutter.dev/docs/get-started/editor?tab=vscode)的 [Visual Studio Code](https://code.visualstudio.com)。
* 在 Android（物理设备或仿真器设备）或 iOS（仅物理设备）上运行应用的功能。 

对于 Android 而言，必须具有：

* 开发人员解锁的物理设备或仿真器（运行安装了 Google Play Services 的 API 26 及更高版本）。

对于 iOS 而言，必须具有：

* 有效的 [Apple 开发人员帐户](https://developer.apple.com)。
* [已注册到开发人员帐户的物理 iOS 设备](https://help.apple.com/developer-account/#/dev40df0d9fa)（运行 iOS 13.0 及更高版本）。
* 在“密钥链”中安装的 p12 [开发证书](https://help.apple.com/developer-account/#/dev04fd06d56)，让你可以在[物理设备上运行应用](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca)。 
* 已安装 [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation)，用于管理库依赖项。

> [!NOTE]
> IOS 模拟器不支持远程通知，因此在 iOS 上浏览此示例时需要使用物理设备。 但是，无需在 Android 和 iOS 上都运行应用以完成本教程。 

可以遵循此首要原则示例中的步骤，而不需要事先体验。 不过，如能熟悉以下方面则会更有帮助。

* [Azure 开发人员门户](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure 通知中心](notification-hubs-push-notification-overview.md)
* [Google Firebase 控制台](https://console.firebase.google.com/u/0/)
* 用于跨平台开发的 [Flutter](https://flutter.dev) 和 [Dart](https://dart.dev)
* 用于 Android 和 iOS 本机开发的 [Kotlin](https://kotlinlang.org) 和 [Swift](https://developer.apple.com/swift)

提供的步骤特定于 [macOS](https://developer.apple.com/macos)。 通过跳过 iOS 方面，可以在 [Windows](https://www.microsoft.com/windows) 上继续操作。

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>设置推送通知服务和 Azure 通知中心

在此部分，将设置 **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** 和 **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** 然后，创建并配置通知中心来处理这些服务。

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>创建 ASP.NET Core Web API 后端应用程序

在本部分中，将创建 [ASP.NET Core Web API](https://dotnet.microsoft.com/apps/aspnet/apis) 后端来进行[设备注册](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration)并将通知发送到 Flutter 移动应用。

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-flutter-application"></a>创建跨平台 Flutter 应用程序

在本部分中，将构建一个 [Flutter](https://flutter.dev) 移动应用程序，实施以跨平台的方式推送通知。

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Flutter application](../../includes/notification-hubs-backend-service-sample-app-flutter.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>针对推送通知配置本地 Android 项目

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-flutter-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>针对推送通知配置本地 iOS 项目

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-flutter-ios.md)]

## <a name="test-the-solution"></a>测试解决方案

现在可以测试通过后端服务发送通知。

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>后续步骤

现在应具有一个通过后端服务连接到通知中心的基本 Flutter 应用，可以发送和接收通知。

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>疑难解答

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>相关链接

* [Azure 通知中心概述](notification-hubs-push-notification-overview.md)
* [在 macOS 上安装 Flutter](https://flutter.dev/docs/get-started/install/macos)
* [在 Windows 上安装 Flutter](https://flutter.dev/docs/get-started/install/windows)
* [用于后端操作的通知中心 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [GitHub 上的通知中心 SDK](https://github.com/Azure/azure-notificationhubs)
* [使用应用程序后端注册](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [注册管理](notification-hubs-push-notification-registration-management.md)
* [使用标记](notification-hubs-tags-segment-push-message.md)
* [使用自定义模板](notification-hubs-templates-cross-platform-push-messages.md)
