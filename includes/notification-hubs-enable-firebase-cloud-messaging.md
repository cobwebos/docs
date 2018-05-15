---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4dc67175f2ccc569ab6dfe5338607fa6bebe3882
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
1. 登录到 [Firebase 控制台](https://firebase.google.com/console/)。 如果还没有 Firebase 项目，创建一个新项目。
2. 创建项目后，选择“向 Android 应用添加 Firebase”。 然后按提供的说明操作。 下载 **google-services.json** 文件。 

    ![将 Firebase 添加到 Android 应用](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在 Firebase 控制台中，选择与项目相对应的齿轮图标。 然后，选择“项目设置”。

    ![选择“项目设置”](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 在项目设置中选择“常规”选项卡。 然后下载包含服务器 API 密钥和客户端 ID 的 **google-services.json** 文件。
5. 选择项目设置中的“Cloud Messaging”选项卡，然后复制“旧服务器密钥”的值。 请使用此值配置通知中心访问策略。
