---
title: 开始使用 Android 地图控件 |微软 Azure 地图
description: 在本文中，您将学习如何使用 Microsoft Azure 地图 Android SDK 开始使用 Android 地图控件。
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 55bfb5030d0a168e7556240212fcd5f3be30a289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335370"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>开始使用 Azure 地图 Android SDK

Azure 地图 Android SDK 是 Android 的矢量映射库。 本文将指导您完成安装 Azure 地图 Android SDK 和加载地图的过程。

## <a name="prerequisites"></a>先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

要完成本文中的过程，首先需要在 S1 定价层中[创建 Azure 地图帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)，并[获取帐户的主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

### <a name="download-android-studio"></a>下载安卓工作室

在安装 Azure 地图 Android SDK 之前，下载 Android Studio 并创建活动为空的项目。 你可以从谷歌免费[下载Android工作室](https://developer.android.com/studio/)。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中创建项目

首先，创建一个活动为空的新项目。 完成这些步骤以创建 Android 工作室项目：

1. 在 **"选择您的项目**"下，选择 **"手机"和"平板电脑**"。 您的应用程序将在此外形上运行。
2. 在 **"电话和平板电脑"** 选项卡上，选择 **"空活动**"，然后选择 **"下一步**"。
3. 在“配置项目”下，选择 `API 21: Android 5.0.0 (Lollipop)` 作为最低要求的 SDK。**** 这是 Azure 地图 Android SDK 支持的最早版本。
4. 接受默认值`Activity Name`并`Layout Name`选择 **"完成**"。

有关安装 Android Studio 和创建新项目的更多帮助，请参阅[Android Studio 文档](https://developer.android.com/studio/intro/)。

![在 Android 工作室中创建项目 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>设置虚拟设备

Android Studio 可让你在计算机上设置 Android 虚拟设备。 这样做可以帮助您在开发期间测试应用程序。 要设置虚拟设备，请在项目屏幕右上角选择 Android 虚拟设备 （AVD） 管理器图标，然后选择"**创建虚拟设备**"。 您还可以通过从工具栏中选择**工具** > **Android** > **AVD 管理器**来访问 AVD 管理器。 在 **"电话"** 类别中，选择**Nexus 5X**，然后选择 **"下一步**"。

你可以了解更多关于在[Android工作室文档中](https://developer.android.com/studio/run/managing-avds)设置AVD。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>安装 Azure 地图 Android SDK

构建应用程序的下一步是安装 Azure 映射 Android SDK。 完成这些步骤以安装 SDK：

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
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. 在工具栏中转到“文件”，然后单击“将项目与 Gradle 文件同步”。********
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
    * 获取**onCreate**方法中的地图控制实例

    使用 `setSubscriptionKey` 或 `setAadProperties` 方法在 `AzureMaps` 类中全局设置身份验证信息后，无需在每个视图中添加身份验证信息。 

    地图控件包含自身的生命周期方法用于管理 Android 的 OpenGL 生命周期。 这些生命周期方法必须直接从包含活动调用。 要使应用正确调用地图控件的生命周期方法，必须覆盖包含地图控件的"活动"中的以下生命周期方法。 而且，您必须调用相应的地图控件方法。 

    * 打开创建（捆绑） 
    * 开始（） 
    * 上简历（） 
    * 上暂停（） 
    * 上停止（） 
    * 上销毁（） 
    * 在保存实例状态（捆绑） 
    * 在低内存（） 

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

完成上述步骤后，您可能会收到来自 Android Studio 有关某些代码的警告。 要解决这些警告，导入 中引用的`MainActivity.java`类。

您可以通过选择 Alt_Enter（选项+在 Mac 上返回）自动导入这些类。

选择运行按钮（如下图所示（或在 Mac 上按 Control+R）来构建应用程序。

![单击“运行”](./media/how-to-use-android-map-control-library/run-app.png)

Android工作室将需要几秒钟来构建应用程序。 生成完成后，您可以在模拟的 Android 设备中测试应用程序。 您应该会看到如下所示的地图：

<center>

![Android 应用程序中的 Azure 映射](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>本地化地图

Azure 地图 Android SDK 提供了三种不同的方法来设置地图的语言和区域视图。 以下代码演示如何将语言设置为法语（"fr-FR"），区域视图设置为"自动"。 

第一个选项是使用静态`AzureMaps``setLanguage`和方法`setView`全局传递语言并将区域信息传递到类中。 这将在应用中加载的所有 Azure 地图控件中设置默认语言和区域视图。

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

下面是 Azure 地图的示例，其语言设置为"fr-FR"，区域视图设置为"自动"。

<center>

![Azure 地图，以法语显示标签的地图图像](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

[此文档](supported-languages.md)提供了支持的语言和区域视图的完整列表。

## <a name="next-steps"></a>后续步骤

了解如何在地图上添加叠加数据：

> [!div class="nextstepaction"]
> [向 Android 地图添加符号图层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [将形状添加到 Android 地图](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [更改 Android 地图中的地图样式](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
