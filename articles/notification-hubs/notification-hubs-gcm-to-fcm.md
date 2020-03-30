---
title: Azure 通知中心和 Google 火库云消息 （FCM） 迁移
description: 描述 Azure 通知中心如何解决 Google GCM 到 FCM 迁移。
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
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127025"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure 通知中心和 Google 火库云消息迁移

## <a name="current-state"></a>当前状态

当 Google 宣布从 Google 云消息 （GCM） 迁移到 Firebase 云消息 （FCM） 时，像我们这样的推送服务必须调整我们向 Android 设备发送通知的方式以适应更改。

我们更新了我们的服务后端，然后根据需要发布了 API 和 SDK 的更新。 通过我们的实施，我们决定保持与现有 GCM 通知架构的兼容性，以尽量减少客户影响。 这意味着我们目前在 FCM 旧模式下使用 FCM 向 Android 设备发送通知。 最终，我们希望添加对 FCM 的真正支持，包括新功能和有效负载格式。 这是一个长期的变化，当前的迁移侧重于保持与现有应用程序和 SDK 的兼容性。 您可以在应用中使用 GCM 或 FCM SDK（以及我们的 SDK），并确保通知发送正确。

一些客户最近收到来自 Google 的电子邮件，警告使用 GCM 终结点的应用程序进行通知。 这只是一个警告，没有什么被打破 - 你的应用程序的Android通知仍然发送到谷歌处理，谷歌仍然处理他们。 一些在其服务配置中显式指定 GCM 终结点的客户仍在使用弃用终结点。 我们已经发现了这个差距，并在谷歌发送电子邮件时正在努力解决这个问题。

我们替换了已弃用的终结点，并部署了修复程序。

## <a name="going-forward"></a>展望未来

谷歌的FCM常见问题解答说，你不必做任何事情。 在[FCM常见问题解答](https://developers.google.com/cloud-messaging/faq)中，谷歌表示"客户端 SDK 和 GCM 令牌将继续无限期工作。 但是，除非您迁移到 FCM，否则您将无法在 Android 应用中定位最新版本的 Google Play 服务。

如果您的应用使用 GCM 库，请继续按照 Google 的说明升级到应用中的 FCM 库。 我们的 SDK 与任一版本兼容，因此您不必更新我们方应用中的任何内容（只要您与我们的 SDK 版本保持最新）。

## <a name="questions-and-answers"></a>问题和解答

以下是我们从客户那里听到的常见问题的一些答案：

**问：** 我需要做什么才能在截止日期前兼容（谷歌当前的截止日期是5月29日，可能会改变）？

**答：** 什么。 我们将保持与现有 GCM 通知架构的兼容性。 您的 GCM 密钥将继续正常工作，应用程序使用的任何 GCM SDK 和库也将照常工作。

如果您决定升级到 FCM SDK 和库以利用新功能，则 GCM 密钥仍然有效。 如果需要，可以切换到使用 FCM 密钥，但在创建新的 Firebase 项目时，请确保将 Firebase 添加到现有 GCM 项目。 这将保证与运行旧版本的应用且仍使用 GCM SDK 和库的客户向后兼容。

如果要创建新的 FCM 项目，但未附加到现有的 GCM 项目，一旦使用新的 FCM 密钥更新通知中心，您将失去将通知推送到当前应用安装的能力，因为新的 FCM 密钥没有指向旧 GCM 的链接项目。

**问：** 为什么我收到有关正在使用的旧 GCM 终结点的电子邮件？ 我该怎么做？

**答：** 什么。 我们一直在迁移到新的终结点，并将很快完成，因此无需更改。 没有什么被打破，我们一个错过的终端只是引起了来自谷歌的警告消息。

**问：** 如何在不中断现有用户的情况下过渡到新的 FCM SDK 和库？

答：随时升级。 谷歌尚未宣布任何对现有GCM SDK和图书馆的弃用。 为了确保不会中断对现有用户的推送通知，请确保在创建新的 Firebase 项目时，您要与现有的 GCM 项目关联。 这将确保新的 Firebase 机密适用于使用 GCM SDK 和库运行应用旧版本的用户，以及使用 FCM SDK 和库的应用的新用户。

**问：** 何时可以使用新的 FCM 功能和架构来进行通知？

**答：** 发布 API 和 SDK 的更新后，请继续关注我们 - 我们希望在未来几个月内为您提供一些服务。
