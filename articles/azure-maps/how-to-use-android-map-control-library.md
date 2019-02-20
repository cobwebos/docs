---
title: 如何使用 Azure Maps 中的 Android 地图控件 | Microsoft Docs
description: 使用 Azure Maps 中的 Android 地图控件。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e3f7579324e1218cc2e2c3594889db776da6e529
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119031"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>如何使用 Azure Maps Android SDK

Azure Maps Android SDK 是适用于 Android 的矢量地图库。 本文将引导你完成安装 Azure Maps Android SDK、加载地图以及在地图上插入图钉的过程。

## <a name="prerequisites-to-get-started"></a>开始之前的先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户 

若要遵循本指南中的步骤，首先需要参阅[管理帐户和密钥](how-to-manage-account-keys.md)以创建并管理采用 S1 定价层的帐户订阅。

### <a name="download-android-studio"></a>下载 Android Studio

可以从 Google 免费下载 [Android Studio](https://developer.android.com/studio/)。 若要安装 Azure Maps Android SDK，首先需要下载 Android Studio，并创建一个包含空活动的项目。

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中创建项目

需要创建一个包含空活动的新项目。 遵循以下步骤创建新的 Android Studio 项目：

1. 在“选择项目”下，选中“手机和平板电脑”作为运行应用程序的平台。
2. 在平台下面单击“空活动”，然后单击“下一步”。
3. 在“配置项目”下，选择 `API 21: Android 5.0.0 (Lollipop)` 作为最低要求的 SDK。 这是 Azure Maps Android SDK 支持的最低版本。
4. 接受默认的 `Activity Name` 和 `Layout Name` 值，然后单击“完成”

安装 Android Studio 和创建新项目时如需更多帮助，请参阅 [Android Studio 文档](https://developer.android.com/studio/intro/)。

![创建新项目](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>设置虚拟设备

Android Studio 可让你在计算机上设置 Android 虚拟设备。 在开发期间，虚拟设备可帮助你测试应用程序。 若要设置虚拟设备，请单击项目屏幕右上角的“Android Virtual Device (AVD) Manager”图标。 然后单击“创建虚拟设备”按钮。 也可以通过工具栏中的“工具”>“Android”>“AVD Manager”转到该管理器。 在“手机”类别中选择“Nexus 5X”，然后单击“下一步”。

在 [Android Studio 文档](https://developer.android.com/studio/run/managing-avds)中详细了解如何设置 AVD。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>安装 Azure Maps Android SDK

在继续生成应用程序之前，请遵循以下步骤安装 Azure Maps Android SDK。 

1. 将以下内容添加到 **build.gradle** 文件中的存储库块 **allprojects**。

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新 **app/build.gradle** 并在其中添加以下内容：

    1. 将以下内容添加到 Android 块：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. 更新依赖项块并在其中添加以下内容：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. 将以下内容添加到“AndroidManifest.xml”以设置权限

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. 编辑 **res > layout > activity_main.xml**，使其如以下 XML 所示：
    
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

5. 编辑 **MainActivity.java** 以创建地图视图活动类。 编辑后，它应如以下类所示：

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

完成上述步骤后，Android Studio 很有可能会针对代码中的某些文本发出警告。 若要处理这种情况，必须导入 `MainActivity.java` 中引用的某些类。

可以按 `Alt`+`Enter`（在 Mac 上为 `Option`+`Return`）自动导入这些类。 

单击“运行‘应用’”按钮（或 Mac 上的 `Control`+`R`）生成应用程序。

![单击“运行”](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio 将花费几秒钟时间来生成应用程序。 生成完成后，可在 Android 仿真设备中测试应用程序。 将会看到下面所示的地图。

![Android 地图](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>将标记添加到地图

若要在地图中添加标记，请将 `mapView.getMapAsync()` 函数添加到 `MainActivity.java`。 最终的 `MainActivity.java` 应如下所示：

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

重新运行应用程序，应会在地图上看到下面所示的标记。

![Android 地图图钉](./media/how-to-use-android-map-control-library/android-map-pin.png)