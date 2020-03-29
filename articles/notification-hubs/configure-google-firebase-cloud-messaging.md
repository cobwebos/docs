---
title: 在 Azure 通知中心配置 Google 火库云消息 |微软文档
description: 了解如何使用 Google Firebase 云消息设置配置 Azure 通知中心。
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127472"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>为 Azure 门户中的通知中心配置 Google Firebase 设置

本文介绍如何使用 Azure 门户为 Azure 通知中心配置 Google Firebase 云消息传递 （FCM） 设置。  

## <a name="prerequisites"></a>先决条件
如果尚未创建通知中心，请立即创建。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>配置谷歌火库云消息 （FCM）

以下过程为您提供了为通知中心配置 Google Firebase 云消息 （FCM） 设置的步骤： 

1. 在 Azure 门户中，在**通知中心**页上，选择左侧菜单上的**Google （GCM/FCM）。** 
2. 为之前保存的 FCM 项目粘贴“API 密钥”****。 
3. 选择“保存”。**** 

   ![显示如何为 Google FCM 配置通知中心的屏幕截图](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>后续步骤
有关使用 Azure 通知中心和 Google Firebase 云消息推送通知到 Android 设备的分步说明的教程，[请参阅使用通知中心和 Google FCM 将通知推送到 Android 设备](notification-hubs-android-push-notification-google-fcm-get-started.md)。

