---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162176"
---
1. 登录到 [Firebase 控制台](https://firebase.google.com/console/)。 如果还没有 Firebase 项目，创建一个新项目。
2. 创建项目后，选择“向 Android 应用添加 Firebase”。 

    ![将 Firebase 添加到 Android 应用](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在“将 Firebase 添加到 Android 应用”页面上，执行以下步骤： 
    1. 对于“Android 程序包名称”，请输入程序包的名称。 例如：`tutorials.tutoria1.xamarinfcmapp`。 

        ![指定程序包名称](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. 选择“注册应用”。 
4. 选择“下载 google-services.json”，将该文件保存到项目的 **app** 文件夹中，然后选择“下一步”。 

    ![下载 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. 在页面上选择“下一步”。 
7. 在页面上选择“跳过此步骤”。 

    ![跳过最后一步](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. 在 Firebase 控制台中，选择与项目相对应的齿轮图标。 然后，选择“项目设置”。

    ![选择“项目设置”](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 如果尚未下载 google-services.json 文件，可以在此页上执行此操作。 
5. 切换到顶部的“Cloud Messaging”选项卡。 
6. 复制并保存**服务器密钥**以供将来使用。 你将使用此值来配置通知中心。
