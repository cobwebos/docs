---
title: 如何发送预定通知 | Microsoft 文档
description: 本主题介绍如何使用 Azure 通知中心发送预定通知。
services: notification-hubs
documentationcenter: .net
keywords: 推送通知,push notification,计划推送通知
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776971"
---
# <a name="how-to-send-scheduled-notifications"></a>如何：发送预定通知
## <a name="overview"></a>概述
如果在你的方案中，需要在将来的某个时间点发送通知，但却无法轻松地唤醒后端代码来发送该通知。 标准层通知中心支持安排最多未来 7 天的通知。

发送通知时，只需如以下示例中所示，使用通知中心 SDK 中的 [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) 类：

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

此外，可以使用其 notificationId 取消以前计划的通知：

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

可以发送的预定通知数没有限制。

