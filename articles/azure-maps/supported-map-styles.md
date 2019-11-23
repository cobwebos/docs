---
title: Azure Maps 中支持的地图样式 | Microsoft Docs
description: Azure Maps 支持的地图样式
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5368aec04eb0a57654adf5b0d5914282a979dcfd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325402"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps 支持的地图样式
Azure Maps 支持几种不同的内置地图样式，如下所述。

## <a name="road"></a>道路
**道路**地图是一个标准地图，可显示道路、自然特征和人工特征以及这些特征的标签。

![道路](./media/supported-map-styles/road.png)

**适用 API：**
* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control

## <a name="blank-and-blank_accessible"></a>blank and blank_accessible

The **blank** and **blank_accessible** map styles provide a blank canvas on which to visualize data on. The **blank_accessible** style will continue to provide screen reader updates with location details of where the map is located, even though the base map is not displayed.

> [!Note]
> In the web SDK you can change the background color of the map by setting the CSS `background-color` style of map DIV element.

**适用 API：**
* Web SDK map control

## <a name="satellite"></a>卫星 
**卫星**样式是卫星和航拍图像的组合。

![卫星](./media/supported-map-styles/satellite.png)

**适用 API：**
* [卫星图块](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK map control
* Android map control

## <a name="satellite_road_labels"></a>satellite_road_labels
此地图样式是道路和标签叠加在卫星和航拍图像上的混合体。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**适用 API：**
* Web SDK map control
* Android map control

## <a name="grayscale_dark"></a>grayscale_dark
**灰度深色**是道路地图样式的深色版本。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**适用 API：**
* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control 
* Android map control


## <a name="grayscale_light"></a>grayscale_light
**grayscale light** is a light version of the road map style.

![grayscale light](./media/supported-map-styles/grayscale_light.png)

**适用 API：**
* Web SDK map control
* Android map control


## <a name="night"></a>夜间
夜间是包含彩色道路和符号的道路地图样式的深色版本。

![夜间](./media/supported-map-styles/night.png)

**适用 API：**
* Web SDK map control
* Android map control

## <a name="road_shaded_relief"></a>road_shaded_relief
道路地形阴影是 Azure Maps 主要样式，通过地球的轮廓完成。

![地形阴影](./media/supported-map-styles/shaded-relief.png)

**适用 API：**
* [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control


## <a name="next-steps"></a>后续步骤

Learn about how to set a map style in Azure Maps:

> [!div class="nextstepaction"]
> [选择地图样式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
