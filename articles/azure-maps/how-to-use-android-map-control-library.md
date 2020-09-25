---
title: Android 地图控件入门 | Microsoft Azure Maps
description: 熟悉 Azure Maps Android SDK。 请参阅如何在 Android Studio 中创建项目、安装 SDK 和创建交互式地图。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2cda543781118a19ba4999e774b96ae81857f442
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330921"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK 入门

Azure Maps Android SDK 是适用于 Android 的矢量地图库。 本文将引导你完成安装 Azure Maps Android SDK 和加载地图的过程。

## <a name="prerequisites"></a>先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

若要完成本文中的过程，首先需要在 S1 定价层中[创建一个 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)，并[获取帐户的主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

### <a name="download-android-studio"></a>下载 Android Studio

下载 Android Studio，并在安装 Azure Maps Android SDK 之前创建一个包含空活动的项目。 可以从 Google 免费[下载 Android Studio](https://developer.android.com/studio/)。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中创建项目

首先，请创建一个包含空活动的新项目。 请完成以下步骤创建 Android Studio 项目：

1. 在“Choose your project”（选择项目）下，选择“手机和平板电脑”（Phone and Tablet）。  你的应用程序将在具有此外形规格的设备上运行。
2. 在“Phone and Tablet”（手机和平板电脑）选项卡上选择“Empty  Activity”（空活动），然后选择“Next”（下一步）。  
3. 在“配置项目”下，选择 `API 21: Android 5.0.0 (Lollipop)` 作为最低要求的 SDK。 这是 Azure Maps Android SDK 支持的最旧版本。
4. 接受默认的 `Activity Name` 和 `Layout Name`，然后选择“Finish”（完成）。

安装 Android Studio 和创建新项目时如需更多帮助，请参阅 [Android Studio 文档](https://developer.android.com/studio/intro/)。

![在 Android Studio 中创建项目 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>设置虚拟设备

Android Studio 可让你在计算机上设置 Android 虚拟设备。 这样做有助于在开发期间测试应用程序。 若要设置虚拟设备，请选择项目屏幕右上角的“Android Virtual Device (AVD) Manager”图标，然后选择“Create Virtual Device”（创建虚拟设备）。 也可以通过在工具栏中选择“Tools”（工具） > “Android” > “AVD Manager”打开 AVD Manager。   在“Phones”（手机）类别中选择“Nexus 5X”，然后选择“Next”（下一步）。  

可以在 [Android Studio 文档](https://developer.android.com/studio/run/managing-avds)中详细了解如何设置 AVD。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>安装 Azure Maps Android SDK

生成应用程序的下一步是安装 Azure Maps Android SDK。 请完成以下步骤来安装该 SDK：

1. 打开顶级 **build.gradle** 文件，将以下代码添加到**所有项目**的 **repositories** 块节中：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新 **app/build.gradle** 并在其中添加以下代码：
    
    1. 确保项目的 **minSdkVersion** 设置为 API 21 或更高版本。

    2. 将以下代码添加到 Android 节：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 更新 dependencies 块，并为最新 Azure Maps Android SDK 添加新的实现依赖项行：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.4"
        ```
    
    4. 在工具栏中转到“文件”，然后单击“将项目与 Gradle 文件同步”。 
3. 将一个地图片段添加到 main 活动 (res \> layout \> activity\_main.xml)：
    
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

4. 在 **MainActivity.java** 文件中，需要：
    
    * 添加 Azure Maps SDK 的 import 语句
    * 设置 Azure Maps 身份验证信息
    * 在 **onCreate** 方法中获取地图控件实例

    使用 `setSubscriptionKey` 或 `setAadProperties` 方法在 `AzureMaps` 类中全局设置身份验证信息后，无需在每个视图中添加身份验证信息。 

    地图控件包含自身的生命周期方法用于管理 Android 的 OpenGL 生命周期。 必须直接从包含活动调用这些生命周期方法。 要使应用正确调用地图控件的生命周期方法，必须在包含地图控件的活动中重写以下生命周期方法。 并且，必须调用相应的地图控件方法。 

    * onCreate(Bundle) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
    * onLowMemory() 

    按如下所示编辑 **MainActivity.java** 文件：
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

完成上述步骤后，你可能会收到 Android Studio 针对某些代码发出的警告。 若要解决这些警告，请导入 `MainActivity.java` 中引用的类。

可以按 Alt+Enter（在 Mac 上为 Option+Return）自动导入这些类。

如下图所示选择“运行”按钮（或者在 Mac 上按 Ctrl+R）生成应用程序。

![单击“运行”](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio 将花费几秒钟时间来生成应用程序。 生成完成后，可在 Android 仿真设备中测试应用程序。 应会看到如下所示的地图：

<center>

![Android 应用程序中的 Azure Maps](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>本地化地图

Azure Maps Android SDK 提供三种不同的方式来设置地图的语言和区域视图。 以下代码演示如何将语言设置为法语（“fr-FR”），以及如何将区域视图设置为“auto”。 

第一种做法是全局使用静态的 `setLanguage` 和 `setView` 方法，将语言和视图区域信息传入 `AzureMaps` 类。 这会在载入到应用的所有 Azure Maps 控件中设置默认的语言和区域视图。

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

第二种做法是将语言和视图信息传入地图控件 XML 中。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

第三种做法是使用地图 `setStyle` 方法以编程方式设置地图的语言和区域视图。 随时可以采取这种做法来更改地图的语言和区域视图。

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

下面是将语言设置为“fr-FR”、将区域视图设置为“auto”的 Azure Maps 示例。

<center>

![Azure Maps，显示法语标签的地图图像](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

[此文档](supported-languages.md)提供了支持的语言和区域视图的完整列表。

## <a name="navigating-the-map"></a>在地图中导航

可通过多种不同的方式缩放、平移、旋转地图及调整其俯仰角。 下面详细说明了在地图中导航的所有不同方式。

**缩放地图**

- 使用双指触摸地图，收拢双指可以缩小，分开双指可以放大。
- 双击地图可将地图放大一个级别。
- 用双指双击地图可将地图缩小一个级别。
- 点击两次；在第二次点击时，用手指按住地图并向上拖动可以放大，向下拖动可以缩小。

**平移地图**

- 触摸地图并朝任意方向拖动。

**旋转地图**

- 用双指触摸地图并旋转。

**调整地图俯仰角**

- 用双指触摸地图，并同时向上或向下拖动双指。

## <a name="next-steps"></a>后续步骤

了解如何在地图上添加叠加数据：

> [!div class="nextstepaction"]
> [在 Android 地图中添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [在 Android 地图中添加形状](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [更改 Android 地图中的地图样式](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
