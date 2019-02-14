---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fb27386881e89cd9056d0efccb7d3c301867bd83
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965981"
---
1. 在 **Android Studio** 中，在菜单上选择“工具”，然后选择“SDK 管理器”。 
2. 选择你的项目中使用的 Android SDK 的目标版本，然后选择“显示包详细信息”。 

    ![Android SDK 管理器 - 选择目标版本](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 选择“Google API”（如果尚未安装）。

    ![Android SDK 管理器 - 选择了“Google API”](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. 切换到“SDK 工具”选项卡。如果尚未安装 Google Play 服务，请选择“Google Play 服务”，如下图所示。 然后，单击“应用”以进行安装。 记下 SDK 路径，因为后面的步骤将要用到。

    ![Android SDK 管理器 - 选择了“Google Play 服务”](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. 如果看到了“确认更改”对话框，请选择“确定”。 组件安装程序将安装所请求的组件。 在安装组件后，选择“完成”。
4. 选择“确定”以关闭“新项目的设置”对话框。  
5. 打开 **app** 目录中的 `build.gradle` 文件，并在 `dependencies` 下添加此行。 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. 选择工具栏中的“立即同步”图标。

    ![通过 Gradle 进行同步](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. 打开 **AndroidManifest.xml** 并将以下标记添加到 *application* 标记中。

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
