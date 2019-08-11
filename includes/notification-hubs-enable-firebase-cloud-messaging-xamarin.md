---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728821"
---
1. 登录到 [Firebase 控制台](https://firebase.google.com/console/)。 如果还没有 Firebase 项目，创建一个新项目。
2. 创建项目后，选择“向 Android 应用添加 Firebase”。  

    ![将 Firebase 添加到 Android 应用](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. 在“将 Firebase 添加到 Android 应用”  页上，执行以下步骤： 
    1. 对于“Android 包名称”，请输入包的名称  。 例如：`tutorials.tutoria1.xamarinfcmapp`。 

        ![指定程序包名称](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. 选择“注册应用”  。  
    1. 选择“下载 google-services.json”  。 然后将文件保存到项目的文件夹中，然后选择“下一步”  。 如果尚未创建 Visual Studio 项目，则可以在创建项目后执行此步骤。 

        ![下载 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. 选择“**下一步**”。 
    7. 选择“跳过此步骤”  。 

        ![跳过最后一步](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. 在 Firebase 控制台中，选择与项目相对应的齿轮图标。 然后，选择“项目设置”。 

    ![选择“项目设置”](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 如果尚未下载 google-services.json 文件，可以在此页上进行下载  。 

    ![从“常规”选项卡下载 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. 切换到顶部的“Cloud Messaging”选项卡。  复制并保存**服务器密钥**以供将来使用。 你将使用此值来配置通知中心。

    ![复制服务器密钥](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
