---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935107"
---
1. 登录到 [Firebase 控制台](https://firebase.google.com/console/)。 如果还没有 Firebase 项目，创建一个新项目。
2. 创建项目后，选择“向 Android 应用添加 Firebase”。  

    ![将 Firebase 添加到 Android 应用](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在“将 Firebase 添加到 Android 应用”  页上，执行以下步骤： 
    1. 对于“Android 程序包名称”  ，请在应用程序的 build.gradle 文件中复制 **applicationId** 的值。 在此示例中，它是 `com.fabrikam.fcmtutorial1app`。 

        ![指定程序包名称](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. 选择“注册应用”  。 
4. 选择“下载 google-services.json”  ，将该文件保存到项目的 **app** 文件夹中，然后选择“下一步”  。 

    ![下载 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. 在 Android Studio 中对你的项目进行以下**配置更改**。 
    1.  在项目级 build.gradle 文件 (&lt;project&gt;/build.gradle) 中，向 **dependencies** 部分中添加以下语句。 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. 在应用级 build.gradle 文件 (&lt;project&gt;/&lt;app-module&gt;/build.gradle) 中，向 **dependencies** 部分中添加以下语句。 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. 在应用级 build.gradle 文件的末尾，在 dependencies 部分后面添加以下行。 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. 在工具栏上选择“立即同步”。  
 
        ![build.gradle 配置更改](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. 选择“**下一步**”。 
7. 选择“跳过此步骤”  。 

    ![跳过最后一步](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. 在 Firebase 控制台中，选择与项目相对应的齿轮图标。 然后，选择“项目设置”。 

    ![选择“项目设置”](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 如果尚未将 google-services.json 文件下载到 Android Studio 项目的 **app** 文件夹中，则可以在该页上执行此操作。 
5. 切换到顶部的“Cloud Messaging”选项卡。  
6. 复制并保存**服务器密钥**以供将来使用。 此值将用于配置中心。
