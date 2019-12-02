---
title: 向 Xamarin.Android 应用添加推送通知
description: 了解如何使用 Azure App Service 和 Azure 通知中心向 Xamarin Android 应用程序发送推送通知。
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c9dc4c825d65287f152522868a2b9e6a38ea70bb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668748"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>向 Xamarin.Android 应用添加推送通知

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”、“测试”和“分发”服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用“分析”和“诊断”服务监视其应用的状态和使用情况，并使用“推送”服务吸引用户。 开发人员还可以利用“身份验证”对其用户进行身份验证，并使用“数据”服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

## <a name="overview"></a>概述

本教程介绍如何向 [Xamarin.Android 快速入门](app-service-mobile-windows-store-dotnet-get-started.md)项目添加推送通知，以便每次插入一条记录时，都向设备发送一条推送通知。

如果不使用下载的快速入门服务器项目，则需要推送通知扩展包。 有关详细信息，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 指南。

## <a name="prerequisites"></a>必备组件

本教程需要设置：

* 有效的 Google 帐户 可以在 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) 注册 Google 帐户。
* [Google Cloud Messaging 客户端组件](https://components.xamarin.com/view/GCMClient/)。

## <a name="configure-hub"></a>配置通知中心

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>启用 Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>配置 Azure 以发送推送请求

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>更新服务器项目以发送推送通知

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>针对推送通知配置客户端项目

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>向应用添加推送通知代码

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>在应用中测试推送通知

可以使用模拟器中的虚拟设备测试应用。 在模拟器中运行时，还需要其他配置步骤。

1. 虚拟设备必须将 Google API 设置为 Android 虚拟设备 (AVD) 管理器中的目标。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. 单击“应用” > “设置” > “添加帐户”，然后按提示操作将 Google 帐户添加到 Android 设备。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. 像以前一样运行 todolist 应用并插入一个新的 todo 项。 此时通知区域中会显示一个通知图标。 可以打开通知抽屉查看通知的完整文本。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
