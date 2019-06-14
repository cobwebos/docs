---
title: 配置 Google Firebase 云消息传送在 Azure 通知中心 |Microsoft Docs
description: 了解如何使用 Google Firebase Cloud Messaging 设置来配置 Azure 通知中心。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60239189"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 门户中配置通知中心的 Google Firebase Cloud Messaging (FCM) 设置
本文介绍如何使用 Azure 门户配置 Azure 通知中心的 Google Firebase Cloud Messaging (FCM) 设置。  

## <a name="prerequisites"></a>必备组件
如果尚未创建通知中心，请立即创建。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>配置 Google Firebase Cloud Messaging (FCM)

以下过程提供配置通知中心的 Google Firebase Cloud Messaging (FCM) 设置的步骤： 

1. 在 Azure 门户中，在**通知中心**页上，选择**Google (GCM/FCM)** 在左侧菜单中。 
2. 为之前保存的 FCM 项目粘贴“API 密钥”  。 
3. 选择“保存”。  

   ![显示如何为 Google FCM 配置通知中心的屏幕截图](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>后续步骤
有关通过使用 Azure 通知中心和 Google Firebase Cloud Messaging 将通知推送到 Android 设备的分步说明的教程，请参阅[使用通知中心、 Google fcm 等将通知推送到 Android 设备](notification-hubs-android-push-notification-google-fcm-get-started.md).

