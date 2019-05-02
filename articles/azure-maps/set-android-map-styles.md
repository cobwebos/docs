---
title: Azure Maps 中的地图样式功能 | Microsoft Docs
description: 了解有关 Azure Maps 样式 Android SDK 的相关功能。
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870967"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>将使用 Azure Maps Android SDK 的地图样式设置

本文介绍两种方法设置的地图风格，使用 Azure Maps Android SDK。 Azure Maps 具有六个不同的映射样式可供选择。 有关受支持的映射样式的详细信息，请参阅[支持 Azure 地图的地图风格，](./supported-map-styles.md)。


## <a name="prerequisites"></a>必备组件

若要完成本文中的过程，您需要安装[Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)加载映射。


## <a name="set-map-style-in-the-layout"></a>在布局中设置地图样式

可以为您活动的类布局文件中设置地图样式。 编辑**res > 布局 > activity_main.xml**，使其类似于下面所示：

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
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`上面的属性将映射样式设置为**grayscale_dark**。 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>在活动类中设置地图样式

可以在活动类中设置地图样式。 复制到以下代码片段**onCreate()** 方法在`MainActivity.java`类。 这将设置为地图样式**satellite_road_labels**。

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>