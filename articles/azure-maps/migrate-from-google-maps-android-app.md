---
title: 迁移 Android 应用 |Microsoft Docs
description: 本教程介绍如何将 Android 应用从 Google Maps 迁移到 Microsoft Azure Maps。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481394"
---
# <a name="migrate-an-android-app-from-google-maps"></a>从 Google Maps 迁移 Android 应用

Azure Maps Android SDK 具有与 Web SDK 非常相似的 API 接口。 如果已使用其中一种 Sdk 进行开发，则很多相同的概念、最佳做法和体系结构都适用，并且你应该能够轻松地将知识从一个迁移到另一个。

Azure Maps Android SDK 支持 API 21 的最低 Android 版本： Android 5.0.0 （棒糖形）。

Java 中提供的所有示例，但是 Kotlin 也可与 Azure Maps Android SDK 一起使用。

有关利用此 SDK 进行开发的详细信息，请参阅[Azure Maps Android SDK 的操作方法指南](how-to-use-android-map-control-library.md)。

## <a name="load-a-map"></a>加载映射

使用 Google 或 Azure Maps 在 Android 应用中加载地图包含许多相同的步骤。 使用任一 SDK 时，必须：

- 获取 API 或订阅密钥以访问任一平台。
- 向活动添加一些 XML，以指定应在何处呈现地图以及应如何进行布局。
- 将包含地图视图的活动中的所有生命周期方法转发到 map 类中的对应项。 特别是，必须在以下方法中转发：
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- 在尝试以编程方式访问映射之前，请等待映射准备就绪。

**之前： Google Maps**

若要使用适用于 Android 的 Google Maps SDK 显示地图，需执行以下步骤：

1.  确保安装 Google Play 服务。
2.  将 Google Maps 服务的依赖项添加到模块的**gradle**文件中： 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  在**google\_map\_api**文件的应用程序部分中添加一个 GOOGLE Maps API 密钥：
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  向主活动添加地图片段：

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  在**MainActivity**文件中，需要添加 GOOGLE Maps SDK 的导入。 将包含地图视图的活动中的所有生命周期方法转发到 map 类中的对应项。 可以通过使用 `getMapAsync(OnMapReadyCallback)` 方法从地图片段检索 `MapView` 的实例。 `MapView` 会自动初始化 maps 系统和视图。 编辑**MainActivity**文件，如下所示：

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

在应用程序中运行时，地图控件将按如下方式加载。

<center>

![简单的 Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**后： Azure Maps**

若要使用适用于 Android 的 Azure Maps SDK 显示地图，需执行以下步骤：

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

        > [!Note]
        > 定期升级并增强 Azure Maps Android SDK。 可以参阅[Android 地图控件](how-to-use-android-map-control-library.md)入门文档，获取最新的 Azure Maps 实现版本号。 此外，还可以将版本号从 "0.2" 设置为 "0 +"，以使其始终指向最新版本。
    
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

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

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

如果运行应用程序，地图控件将按如下方式加载。

<center>

![简单 Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

请注意，Azure Maps 控件支持进一步缩小，并提供了更多世界视图。

> [!TIP]
> 如果在 Windows 中使用 Android 仿真程序，则映射可能无法呈现，因为与 OpenGL 和软件加速图形呈现发生冲突。 以下是解决此问题的一些问题。 打开 AVD 管理器，选择要编辑的虚拟设备。 在 "**模拟性能**" 部分中，将 "**图形**" 选项设置为 "**硬件**"。

## <a name="localizing-the-map"></a>本地化地图

如果你的受众遍布多个国家或地区，则本地化非常重要。

**之前： Google Maps**

可以在主活动的 `onCreate` 方法中设置地图的语言，方法是在设置映射的上下文视图之前添加以下代码。 以下代码使用语言代码 "fr" 将语言限制为法语。

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

下面是将语言设置为 "fr" 的 Google Maps 的示例。

<center>

![Google Maps 本地化](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**后： Azure Maps**

Azure Maps 提供了三种不同的方式来设置地图的语言和区域视图。 第一种方法是使用静态 `setLanguage` 将语言和区域视图信息直接传递到 `AzureMaps` 类，并全局 `setView` 方法。 这会在应用中加载的所有 Azure Maps 控件上设置默认语言和区域视图。 以下代码使用语言代码 "fr-fr" 将语言限制为法语。

```java
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

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

第三种方法是使用地图 `setStyle` 方法以编程方式设置地图的语言和区域视图。 可随时完成此操作，以更改地图的语言和区域视图。

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

下面是将语言设置为 "fr" 的 Azure Maps 的示例。

<center>

![Azure Maps 本地化](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

[此处](supported-languages.md)介绍了支持的语言和区域视图的完整列表。

## <a name="setting-the-map-view"></a>设置地图视图

可以通过调用适当的方法，以编程方式将 Azure 和 Google Maps 中的动态映射移动到新的地理位置。 下面的示例演示如何使地图显示卫星高空图像，将地图居中放置在具有坐标的位置（纬度：35.0272，经度：-111.0225），并在 Google Maps 中将缩放级别更改为15。

> [!NOTE]
> Google Maps 使用大小为256像素的磁贴，而 Azure Maps 使用较大的512像素磁贴。 这会减少 Azure Maps 加载与 Google Maps 相同的映射区所需的网络请求数。 不过，由于磁贴金字塔在地图控件中的工作方式，Azure Maps 中的较大磁贴表示在 Google Maps 中实现与地图相同的可查看区域，因此使用 Azure Maps 时，需要将在 Google Maps 中使用的缩放级别减去1。

**之前： Google Maps**

可以通过使用 `moveCamera` 方法以编程方式移动 Google Maps 地图控件的照相机，此方法允许您指定地图的中心和缩放级别。 `setMapType` 方法可用于更改所显示的地图的类型。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps 集视图](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**后： Azure Maps**

如前所述，若要在 Azure Maps 中实现相同的可查看区域，请将 Google Maps 中使用的缩放级别减一，在这种情况下，使用的缩放级别为14。

可以在地图控件上的 XML 特性中设置初始映射视图。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

可以使用映射 `setCamera` 和 `setStyle` 方法以编程方式更新映射视图。

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure Maps 集视图](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**其他资源：**

- [支持的地图样式](supported-map-styles.md)

## <a name="adding-a-marker"></a>添加标记

点数据通常使用地图上的图像呈现在地图上。 这些图像通常称为标记、图钉、pin 或符号。 下面的示例将点数据呈现为地图上的标记（纬度：51.5、经度：-0.2）。

**之前： Google Maps**

使用 Google Maps，将使用映射 `addMarker` 方法添加标记。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google Maps 标记](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**后： Azure Maps**

在 Azure Maps 点数据可以在地图上呈现，方法是先将数据添加到数据源，然后将该数据源连接到符号层。 数据源优化地图控件中空间数据的管理，符号层指定如何使用作为图像和/或文本呈现点数据。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

Azure Maps 标记 ![](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>添加自定义标记

自定义图像可用于表示地图上的点。 下面的示例使用自定义图像来显示地图上的一个点（纬度：51.5，经度：-0.2），并偏移标记的位置，使图钉图标的点与地图上的正确位置对齐。

<center>

![黄色图钉图像](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_图钉</center>

在这两个示例中，上述映像将添加到 apps 资源的可绘制文件夹中。

**之前： Google Maps**

使用 Google Maps，可以通过在标记的 `icon` 选项中加载自定义图像来对其进行标记。 若要使图像的点与坐标对齐，可以使用 `anchor` 选项。 定位点相对于图像的尺寸，在本例中为0.2 单位宽，1单位高。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Google Maps 自定义标记](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**后： Azure Maps**

Azure Maps 中的符号层也支持自定义图像，但需要先将该图像加载到映射资源中，并为其分配唯一的 ID。 然后，符号层可以引用此 ID。 通过使用 `iconOffset` 选项，符号可以偏移，以与图像上的正确点对齐。 请注意，图标偏移量以像素为单位。 默认情况下，偏移量是相对于图像底部的位置，但这可以使用 `iconAnchor` 选项进行调整。 此示例将 `iconAnchor` 选项设置为 `"center"`，并使用图标偏移量将图像向右移动5个像素，将图像向上移动15个像素以与图钉图像的点对齐。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

Azure Maps 自定义标记 ![](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>添加折线

折线用于表示地图上的行或路径。 下面的示例演示如何在地图上创建虚线折线。

**之前： Google Maps**

使用 Google Maps，可以使用 `PolylineOptions` 类创建折线，并使用 `addPolyline` 方法将其添加到地图中。 可以使用 `color` 选项设置笔划颜色，使用 width 选项设置笔划宽度，还可以使用 `pattern` 选项设置笔画虚线数组。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Google Maps 折线](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**后： Azure Maps**

在 Azure Maps 中，折线称为 LineString 或 MultiLineString 对象。 可以将这些对象添加到数据源，并使用线条层进行呈现。 请注意，笔划宽度和短线数组 "像素" 单位与中的 Azure Maps Web SDK 保持一致，因为这两个 Sdk 中的相同值生成相同的结果。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure Maps 折线](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>添加多边形

多边形用于表示地图上的某个区域。 下面的示例演示如何创建一个多边形，使其基于地图的中心坐标形成三角形。

**之前： Google Maps**

使用 Google Maps，可以使用 `PolygonOptions` 类创建多边形并使用 `addPolygon` 方法将其添加到地图中。 可以使用 "`fillColor`" 和 "`strokeColor`" 选项设置填充和笔划颜色，使用 "`strokeWidth`" 选项设置笔划宽度。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Google Maps 多边形](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**后： Azure Maps**

在 Azure Maps 中，可以使用层将多边形和 MultiPolygon 对象添加到数据源，并在地图上呈现这些对象。 多边形的面积可以在多边形层中呈现。 可以使用线条层呈现多边形的轮廓。 请注意，笔划宽度和短线数组 "像素" 单位与中的 Azure Maps Web SDK 保持一致，因为这两个 Sdk 中的相同值生成相同的结果。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

Azure Maps 多边形 ![](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>覆盖图块层

图块层（也称为 Google Maps 中的图像叠加）允许您覆盖已分解成较小平铺图像、与地图平铺系统对齐的图层图像。 这是覆盖层图像或非常大的数据集的常用方法。

下面的示例将覆盖爱荷华州大学爱荷华环境 Mesonet 的天气预报。 图块的大小为256像素。

**之前： Google Maps**

使用 Google Maps，图块层可以在地图的顶部重叠使用 `TileOverlayOptions` 类并使用 `addTileLauer` 方法添加到地图中。 若要使磁贴半透明，`transparency` 选项设置为0.2 或20% 透明。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Google 地图图块层](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**后： Azure Maps**

在 Azure Maps 中，图块层可添加到地图中，这与任何其他层的方式几乎相同。 具有 x、y 和缩放占位符的格式 URL;分别 `{x}`、`{y}``{z}`，告诉层在何处访问磁贴。 Azure Maps 中的图块层还支持 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 占位符。 若要使图块层为半透明，则使用不透明度值0.8。 请注意，不透明度和透明度尽管相似，但使用了反转值。 若要在两者之间进行转换，只需从数字1中减去值。

> [!TIP]
> 在 Azure Maps 层中，可以轻松地在其他层（包括基本地图层）下呈现。 通常，需要在地图标签下面呈现图块层以便于阅读。 `map.layers.add` 方法采用第二个参数，该参数是要在其中插入新层的层的 id。 若要在地图标签下面插入图块层，可以使用以下代码： `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Azure Maps 图块层](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>显示交通情况

流量数据可以重叠到 Azure 和 Google maps。

**之前： Google Maps**

使用 Google Maps，可以通过将 true 传递到地图的 `setTrafficEnabled` 方法，将流量流数据叠加到地图的顶部。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps 流量](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**后： Azure Maps**

Azure Maps 提供了用于显示流量的多种不同选项。 流量事件（例如公路和事故）可以显示为地图上的图标。 流量可以在地图上重叠，可以在地图上重叠，并且可以根据正常的预期延迟或绝对延迟，将颜色修改为相对于已发布的速度限制。 Azure Maps 中的事件数据每分钟更新一次，流数据每两分钟更新一次。

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

Azure Maps 流量 ![](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>后续步骤

了解 Azure Maps Android SDK 的详细信息。

> [!div class="nextstepaction"]
> [如何使用 Android 地图控件](how-to-use-android-map-control-library.md)
