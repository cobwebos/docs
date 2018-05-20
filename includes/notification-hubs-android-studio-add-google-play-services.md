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
ms.openlocfilehash: 540c9775ae56798ce2d2d87fed4924244c31412f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
1. 通过单击 Android Studio 工具栏上的图标，或者通过单击“工具” > “Android” > “SDK Manager”，打开 Android SDK Manager。 找到项目中使用的 Android SDK 目标版本，单击“显示包详细信息”将它打开，然后选择“Google API”（如果尚未安装）。
2. 单击“SDK 工具”选项卡。如果尚未安装 Google Play 服务，请单击“Google Play 服务”，如下所示。 然后，单击“应用”以进行安装。 
   
    记下 SDK 路径，因为后面的步骤将要用到。 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 打开应用目录中的 **build.gradle** 文件。
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. 在 *dependencies*下面添加以下行： 
    
    ```java
        compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. 在工具栏中，单击“将项目与 Gradle 文件同步”图标。
6. 打开 **AndroidManifest.xml** 并将以下标记添加到 *application* 标记中。
   
    ```java
    <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
    ```

