---
title: 如何使用 Android 地图控件中 Azure Maps |Microsoft Docs
description: Azure Maps 中的 Android 地图控件。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010661"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>如何使用 Azure Maps Android SDK

Azure Maps Android SDK 是适用于 Android 的矢量地图库。 本文将指导您完成以下过程安装 Azure Maps Android SDK、 加载映射中，并在代码图上放置一个 pin。

## <a name="prerequisites"></a>必备组件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

若要完成本文中的过程，必须先向[创建 Azure Maps 帐户](how-to-manage-account-keys.md)S1 定价层中。

### <a name="download-android-studio"></a>下载 Android Studio

您需要下载 Android Studio 并创建一个项目具有空的活动，然后才能安装 Azure Maps Android SDK。 你可以[下载 Android Studio](https://developer.android.com/studio/)免费从 Google。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中创建项目

首先，需要使用空活动创建新项目。 完成这些步骤以创建 Android Studio 项目：

1. 下**选择你的项目**，选择**手机和平板电脑**。 你的应用程序将此窗体上运行。
2. 上**手机和平板电脑**选项卡上，选择**空活动**，然后选择**下一步**。
3. 在“配置项目”下，选择 `API 21: Android 5.0.0 (Lollipop)` 作为最低要求的 SDK。 这是 Azure Maps Android SDK 支持的最早版本。
4. 接受默认值`Activity Name`并`Layout Name`，然后选择**完成**。

请参阅[Android Studio 文档](https://developer.android.com/studio/intro/)有关的详细信息帮助安装 Android Studio 以及创建新的项目。

![创建一个项目](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>设置虚拟设备

Android Studio 可让你在计算机上设置 Android 虚拟设备。 执行此操作可帮助您测试应用程序在开发过程。 若要设置虚拟设备，右上角的项目在屏幕上，选择 Android 虚拟设备 (AVD) 管理器图标，然后选择**创建虚拟设备**。 此外可以通过选择获取到 AVD 管理器**工具** > **Android** > **AVD 管理器**从工具栏中。 在中**手机**类别中，选择**Nexus 5 X**，然后选择**下一步**。

您可以了解更多有关设置的 AVD [Android Studio 文档](https://developer.android.com/studio/run/managing-avds)。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK 安装

构建您的应用程序的下一步是安装 Azure Maps Android SDK。 完成这些步骤以安装 SDK:

1. 将以下代码添加到**的所有项目**，**存储库**中阻止你**build.gradle**文件。

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新你**app/build.gradle**并向其添加以下代码：

    1. 将以下代码添加到 Android 块：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. 更新你的依赖项的块，并向其添加以下代码：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. 设置的权限通过添加以下 XML 到您**AndroidManifest.xml**文件：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. 编辑**res** > **布局** > **activity_main.xml**使它看起来像此 XML:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. 编辑 **MainActivity.java** 以创建地图视图活动类。 在编辑后，它应类似此类：

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
            AzureMaps.setSubscriptionKey("{subscription-key}");
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

![Android 地图](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>将标记添加到地图

若要将标记添加到您的映射，将添加`mapView.getMapAsync()`函数来`MainActivity.java`。 最后一个`MainActivity.java`代码应如下所示：

```java
package com.example.myapplication;

import android.app.Activity;
import android.os.Bundle;
import com.mapbox.geojson.Feature;
import com.mapbox.geojson.Point;
import com.microsoft.azure.maps.mapcontrol.AzureMaps;
import com.microsoft.azure.maps.mapcontrol.MapControl;
import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
import com.microsoft.azure.maps.mapcontrol.source.DataSource;
import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
public class MainActivity extends AppCompatActivity {
    
    static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        mapControl.onStart();
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

再次运行应用程序。 应在映射中，看到一个标记，如下所示：

![Android 地图图钉](./media/how-to-use-android-map-control-library/android-map-pin.png)