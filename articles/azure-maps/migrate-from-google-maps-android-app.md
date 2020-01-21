---
title: 教程：迁移 Android 应用 | Microsoft Azure Maps
description: 如何将 Android 应用从 Google Maps 迁移到 Microsoft Azure Maps。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643d48cb931bcec1a8a3385d2ec24a394660c368
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909190"
---
# <a name="migrate-an-android-app-from-google-maps"></a>从 Google Maps 迁移 Android 应用

Azure Maps Android SDK 有一个非常类似于 Web SDK 的 API 接口。 如果你已使用某个 Web SDK 进行开发，很多概念、最佳做法和体系结构在 Android SDK 中同样适用，因此你应该可以在一个 SDK 中运用另一个 SDK 方面的知识。

Azure Maps Android SDK 支持 API 21 的最低 Android 版本：Android 5.0.0 (Lollipop)。

所有示例都是以 Java 提供的，但是，在 Azure Maps Android SDK 中也可以使用 Kotlin。

有关使用此 SDK 进行开发的详细信息，另请参阅 [Azure Maps Android SDK 的操作指南](how-to-use-android-map-control-library.md)。

## <a name="load-a-map"></a>加载地图

使用 Google Maps 或 Azure Maps 在 Android 应用中加载地图涉及到许多相同的步骤。 使用上述任一 SDK 时，必须：

- 获取用于访问其中一个平台的 API 密钥或订阅密钥。
- 将一些 XML 添加到活动中，以指定要在何处呈现地图，以及如何布局地图。
- 将包含地图视图的活动中的所有生命周期方法转发到地图类中的相应方法。 具体而言，必须转发以下方法：
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- 在尝试以编程方式访问地图之前，请等待地图准备就绪。

**前者：Google Maps**

若要使用适用于 Android 的 Google Maps SDK 显示地图，需执行以下步骤：

1.  确保已安装 Google Play 服务。
2.  将 Google Maps 服务的依赖项添加到模块的 **gradle.build** 文件： 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  将 Google Maps API 密钥添加到 **google\_maps\_api.xml** 文件的 application 节中：
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  将一个地图片段添加到主活动：

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  在 **MainActivity.java** 文件中，需要添加 Google Maps SDK 的 import 语句。 将包含地图视图的活动中的所有生命周期方法转发到地图类中的相应方法。 可以使用 `getMapAsync(OnMapReadyCallback)` 方法从地图片段中检索 `MapView` 实例。 `MapView` 将自动初始化地图系统和视图。 按如下所示编辑 **MainActivity.java** 文件：

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

在应用程序中运行时，将按如下所示加载地图控件。

<center>

![简单 Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**后者：Azure Maps**

若要使用适用于 Android 的 Azure Maps SDK 显示地图，需执行以下步骤：

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

        > [!Note]
        > Azure Maps Android SDK 会定期升级并增强。 可以查看 [Android 地图控件入门](how-to-use-android-map-control-library.md)文档，以获取最新的 Azure Maps 实现版本号。 此外，可将版本号从“0.2”设置为“0+”，使其始终指向最新版本。
    
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

    地图控件包含自身的生命周期方法用于管理 Android 的 OpenGL 生命周期，必须直接从包含活动调用这些方法。 要使应用正确调用地图控件的生命周期方法，必须在包含地图控件的活动中重写以下生命周期方法，并调用相应的地图控件方法。 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    按如下所示编辑 **MainActivity.java** 文件：
    
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

如果运行应用程序，将按如下所示加载地图控件。

<center>

![简单 Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

请注意，Azure Maps 控件支持更大程度的缩小操作，并不仅提供世界视图。

> [!TIP]
> 如果在 Windows 中使用 Android 仿真器，则地图可能无法呈现，因为与 OpenGL 和软件加速图形渲染之间存在冲突。 以下方法在某种程度上可以解决此问题。 打开 AVD 管理器，并选择要编辑的虚拟设备。 在“仿真性能”部分，将“图形”选项设置为“硬件”。   

## <a name="localizing-the-map"></a>本地化地图

如果你的受众遍布多个国家/地区或讲不同的语言，则本地化就非常重要。

**前者：Google Maps**

可以在 main 活动的 `onCreate` 方法中设置地图语言，方法是在设置地图的上下文视图之前添加以下代码。 以下示例使用语言代码“fr”将语言限制为法语。

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

下面是将语言设置为“fr”的 Google Maps 示例。

<center>

![Google Maps 本地化](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**后者：Azure Maps**

Azure Maps 提供三种不同的方式来设置地图的语言和区域视图。 第一种做法是全局使用静态 `setLanguage` 和 `setView` 方法，将语言和区域视图信息直接传入 `AzureMaps` 类。 这会在载入到应用的所有 Azure Maps 控件中设置默认的语言和区域视图。 以下示例使用语言代码“fr-FR”将语言限制为法语。

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

第二种做法是将语言和视图信息传入地图控件 XML 中。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

第三种做法是使用地图 `setStyle` 方法以编程方式设置地图的语言和区域视图。 随时可以采取这种做法来更改地图的语言和区域视图。

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

下面是将语言设置为“fr-FR”的 Azure Maps 示例。

<center>

![Azure Maps 本地化](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

[此文档](supported-languages.md)提供了支持的语言和区域视图的完整列表。

## <a name="setting-the-map-view"></a>设置地图视图

可以通过调用相应的方法，以编程方式将 Azure Maps 和 Google Maps 中的动态地图移到新的地理位置。 以下示例演示如何在 Google Maps 中使地图显示卫星航拍图像，并将地图中心点置于坐标（纬度：35.0272，经度：-111.0225），并将缩放级别更改为 15。

> [!NOTE]
> Google Maps 使用尺寸为 256 像素的图块，而 Azure Maps 则使用更大的 512 像素图块。 在加载相同的地图区域时，与 Google Maps 相比，这可以减少 Azure Maps 所需的网络请求数。 但是，由于地图控件中图块金字塔的工作方式，Azure Maps 中更大的图块意味着在，在使用 Azure Maps 时，若要实现与 Google Maps 中地图相同的可视区域，需要将 Google Maps 中使用的缩放级别减 1。

**前者：Google Maps**

可以使用 `moveCamera` 方法以编程方式移动 Google Maps 地图控件的相机，此方法允许指定地图的中心点和缩放级别。 `setMapType` 方法可用于更改显示的地图类型。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps - 设置视图](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**后者：Azure Maps**

如前所述，若要在 Azure Maps 中实现相同的可视区域，请将 Google Maps 中使用的缩放级别减 1。本例中使用的缩放级别为 14。

可以在地图控件上的 XML 特性中设置初始地图视图。

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

可以使用地图 `setCamera` 和 `setStyle` 方法以编程方式更新地图视图。

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure Maps - 设置视图](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**其他资源：**

- [支持的地图样式](supported-map-styles.md)

## <a name="adding-a-marker"></a>添加标记

通常使用地图上的图像在地图上呈现点数据。 这些图像通常称为标记、图钉或符号。 以下示例在地图上的（纬度：51.5，经度：-0.2）位置以标记的形式呈现点数据。

**前者：Google Maps**

在 Google Maps 中，标记是使用地图 `addMarker` 方法添加的。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google Maps 标记](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，可以通过先将数据添加到数据源，然后将该数据源连接到符号层，在地图上呈现点数据。 数据源优化地图控件中空间数据的管理，符号层指定如何以图像和/或文本的形式呈现点数据。

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

![Azure Maps 标记](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>添加自定义标记

可使用自定义图像来表示地图上的点。 以下示例使用一个自定义图像在地图上的（纬度：51.5，经度：-0.2）位置显示一个点，并偏移标记的位置，使图钉图标的点与地图上的正确位置对齐。

<center>

![黄色图钉图像](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

在这两个示例中，以上图像将添加到应用资源的 drawable 文件夹中。

**前者：Google Maps**

在 Google Maps 中，可将自定义图像用于标记，方法是通过标记的 `icon` 选项加载这些图像。 若要使图像的点与坐标对齐，可以使用 `anchor` 选项。 定位点相对于图像的尺寸，在本例中宽度为 0.2 个单位，高度为 1 个单位。

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

**后者：Azure Maps**

Azure Maps 中的符号层也支持自定义图像，但需要先将图像载入地图资源，并为其分配唯一的 ID。 然后，符号层可以引用此 ID。 可以使用 `iconOffset` 选项来偏移符号，使之与图像上的正确点对齐。 请注意，图标偏移量以像素为单位。 默认情况下，偏移量相对于图像的中下点，但可以使用 `iconAnchor` 选项调整此位置。 此示例将 `iconAnchor` 选项设置为 `"center"`，并使用图标偏移量将图像向右移动 5 个像素，向上移动 15 个像素，以便与图钉图像的点对齐。

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

![Azure Maps 自定义标记](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>添加折线

折线用于表示地图上的线条或路径。 以下示例演示如何在地图上创建虚线折线。

**前者：Google Maps**

在 Google Maps 中，可以使用 `PolylineOptions` 类创建折线，并使用 `addPolyline` 方法将其添加到地图中。 可以使用 `color` 选项设置笔划颜色，使用 width 选项设置笔划宽度，并使用 `pattern` 选项设置笔划虚线数组。

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

**后者：Azure Maps**

在 Azure Maps 中，折线称为 LineString 或 MultiLineString 对象。 可将这些对象添加到数据源，并使用线条层来呈现。 请注意，笔划宽度和虚线数组“像素”单位与 Azure Maps Web SDK 保持一致，因为在这两个 SDK 中使用相同值会生成相同的结果。

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

多边形用于表示地图上的某个区域。 以下示例演示如何创建一个多边形，该多边形基于地图的中点坐标构成一个三角形。

**前者：Google Maps**

在 Google Maps 中，可以使用 `PolygonOptions` 类创建多边形，并使用 `addPolygon` 方法将其添加到地图中。 可以使用 `fillColor` 和 `strokeColor` 选项设置填充颜色和笔划颜色，使用 `strokeWidth` 选项设置笔划宽度。

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

**后者：Azure Maps**

在 Azure Maps 中，可将 Polygon 和 MultiPolygon 对象添加到数据源，并使用层在地图上呈现这些对象。 可以在多边形层中呈现多边形的区域。 可以使用线条层呈现多边形的轮廓。 请注意，笔划宽度和虚线数组“像素”单位与 Azure Maps Web SDK 保持一致，因为在这两个 SDK 中使用相同值会生成相同的结果。

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

![Azure Maps 多边形](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>叠加图块层

图块层（在 Google Maps 中也称为图像叠加层）可让你叠加层图像，这些图像已分解成与地图图块系统相符的较小图块式图像。 这是叠加层图像或极大数据集的常用方法。

以下示例叠加爱荷华州立大学环境气象网提供的气象雷达图块层。 图块大小为 256 像素。

**前者：Google Maps**

在 Google Maps 中，可以使用 `TileOverlayOptions` 类在地图的顶层叠加图块层，并使用 `addTileLauer` 方法将其添加到地图中。 若要使图块半透明，请将 `transparency` 选项设置为 0.2，即 20% 的透明度。

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

![Google Maps 图块层](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**后者：Azure Maps**

在 Azure Maps 中，基本上可以像添加任何其他层一样将图块层添加到地图中。 指定包含 x 轴、y 轴和缩放比例占位符的带格式 URL；`{x}`、`{y}`、`{z}` 分别用于告知层要在何处访问图块。 Azure Maps 中的图块层还支持 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 占位符。 若要使图块层半透明，请使用不透明度值 0.8。 请注意，尽管不透明度和透明度相似，但两者应使用反值。 若要在两者之间进行转换，只需用数字 1 减去其值。

> [!TIP]
> 在 Azure Maps 中，可以轻松地在其他层（包括基础地图层）下面呈现层。 通常，最好是在地图标签下面呈现图块层，以便于阅读。 `map.layers.add` 方法采用另一个参数，该参数是要在其中插入新层的层的 ID。 若要在地图标签下面插入图块层，可使用以下代码：`map.layers.add(myTileLayer, "labels");`

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

在 Azure Maps 和 Google Maps 中都可以叠加交通状况数据。

**前者：Google Maps**

在 Google Maps 中，可以通过将 true 传入地图的 `setTrafficEnabled` 方法，将交通流量数据叠加到地图的顶层。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps 交通状况](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**后者：Azure Maps**

Azure Maps 提供用于显示交通状况的多种不同选项。 交通事件（例如道路封闭和事故）可以作为图标显示在地图上。 交通流量和带颜色编码的道路可以叠加在地图上，并且可以根据公布的限速、正常的预期延误时间或绝对延误时间修改颜色。 Azure Maps 中的事件数据每隔一分钟更新一次，交通流量数据每隔两分钟更新一次。

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps 交通状况](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps Android SDK。

> [!div class="nextstepaction"]
> [如何使用 Android 地图控件](how-to-use-android-map-control-library.md)
