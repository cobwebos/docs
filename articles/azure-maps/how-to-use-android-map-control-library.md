---
title: 开始使用 Azure Maps 中的 Android 地图控件 |Microsoft Docs
description: Azure Maps 中的 Android 地图控件。
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9df5eb9fa4493f82c6efd4a8e30eee324e4eac2a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273833"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>开始使用 Azure Maps Android SDK

Azure Maps Android SDK 是适用于 Android 的矢量地图库。 本文将指导你完成安装 Azure Maps Android SDK 和加载映射的过程。

## <a name="prerequisites"></a>必备组件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

若要完成本文中的过程，必须先向[创建 Azure Maps 帐户](how-to-manage-account-keys.md)S1 定价层中。

### <a name="download-android-studio"></a>下载 Android Studio

您需要下载 Android Studio 并创建一个项目具有空的活动，然后再安装 Azure Maps Android SDK。 你可以[下载 Android Studio](https://developer.android.com/studio/)免费从 Google。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中创建项目

首先，需要使用空活动创建新项目。 完成这些步骤以创建 Android Studio 项目：

1. 下**选择你的项目**，选择**手机和平板电脑**。 你的应用程序将此窗体上运行。
2. 上**手机和平板电脑**选项卡上，选择**空活动**，然后选择**下一步**。
3. 在“配置项目”下，选择 `API 21: Android 5.0.0 (Lollipop)` 作为最低要求的 SDK。  这是 Azure Maps Android SDK 支持的最早版本。
4. 接受默认值`Activity Name`并`Layout Name`，然后选择**完成**。

请参阅[Android Studio 文档](https://developer.android.com/studio/intro/)有关的详细信息帮助安装 Android Studio 以及创建新的项目。

![创建一个项目](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>设置虚拟设备

Android Studio 可让你在计算机上设置 Android 虚拟设备。 执行此操作可帮助您测试应用程序在开发过程。 若要设置虚拟设备，右上角的项目在屏幕上，选择 Android 虚拟设备 (AVD) 管理器图标，然后选择**创建虚拟设备**。 此外可以通过选择获取到 AVD 管理器**工具** > **Android** > **AVD 管理器**从工具栏中。 在中**手机**类别中，选择**Nexus 5 X**，然后选择**下一步**。

您可以了解更多有关设置的 AVD [Android Studio 文档](https://developer.android.com/studio/run/managing-avds)。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK 安装

构建您的应用程序的下一步是安装 Azure Maps Android SDK。 完成这些步骤以安装 SDK:

1. 打开的顶级**build.gradle**文件，并将以下代码添加到**的所有项目**，**存储库**阻止部分：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新你**app/build.gradle**并向其添加以下代码：
    
    1. 请确保你的项目**minSdkVersion**是 API 21 或更高版本。

    2. 将以下代码添加到 Android 部分：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 更新你的依赖项的块并为最新的 Azure Maps Android SDK 添加新的实现依赖关系行：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Azure Maps Android SDK 定期正在升级和增强。 您可以看到[开始使用 Android 地图控件](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)文档，以获取最新的 Azure Maps 实现版本号。 此外，可以设置从"0.2"的版本号为"0 +"，使其始终指向最新版本。

3. 编辑**res** > **布局** > **activity_main.xml**和用以下代码替换它：
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. 在中**MainActivity.java**你将需要的文件：
    
    * 添加导入适用于 Azure Maps SDK
    * 设置 Azure Maps 身份验证信息
    * 获取地图控件实例中**onCreate**方法

    全局使用 setSubscriptionKey 或 setAadProperties 方法 AzureMaps 类上设置的身份验证信息使它，因此无需对每个视图中添加身份验证信息。 地图控件包含用于管理 Android 的 OpenGL 生命周期，这必须直接从包含的活动中调用其自身生命周期方法。 为了使您的应用程序正确，调用该地图控件的生命周期方法，必须重写包含地图控件的活动中的以下生命周期方法，并调用相应的地图控件方法。 

    编辑**MainActivity.java**文件，如下所示：
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }

    }

    ```

## <a name="import-classes"></a>导入类

完成上述步骤后，你将可能获取警告 Android Studio 有关的一些代码。 若要解决这些警告，导入中引用的类`MainActivity.java`。

您可以通过选择 Alt + Enter （选项 + 返回在 Mac 上） 会自动导入这些类。

选择运行按钮，如以下图 （或按在 Mac 上的控件 + R），生成应用程序中所示。

![单击“运行”](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio 中将需要几秒钟才能生成应用程序。 生成完成后，你可以测试应用程序中仿真的 Android 设备。 应看到类似如下的映射：

<center>

![Android 映射](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>后续步骤

了解如何在地图上添加覆盖数据：

> [!div class="nextstepaction"]
> [将符号层添加到 Android 映射](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [将形状添加到 Android 映射](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [在 Android 映射中的更改地图样式](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
