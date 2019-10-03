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
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509115"
---
1. 在 Android Studio 中，在菜单上选择“工具”  ，然后选择“SDK 管理器”  。 
2. 选择项目中使用的 Android SDK 的目标版本。 然后选择“显示包详细信息”  。 

    ![Android SDK 管理器 - 选择目标版本](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 选择“Google API”  （如果尚未安装）。

    ![Android SDK 管理器 - 选择了“Google API”](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. 切换到“SDK 工具”选项卡。  如果尚未安装 Google Play Services，请选择“Google Play Services”，如下图所示。  然后，选择“应用”  以进行安装。 记下 SDK 路径，因为后面的步骤将要用到。

    ![Android SDK 管理器 - 选择了“Google Play 服务”](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. 如果看到了“确认更改”  对话框，请选择“确定”  。 组件安装程序将安装所请求的组件。 在安装组件后，选择“完成”  。
4. 选择“确定”  以关闭“新项目的设置”  对话框。  
5. 打开 **app** 目录中的 build.gradle 文件，然后在 `dependencies` 下添加以下行。 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. 选择工具栏中的“立即同步”  图标。

    ![通过 Gradle 进行同步](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. 打开 AndroidManifest.xml 文件，然后将以下标记添加到应用程序  标记。

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
