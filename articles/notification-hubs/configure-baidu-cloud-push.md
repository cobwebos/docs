---
title: 在 Azure 通知中心中配置百度云推送 |Microsoft Docs
description: 了解如何配置 Azure 通知中心的百度设置。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488212"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 门户中配置通知中心的百度云推送设置
本文介绍如何使用 Azure 门户配置 Azure 通知中心的百度云推送设置。 

## <a name="prerequisites"></a>必备组件
如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-baidu-cloud-push"></a>配置百度云推送
以下过程提供配置通知中心的百度云推送设置的步骤：

1. 在 Azure 门户中，在**通知中心**页上，选择**百度 (Android China)** 在左侧菜单中。 
2. 在百度云推送项目中，输入从百度控制台获取的“API 密钥”。 
3. 在百度云推送项目中，输入从百度控制台获取的“机密密钥”。 
4. 选择“保存”。 

    ![通知中心的屏幕截图，其中显示了百度 (Android China) 的推送通知配置](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>后续步骤
有关将通知推送到百度使用 Azure 通知中心和百度云推送的分步说明的教程，请参阅[通过百度开始使用通知中心](notification-hubs-baidu-china-android-notifications-get-started.md)。
