---
title: 在 Azure 通知中心中配置百度云推送 |Microsoft Docs
description: 了解如何为 Azure 通知中心配置百度设置。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212515"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>弃用在 Azure 门户中为通知中心配置百度云推送设置

本文介绍如何使用 Azure 门户为 Azure 通知中心配置百度云推送设置。

> [!IMPORTANT]
> 本教程已弃用。 

## <a name="prerequisites"></a>先决条件
如果尚未创建通知中心，请立即创建。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-baidu-cloud-push"></a>配置百度云推送
以下过程提供的步骤演示了如何为通知中心配置百度云推送设置：

1. 在 Azure 门户的“通知中心”页上，在左侧菜单中选择“Baidu (Android China)”。 
2. 在百度云推送项目中，输入从百度控制台获得的 API 密钥。 
3. 在百度云推送项目中，输入从百度控制台获得的机密密钥。 
4. 选择“保存”。 

    ![显示百度 (Android China) 推送通知配置的通知中心屏幕截图](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>后续步骤
如需通过教程的分步说明来了解如何使用 Azure 通知中心和百度云推送将通知推送到百度，请参阅[使用百度的通知中心入门](notification-hubs-baidu-china-android-notifications-get-started.md)。
