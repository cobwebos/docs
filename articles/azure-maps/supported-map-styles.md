---
title: 支持的地图样式 |微软 Azure 地图
description: 在本文中，您将了解 Microsoft Azure 地图支持的不同地图呈现样式。
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334041"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps 支持的地图样式
Azure Maps 支持几种不同的内置地图样式，如下所述。

## <a name="road"></a>道路
**道路**地图是一个标准地图，可显示道路、自然特征和人工特征以及这些特征的标签。

![路线图样式](./media/supported-map-styles/road.png)

**适用 API：**
* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图磁贴](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* 安卓地图控件

## <a name="blank-and-blank_accessible"></a>空白和blank_accessible

**空白**和**blank_accessible**地图样式提供了一个空白画布，用于在其中可视化数据。 **blank_accessible**样式将继续提供带有地图位置详细信息的屏幕阅读器更新，即使不显示底地图也是如此。

> [!Note]
> 在 Web SDK 中，您可以通过设置地图 DIV 元素的 CSS`background-color`样式来更改地图的背景颜色。

**适用 API：**
* Web SDK 地图控件

## <a name="satellite"></a>卫星 
**卫星**样式是卫星和航拍图像的组合。

![卫星瓷砖地图样式](./media/supported-map-styles/satellite.png)

**适用 API：**
* [卫星图块](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK 地图控件
* 安卓地图控件

## <a name="satellite_road_labels"></a>satellite_road_labels
此地图样式是道路和标签叠加在卫星和航拍图像上的混合体。

![satellite_road_labels地图样式](./media/supported-map-styles/satellite-road-labels.png)

**适用 API：**
* Web SDK 地图控件
* 安卓地图控件

## <a name="grayscale_dark"></a>grayscale_dark
**灰度深色**是道路地图样式的深色版本。

![gray_scale地图样式](./media/supported-map-styles/grayscale-dark.png)

**适用 API：**
* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图磁贴](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件 
* 安卓地图控件


## <a name="grayscale_light"></a>grayscale_light
**灰度光**是路线图样式的浅色版本。

![灰度光贴图样式](./media/supported-map-styles/grayscale-light.png)

**适用 API：**
* Web SDK 地图控件
* 安卓地图控件


## <a name="night"></a>夜间
夜间**** 是包含彩色道路和符号的道路地图样式的深色版本。

![夜地图样式](./media/supported-map-styles/night.png)

**适用 API：**
* Web SDK 地图控件
* 安卓地图控件

## <a name="road_shaded_relief"></a>road_shaded_relief
道路地形阴影是 Azure Maps 主要样式，通过地球的轮廓完成****。

![底子浮雕地图样式](./media/supported-map-styles/shaded-relief.png)

**适用 API：**
* [地图磁贴](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* 安卓地图控件

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark**是一种暗地图样式，与其他样式相比对比度更高。

![高对比度深色地图样式](./media/supported-map-styles/high-contrast-dark.png)

**适用 API：**
* Web SDK 地图控件

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 地图中设置地图样式：

> [!div class="nextstepaction"]
> [选择地图样式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
