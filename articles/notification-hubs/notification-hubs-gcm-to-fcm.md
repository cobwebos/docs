---
title: Azure 通知中心和 Google Firebase Cloud 消息（FCM）迁移
description: 介绍 Azure 通知中心如何将 Google GCM 寻址到 FCM 迁移。
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 80eae09240bde61870995468485338db5f0b9c2d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212318"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure 通知中心和 Google Firebase Cloud 消息（FCM）迁移

## <a name="current-state"></a>当前状态

当 Google 公布从 Google Cloud Messaging （GCM）到 Firebase 云消息传送（FCM）的迁移时，像我们这样的推送服务必须调整将通知发送到 Android 设备以适应变化的方式。

我们更新了服务后端，并根据需要将更新发布到我们的 API 和 Sdk。 利用我们的实现方式，我们决定保持与现有 GCM 通知架构的兼容性，以最大程度地减少对客户的影响。 这意味着我们目前使用 FCM 旧版模式下的 FCM 将通知发送到 Android 设备。 最终，我们想要添加对 FCM 的真正支持，包括新功能和有效负载格式。 这是一项较长的更改，当前迁移的重点是保持与现有应用程序和 Sdk 的兼容性。 你可以在应用中使用 GCM 或 FCM Sdk （与我们的 SDK 配合使用），并确保通知发送正确。

一些客户最近收到了来自 Google 的一封电子邮件，其中介绍了有关使用 GCM 终结点通知的应用。 这只是一条警告，不会有任何损坏–应用的 Android 通知仍会发送到 Google 进行处理，Google 仍会处理它们。 某些已在其服务配置中明确指定 GCM 终结点的客户仍在使用已弃用的终结点。 我们已经认识到了这种缺口，并致力于在 Google 发送电子邮件时解决问题。

我们取代了弃用的终结点，并部署了修补程序。

## <a name="going-forward"></a>今后

Google 的 FCM FAQ 指出，无需执行任何操作。 在[FCM FAQ](https://developers.google.com/cloud-messaging/faq)中，Google 说 "客户端 SDK 和 GCM 令牌将继续无限期地工作。 但是，除非迁移到 FCM，否则不能在 Android 应用程序中定位最新版本的 Google Play Services。

如果你的应用使用 GCM 库，请继续操作并按照 Google 的说明升级到你的应用中的 FCM 库。 我们的 SDK 与兼容，因此你无需在一侧就更新应用程序中的任何内容（只要你使用的 SDK 版本是最新版本）。

## <a name="questions-and-answers"></a>问题和解答

下面是我们为客户提供的一些常见问题的解答：

**问：** 需要执行哪些操作才能与截止日期兼容（Google 当前截止日期可能为29，可能会更改）？

**答：** 无变化。 我们将保持与现有 GCM 通知架构的兼容性。 与应用程序使用的任何 GCM Sdk 和库一样，GCM 密钥仍能正常工作。

如果你决定升级到 FCM Sdk 和库以利用新功能，你的 GCM 密钥仍将正常工作。 如果需要，你可以切换到使用 FCM 键，但请确保在创建新的 Firebase 项目时向现有 GCM 项目添加 Firebase。 这将保证与正在运行旧版本应用的客户的向后兼容性，仍使用 GCM Sdk 和库。

如果要创建新的 FCM 项目，而不是附加到现有的 GCM 项目，一旦使用新的 FCM 密码更新通知中心，就会失去将通知推送到当前应用安装的功能，因为新的 FCM 项没有到旧 GCM 的链接投影.

**问：** 为什么要收到有关使用旧 GCM 终结点的这封电子邮件？ 我需要做些什么？

**答：** 无变化。 我们已经迁移到新的终结点，即将完成，因此不需要进行任何更改。 不会有任何中断，我们缺少一个终结点，只是导致来自 Google 的警告消息。

**问：** 如何在不破坏现有用户的情况下切换到新的 FCM Sdk 和库？

答：随时升级。 Google 尚未公布现有 GCM Sdk 和库的任何弃用。 若要确保不会向现有用户中断推送通知，请确保在创建新的 Firebase 项目时，将其与现有 GCM 项目相关联。 这将确保使用 GCM Sdk 和库为运行较旧版本的应用的用户以及使用 FCM Sdk 和库的应用的新用户提供新的 Firebase 机密。

**问：** 何时可以使用新的 FCM 功能和架构来接收通知？

**答：** 将更新发布到我们的 API 和 Sdk 后，请继续进行优化–我们预计会在未来的几个月内提供一些内容。
