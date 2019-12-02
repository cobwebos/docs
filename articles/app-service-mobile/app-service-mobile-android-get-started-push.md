---
title: 向 Android 应用添加推送通知
description: 了解如何使用移动应用将推送通知发送到 Android 应用。
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ce4ebe9e8874e779b8da16e9c30fcfc3ca46754e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668614"
---
# <a name="add-push-notifications-to-your-android-app"></a>向 Android 应用添加推送通知

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”、“测试”和“分发”服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用“分析”和“诊断”服务监视其应用的状态和使用情况，并使用“推送”服务吸引用户。 开发人员还可以利用“身份验证”对其用户进行身份验证，并使用“数据”服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

## <a name="overview"></a>概述

本教程介绍如何向 [Android 快速入门]项目添加推送通知，以便每次插入一条记录时，向设备发送一条推送通知。

如果不使用下载的快速入门服务器项目，则需要推送通知扩展包。 有关详细信息，请参阅[使用用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="prerequisites"></a>必备组件

需要满足以下条件：

* 依赖于项目后端的 IDE：

  * 如果该应用后端为 Node.js，则需要 [Android Studio](https://developer.android.com/sdk/index.html)。
  * 如果该应用后端为 Microsoft .NET，则需要 [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 或更高版本。
* Android 2.3 或更高版本、Google Repository 修订版 27 或更高版本以及适用于 Firebase Cloud Messaging 的 Google Play Services 9.0.2 或更高版本。
* 完成 [Android 快速入门]。

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>创建支持 Google Cloud Messaging 的项目

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>配置通知中心

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>配置 Azure 以发送推送通知

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>启用服务器项目的推送通知

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>向你的应用程序添加推送通知

本部分将更新客户端 Android 应用，以处理推送通知。

### <a name="verify-android-sdk-version"></a>验证 Android SDK 版本

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

下一步就是安装 Google Play Services。 Firebase Cloud Messaging 针对开发和测试有一些最低的 API 级别要求，清单中的 minSdkVersion 属性必须符合这些要求。

如果要测试一台较旧的设备，请查阅[将 Firebase 添加到 Android 项目]，以确定此值可设置到的最小值，并相应地进行设置。

### <a name="add-firebase-cloud-messaging-to-the-project"></a>将 Firebase Cloud Messaging 添加到项目

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>添加代码

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>针对发布的移动服务测试应用程序

可以通过以下方式测试应用程序：使用 USB 电缆直接连接 Android 手机，或者在模拟器中使用虚拟设备。

## <a name="next-steps"></a>后续步骤

完成此教程后，请考虑继续学习以下教程之一：

* [将身份验证添加到 Android 应用](app-service-mobile-android-get-started-users.md)。
  介绍如何使用支持的标识提供者将身份验证添加到 Android 上的待办事项列表快速入门项目。
* [为 Android 应用启用脱机同步](app-service-mobile-android-get-started-offline-data.md)。
  了解如何使用移动应用后端向应用添加脱机支持。 使用脱机同步，用户可以与移动应用进行交互&mdash;查看、添加或修改数据&mdash;，即使在没有网络连接时也是如此。

<!-- URLs -->
[Android 快速入门]: app-service-mobile-android-get-started.md
[将 Firebase 添加到 Android 项目]: https://firebase.google.com/docs/android/setup
