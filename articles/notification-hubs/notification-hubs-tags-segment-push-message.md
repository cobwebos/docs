---
title: Azure 通知中心的路由和标记表达式
description: 了解如何为 Azure 通知中心路由和标记表达式。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: b1162e6070deba7f645298b59ffeb1898eb030a8
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545766"
---
# <a name="routing-and-tag-expressions"></a>路由和标记表达式

## <a name="overview"></a>概述

通过通知中心发送推送通知时，使用标记表达式可以定向到特定的设备集或更具体的注册。

## <a name="targeting-specific-registrations"></a>定向到特定注册

定向到特定注册的唯一方法是将这些注册与标记相关联，然后定向到这些标记。 如[注册管理](notification-hubs-push-notification-registration-management.md)中所述，若要接收推送通知，应用必须在通知中心内注册设备句柄。 应用在通知中心创建注册后，应用程序后端可以向其发送推送通知。 应用程序后端可以通过以下方式选择要将特定通知定向到的注册：

1. **广播**：通知中心内的所有注册均将收到通知。
2. **标记**：包含指定标记的所有注册将收到通知。
3. **标记表达式**：其标记集匹配指定表达式的所有注册将收到通知。

## <a name="tags"></a>标记

标记可以是任意字符串，最多120个字符，其中包含字母数字和以下非字母数字字符： "`_`"、"`@`"、"`#`"、"`.`"、"`:`"、"`-`"。 以下示例显示了一个应用程序，可以从中接收有关特定乐队的 toast 通知。 在此方案中，路由通知的一种简单方法是使用表示不同区段的标记来标记注册，如下图所示：

![标记概述](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

在图中，用**Beatles**标记的消息仅转到用标记**Beatles**注册的平板电脑。

有关针对标记创建注册的详细信息，请参阅[注册管理](notification-hubs-push-notification-registration-management.md)。

可以使用 [Microsoft Azure 通知中心](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK 中 `Microsoft.Azure.NotificationHubs.NotificationHubClient` 类的发送通知方法将通知发送到标记。 还可以使用 Node.js 或推送通知 REST API。  下面是使用 SDK 的示例。

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

标记不得预先预配，并且可以引用多个特定于应用的概念。 例如，此示例应用程序的用户可以对乐队发表评论，并且不仅想要接收有关其喜爱乐队的评论的 toast，而且想要接收来自其好友的所有评论（不管他们在对哪个乐队发表评论）的 toast。 下图突出显示了此方案的示例：

![标记朋友](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

在此示例中，Alice 对 Beatles 的更新感兴趣，小明对 Wailers 的更新感兴趣。 Bob 还对 Charlie 的评论感兴趣，而 Charlie 对 Wailers 感兴趣。 当针对 Beatles 上的 Charlie 评论发送通知时，通知中心会将其发送到 Alice 和 Bob。

尽管可以在标记中对多个问题（例如 `band_Beatles` 或 `follows_Charlie`）进行编码，但标记是简单的字符串，而不是具有值的属性。 注册仅匹配特定标记是否存在。

有关如何使用标记发送到相关组的完整分步教程，请参阅[突发新闻](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)。

> [!NOTE]
> Azure 通知中心支持每次注册最多 60 个标记。

## <a name="using-tags-to-target-users"></a>使用标记定向到用户

使用标记的另一种方法是标识与特定用户关联的所有设备。 你可以使用包含用户 ID 的标记来标记注册，如下图所示：

![标记用户](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

在该图中，标记为 `user_Alice` 的邮件到达用 `user_Alice`标记的所有设备。

## <a name="tag-expressions"></a>标记表达式

在某些情况下，通知必须以一组标识的注册为目标，而不是由单个标记标识，而是使用标记的布尔表达式。

考虑这样一个体育应用程序，它将向波士顿的所有用户发送有关红袜队和红雀队之间的比赛的提醒。 如果客户端应用注册了有关感兴趣的球队和位置的标记，则通知应定向到波士顿中对红袜队或红雀队感兴趣的所有用户。 此条件可以用以下布尔表达式表示：

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![标记表达式](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

标记表达式支持常见的布尔运算符，如 `AND` （`&&`）、`OR` （`||`）和 `NOT` （`!`）;它们还可以包含括号。 仅使用 `OR` 运算符的标记表达式可以引用20个标记;否则，标记表达式限制为6个标记。

下面是使用 SDK 通过标记表达式发送通知的示例：

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
