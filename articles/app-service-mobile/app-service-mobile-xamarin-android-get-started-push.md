---
title: "向 Xamarin Android 应用添加推送通知 | Microsoft Docs"
description: "了解如何使用 Azure App Service 和 Azure 通知中心将推送通知发送到 Xamarin Android 应用"
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c9f8c46fa5fba1e69382a383ff94700de07db4ab


---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>向 Xamarin.Android 应用添加推送通知
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概述
本教程介绍如何向 [Xamarin.Android 快速入门](app-service-mobile-windows-store-dotnet-get-started.md)项目添加推送通知，以便每次插入一条记录时，向设备发送一条推送通知。

如果不使用下载的快速入门服务器项目，则需要推送通知扩展包。 有关详细信息，请参阅[使用用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="prerequisites"></a>先决条件
本教程需要的内容如下：

* 有效的 Google 帐户 可以在 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302) 注册 Google 帐户。
* [Google Cloud Messaging 客户端组件](http://components.xamarin.com/view/GCMClient/)。

## <a name="a-nameconfigure-hubaconfigure-a-notification-hub"></a><a name="configure-hub"></a>配置通知中心
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="a-idregisteraenable-firebase-cloud-messaging"></a><a id="register"></a>启用 Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>配置 Azure 以发送推送请求
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="a-idupdate-serveraupdate-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>更新服务器项目以发送推送通知
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="a-idconfigure-appaconfigure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>为推送通知配置客户端项目
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="a-idadd-pushaadd-push-notifications-code-to-your-app"></a><a id="add-push"></a>向应用程序添加推送通知代码
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="a-nametestatest-push-notifications-in-your-app"></a><a name="test"></a>在应用程序中测试推送通知
可以使用模拟器中的虚拟设备测试应用。 在模拟器中运行时，还需要其他配置步骤。

1. 请确保在将 Google API 设置为目标的虚拟设备上部署或调试，如下方的 Android 虚拟设备 (AVD) 管理器所示。
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. 单击“应用” > “设置” > “添加帐户”，然后按提示操作将 Google 帐户添加到 Android 设备。
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. 像以前一样运行 todolist 应用并插入一个新的 todo 项。 此时通知区域中会显示一个通知图标。 可以打开通知抽屉查看通知的完整文本。
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android 快速入门]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging 客户端组件]: http://components.xamarin.com/view/GCMClient/
[Azure 移动服务组件]: http://components.xamarin.com/view/azure-mobile-services/



<!--HONumber=Nov16_HO3-->


