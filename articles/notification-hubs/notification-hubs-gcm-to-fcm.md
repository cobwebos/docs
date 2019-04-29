---
title: Azure 通知中心和 Google Firebase Cloud Messaging (FCM) 迁移
description: 介绍 Azure 通知中心如何解决 FCM 迁移到 Google GCM。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458291"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure 通知中心和 Google Firebase Cloud Messaging (FCM) 迁移

## <a name="current-state"></a>当前状态

当 Google 宣布其迁移从 Google Cloud Messaging (GCM) 到 Firebase Cloud Messaging (FCM)，我们必须调整如何我们通知发送到 Android 设备以适应此变化等的推送服务。

我们更新我们服务的后端，然后发布到我们的 API 和 Sdk 根据需要更新。 我们的实现，我们决定以保持与现有客户的影响降至最低的 GCM 通知架构兼容性。 这意味着我们目前向在 FCM 旧模式下使用 FCM 的 Android 设备发送通知。 从根本上讲，我们想要添加为支持 FCM，包括新功能和有效负载格式。 这就是长期更改，当前迁移专注于保持与现有的应用程序和 Sdk 兼容性。 可以在你的应用 （以及我们的 SDK) 中使用的 GCM 或 FCM Sdk 和我们要确保正确发送通知。

某些客户最近在 Google 警告中有关应用的通知使用 GCM 终结点收到一封电子邮件。 这是仅是警告，并执行任何操作已中断 – 应用的 Android 通知将仍向 Google 发送进行处理，并 Google 仍处理。 在其服务配置中显式指定 GCM 终结点的某些客户还在使用不推荐使用的终结点。 我们已确定这一缺口，并努力解决此问题时 Google 发送了电子邮件。

我们替换为该不推荐使用的终结点并部署该修补程序。

## <a name="going-forward"></a>今后

Google 的 FCM 常见问题解答说，无需执行任何操作。 在中[FCM 常见问题解答](https://developers.google.com/cloud-messaging/faq)，Google 说"客户端 Sdk 和 GCM 令牌将继续无限期地工作。 但是，你将无法针对 Android 应用程序中的 Google Play 服务的最新版本，除非迁移到了 FCM。"

如果您的应用程序使用 GCM 库，请继续并按照 Google 的说明升级到 FCM 库应用程序中。 我们的 SDK 与都兼容，因此无需更新自己的应用程序中的任何内容 （只要您与我们的 SDK 版本保持最新）。

## <a name="questions-and-answers"></a>问题和解答

下面是一些我们已收到来自客户的常见问题的解答：

**问：** 我需要做的截止日期为兼容 (Google 的当前截止日期为 5 月 29 日，并且可能会更改)？

**答:** 无变化。 我们将保持与现有的 GCM 通知架构兼容性。 GCM 密钥将继续按正常的因为将任何 GCM Sdk 和应用程序所使用的库。

必要时你决定升级到 FCM Sdk 和库，以便充分利用新功能、 GCM 密钥仍将起作用。 您可以切换到使用 FCM 密钥，如果你愿意，但请确保要将 Firebase 添加到现有的 GCM 项目创建新的 Firebase 项目时。 这将保证向后兼容性与运行较旧版本的应用程序仍使用 GCM Sdk 和库的客户。

如果您是创建新的 FCM 项目并使用新的 FCM 密钥更新通知中心后，不附加到现有的 GCM 项目，你将失去的功能将通知推送到你当前的应用程序安装，因为新的 FCM 密钥有没有链接到旧的 GCM项目。

**问：** 为什么会收到有关旧 GCM 终结点正在使用此电子邮件？ 我需要执行什么操作？

**答:** 无变化。 我们已迁移到新的终结点，并将很快完成，因此不更改是必需。 执行任何操作已中断，我们错过了终结点只需从 Google 导致警告消息。

**问：** 我如何可以过渡到新的 FCM Sdk 和库而不会中断现有用户？

答：在任何时候升级。 Google 尚未宣布任何不推荐使用的现有 GCM Sdk 和库。 若要确保不中断到你的现有用户的推送通知，请确保您创建新 Firebase 项目将与你现有的 GCM 项目关联。 这将确保新 Firebase 机密将适用于与 GCM Sdk 和库，运行您的应用程序的较旧版本的用户，以及使用 FCM Sdk 和库对应用程序的新用户。

**问：** 对于我的通知，何时可以使用 FCM 的新功能和架构？

**答:** 一旦我们将更新发布到我们的 API 和 Sdk，请继续关注-我们希望在未来几个月中有一些给您。
