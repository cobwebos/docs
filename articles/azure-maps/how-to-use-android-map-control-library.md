---
title: Azure Maps 中的 Android 地图控件入门 |Microsoft Docs
description: Azure Maps 中的 Android 地图控件。
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: cb79b24a37758307657c1245622fa980123cc5c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432919"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK 入门

Azure Maps Android SDK 是适用于 Android 的矢量地图库。 本文将指导你完成安装 Azure Maps Android SDK 和加载映射的过程。

## <a name="prerequisites"></a>必备组件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

若要完成本文中的过程，首先需要在 S1 定价层中[创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)，并获取帐户的[主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

有关 Azure Maps 中的身份验证的详细信息，请参阅[Azure Maps 中的管理身份验证](./how-to-manage-authentication.md)。

### <a name="download-android-studio"></a>下载 Android Studio

下载 Android Studio 并在安装 Azure Maps Android SDK 之前创建具有空活动的项目。 可以从 Google 免费[下载 Android Studio](https://developer.android.com/studio/) 。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中创建项目

首先，创建一个包含空活动的新项目。 完成以下步骤创建 Android Studio 项目：

1. 在 "**选择项目**" 下，选择 "**手机和平板电脑**"。 应用程序将在此外观上运行。
2. 在 "**手机和平板电脑**" 选项卡上，选择 "**空活动**"，然后选择 "**下一步**"。
3. 在“配置项目”下，选择 `API 21: Android 5.0.0 (Lollipop)` 作为最低要求的 SDK。 这是 Azure Maps Android SDK 支持的最早版本。
4. 接受默认 `Activity Name` 并 `Layout Name` 并选择 "**完成**"。

有关安装 Android Studio 和创建新项目的更多帮助，请参阅[Android Studio 文档](https://developer.android.com/studio/intro/)。

![创建一个项目](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>设置虚拟设备

Android Studio 可让你在计算机上设置 Android 虚拟设备。 这样做可以帮助你在开发过程中测试你的应用程序。 若要设置虚拟设备，请在项目屏幕的右上角选择 "Android 虚拟设备（AVD）管理器" 图标，然后选择 "**创建虚拟设备**"。 还可以通过从工具栏中选择 "**工具**" > **Android** > **AVD Manager**转到 AVD 管理器。 在 "**电话**" 类别中，选择 "**结点 5**"，然后选择 "**下一步**"。

可以在[Android Studio 文档](https://developer.android.com/studio/run/managing-avds)中了解有关设置 AVD 的详细信息。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>安装 Azure Maps Android SDK

构建应用程序的下一步是安装 Azure Maps Android SDK。 完成以下步骤以安装 SDK：

1. 打开顶级**gradle**文件，并将以下代码添加到 "**所有项目**"、"**存储库**" 块部分：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新**应用/gradle** ，并向其中添加以下代码：
    
    1. 请确保项目的**minSdkVersion**为 API 21 或更高版本。

    2. 将以下代码添加到 Android 部分：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 更新依赖项块，并为最新的 Azure Maps Android SDK 添加新的实现依赖项行：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. 在工具栏中，单击 "**文件**"，然后单击 "将**项目与 Gradle 文件同步**"。
3. 将地图片段添加到主活动（res \> 布局 \> 活动\_：
    
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

4. 在**MainActivity**文件中，你将需要：
    
    * 添加 Azure Maps SDK 的导入
    * 设置 Azure Maps 身份验证信息
    * 在**onCreate**方法中获取地图控件实例

    使用 `setSubscriptionKey` 或 `setAadProperties` 方法全局设置 `AzureMaps` 类的身份验证信息，这样就无需在每个视图上添加身份验证信息。 

    Map 控件包含其自身的生命周期方法，用于管理 Android 的 OpenGL 生命周期，该生命周期必须直接从包含的活动中调用。 若要使应用正确地调用地图控件的生命周期方法，必须在包含地图控件的活动中重写以下生命周期方法，并调用各自的地图控制方法。 

    * onCreate （捆绑包） 
    * onStart （） 
    * onResume() 
    * onPause （） 
    * onStop （） 
    * onDestroy （） 
    * onSaveInstanceState （捆绑包） 
    * onLowMemory() 

    编辑**MainActivity**文件，如下所示：
    
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

完成上述步骤后，你可能会收到有关某些代码的 Android Studio 警告。 若要解决这些警告，请导入 `MainActivity.java`中引用的类。

可以通过选择 "Alt + Enter" （Mac 上的选项 + 返回）来自动导入这些类。

选择 "运行" 按钮，如下图所示（或在 Mac 上按 Ctrl + R）生成应用程序。

![单击“运行”](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio 将需要几秒钟时间才能生成应用程序。 完成生成后，可以在模拟的 Android 设备中测试应用程序。 应该会看到类似下面的地图：

<center>

![Android map](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>本地化地图

Azure Maps Android SDK 提供了三种不同的方式来设置地图的语言和区域视图。 下面的代码演示如何将语言设置为法语（"fr"），并将区域视图设置为 "自动"。 

第一种方法是使用静态 `setLanguage` 和 `setView` 全局方法，将语言和区域信息查看到 `AzureMaps` 类。 这会在应用中加载的所有 Azure Maps 控件之间设置默认语言和区域视图。

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

第二种方法是将语言和视图信息传递到地图控件 XML 中。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

第三种方法是使用地图 `setStyle` 方法以编程方式设置地图的语言和区域视图。 可随时完成此操作，以更改地图的语言和区域视图。

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

下面是将语言设置为 "fr-fr" 并将区域视图设置为 "自动" 的 Azure Maps 的示例。

<center>

在法语](./media/how-to-use-android-map-control-library/android-localization.png)
中显示标签 ![地图图像 </center>

[此处](supported-languages.md)介绍了支持的语言和区域视图的完整列表。

## <a name="next-steps"></a>后续步骤

了解如何在地图上添加覆盖数据：

> [!div class="nextstepaction"]
> [向 Android 地图添加符号层](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [将形状添加到 Android 地图](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [更改 Android maps 中的地图样式](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
