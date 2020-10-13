---
title: 支持的内置 Azure Maps 地图样式
description: 了解 Azure Maps 支持的内置地图样式，例如公路、blank_accessible、卫星、satellite_road_labels、road_shaded_relief 和晚间。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4a50a9d6d4a485f7d8e63adb9ae5032f49edc261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310350"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps 支持的内置地图样式

Azure Maps 支持几种不同的内置地图样式，如下所述。

## <a name="road"></a>道路

**道路**地图是一种标准地图，显示道路。 它还显示自然和人工功能，以及这些功能的标签。

![道路地图样式](./media/supported-map-styles/road.png)

**适用 API：**

* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图磁贴](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="blank-and-blank_accessible"></a>空白和 blank_accessible

**空白**和**blank_accessible**地图样式提供空白画布来可视化数据。 **Blank_accessible**样式将继续提供屏幕阅读器更新和地图的位置详细信息，即使未显示基本地图。

> [!Note]
> 在 Web SDK 中，可以通过设置地图 DIV 元素的 CSS 样式来更改地图的背景色 `background-color` 。

**适用 API：**

* Web SDK 地图控件

## <a name="satellite"></a>satellite

**卫星**样式是卫星和航拍图像的组合。

![卫星磁贴地图样式](./media/supported-map-styles/satellite.png)

**适用 API：**

* [卫星图块](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="satellite_road_labels"></a>satellite_road_labels

此地图样式是道路和标签叠加在卫星和航拍图像上的混合体。

![satellite_road_labels 地图样式](./media/supported-map-styles/satellite-road-labels.png)

**适用 API：**

* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="grayscale_dark"></a>grayscale_dark

**灰度深色**是道路地图样式的深色版本。

![gray_scale 地图样式](./media/supported-map-styles/grayscale-dark.png)

**适用 API：**

* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图磁贴](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="grayscale_light"></a>grayscale_light

**灰色光** 是道路地图样式的轻型版本。

![灰度浅地图样式](./media/supported-map-styles/grayscale-light.png)

**适用 API：**
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="night"></a>夜间

夜间**** 是包含彩色道路和符号的道路地图样式的深色版本。

![夜景地图样式](./media/supported-map-styles/night.png)

**适用 API：**

* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="road_shaded_relief"></a>road_shaded_relief

道路地形阴影是 Azure Maps 主要样式，通过地球的轮廓完成****。

![着色止裂槽地图样式](./media/supported-map-styles/shaded-relief.png)

**适用 API：**

* [地图磁贴](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** 是比其他样式高的深色地图样式。

![高对比度深色地图样式](./media/supported-map-styles/high-contrast-dark.png)

**适用 API：**

* Web SDK 地图控件
* Power BI 视觉对象

## <a name="next-steps"></a>后续步骤

了解如何在 Azure Maps 中设置地图样式：

[选择地图样式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)