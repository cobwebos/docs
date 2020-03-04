---
title: 显示 android 地图上的流量数据 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Microsoft Azure map Android SDK 来显示地图上的流量数据。
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 37de55d671bb19cfcd9fd494c2e76f658fc7db21
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249495"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>使用 Azure Maps 显示地图上的流量数据 Android SDK

流数据和事件数据是可以在地图上显示的两种类型的流量数据。 本指南演示如何显示这两种类型的流量数据。 事件数据包含基于点的数据和基于行的数据，如构造、闭包和意外等。 流数据显示有关路上流量的指标。

## <a name="prerequisites"></a>必备条件

你需要[创建 Azure 帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)并[获取订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)，然后才能在地图上显示流量。 然后，需要安装[Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)并加载地图。

## <a name="incidents-traffic-data"></a>事件流量数据 

需要导入以下库来调用 `setTraffic` 和 `incidents`：

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 下面的代码段演示如何在地图上显示流量数据。 我们会将一个布尔值传递到 `incidents` 方法，并将其传递给 `setTraffic` 方法。 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>流流量数据

首先需要导入以下库，以便调用 `setTraffic` 和 `flow`：

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

使用以下代码片段设置流量流数据。 与上一节中的代码类似，我们将 `flow` 方法的返回值传递到 `setTraffic` 方法。 有四个可传递给 `flow`的值，每个值都将触发 `flow` 以返回各自的值。 然后，`flow` 的返回值将作为参数传递给 `setTraffic`。 请参阅下表中的以下四个值：

| | |
| :-- | :-- |
| TrafficFlow | 不显示地图上的流量数据 |
| TrafficFlow | 显示相对于旅途的自由流动速度的流量数据 |
| TrafficFlow RELATIVE_DELAY | 显示比平均预计延迟慢的区域 |
| TrafficFlow | 显示路上的所有车辆的绝对速度 |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>通过单击功能显示事件流量数据

若要获取特定功能的事件，可以使用以下代码。 单击某项功能后，代码逻辑将检查事件，并生成有关事件的消息。 屏幕底部将显示一条消息，其中包含详细信息。

1. 首先，您需要 **> 布局 > activity_main**中编辑 res，使其看起来像下面这样的内容。 可以将 `mapcontrol_centerLat`、`mapcontrol_centerLng`和 `mapcontrol_zoom` 替换为所需的值。 请记住，缩放级别是0到22之间的值。 在缩放级别为0时，整个世界适用于单个磁贴。

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. 将以下代码添加到**MainActivity**文件。 默认情况下包含包，因此请确保将包置于顶部。

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. 在应用程序中合并上述代码后，你将能够单击功能并查看流量事件的详细信息。 根据你在**activity_main .xml**文件中使用的纬度、经度和缩放级别值，你将看到类似于下图的结果：

   <center>

   ![事件-地图上的流量](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>后续步骤

查看以下指南以了解如何将更多数据添加到地图：

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加图块层](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [将形状添加到 android 地图](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [显示功能信息](display-feature-information-android.md)
