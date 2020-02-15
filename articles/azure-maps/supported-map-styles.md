---
title: 支持的地图样式 |Microsoft Azure 映射
description: 在本文中，你将了解 Microsoft Azure 地图支持的不同地图呈现样式。
author: farah-alyasari
ms.author: v-faalya
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9cdfd0d029057e36e010203b7c35a5aafee4b574
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208278"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps 支持的地图样式
Azure Maps 支持几种不同的内置地图样式，如下所述。

## <a name="road"></a>道路
**道路**地图是一个标准地图，可显示道路、自然特征和人工特征以及这些特征的标签。

![道路地图样式](./media/supported-map-styles/road.png)

**适用 API：**
* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* Android 地图控件

## <a name="blank-and-blank_accessible"></a>空白和 blank_accessible

**空白**和**blank_accessible**地图样式提供了一个空白画布，可在其上对数据进行可视化处理。 **Blank_accessible**样式将继续提供屏幕阅读器更新和地图的位置详细信息，即使未显示基本地图。

> [!Note]
> 在 web SDK 中，可以通过设置地图 DIV 元素的 CSS `background-color` 样式来更改地图的背景色。

**适用 API：**
* Web SDK 地图控件

## <a name="satellite"></a>卫星 
**卫星**样式是卫星和航拍图像的组合。

![卫星磁贴地图样式](./media/supported-map-styles/satellite.png)

**适用 API：**
* [卫星图块](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK 地图控件
* Android 地图控件

## <a name="satellite_road_labels"></a>satellite_road_labels
此地图样式是道路和标签叠加在卫星和航拍图像上的混合体。

![satellite_road_labels 地图样式](./media/supported-map-styles/satellite_road_labels.png)

**适用 API：**
* Web SDK 地图控件
* Android 地图控件

## <a name="grayscale_dark"></a>grayscale_dark
**灰度深色**是道路地图样式的深色版本。

![gray_scale 地图样式](./media/supported-map-styles/grayscale_dark.png)

**适用 API：**
* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件 
* Android 地图控件


## <a name="grayscale_light"></a>grayscale_light
**灰色光**是道路地图样式的轻型版本。

![灰度浅地图样式](./media/supported-map-styles/grayscale_light.png)

**适用 API：**
* Web SDK 地图控件
* Android 地图控件


## <a name="night"></a>夜间
夜间是包含彩色道路和符号的道路地图样式的深色版本。

![夜景地图样式](./media/supported-map-styles/night.png)

**适用 API：**
* Web SDK 地图控件
* Android 地图控件

## <a name="road_shaded_relief"></a>road_shaded_relief
道路地形阴影是 Azure Maps 主要样式，通过地球的轮廓完成。

![着色止裂槽地图样式](./media/supported-map-styles/shaded-relief.png)

**适用 API：**
* [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* Android 地图控件


## <a name="next-steps"></a>后续步骤

了解如何在 Azure Maps 中设置地图样式：

> [!div class="nextstepaction"]
> [选择地图样式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
