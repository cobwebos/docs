---
title: Azure Maps 中支持的地图样式 | Microsoft Docs
description: Azure Maps 支持的地图样式
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 923b9fef75fa610ee817a86ce0b4d6b322153f01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113425"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps 支持的地图样式
Azure Maps 支持几种不同的内置地图样式，如下所述。

## <a name="road"></a>道路
**道路**地图是一个标准地图，可显示道路、自然特征和人工特征以及这些特征的标签。

![道路](./media/supported-map-styles/road.png)

**适用 API：**
* [地图图像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS 地图控件

## <a name="satellite"></a>卫星 
**卫星**样式是卫星和航拍图像的组合。

![卫星](./media/supported-map-styles/satellite.png)

**适用 API：**
* [卫星图块](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS 地图控件

## <a name="satelliteroadlabels"></a>satellite_road_labels
此地图样式是道路和标签叠加在卫星和航拍图像上的混合体。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**适用 API：**
* JS 地图控件

## <a name="grayscaledark"></a>grayscale_dark
**灰度深色**是道路地图样式的深色版本。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**适用 API：**
* JS 地图控件 

## <a name="night"></a>夜间
夜间是包含彩色道路和符号的道路地图样式的深色版本。

![夜间](./media/supported-map-styles/night.png)

**适用 API：**
* JS 地图控件

## <a name="roadshadedrelief"></a>road_shaded_relief
道路地形阴影是 Azure Maps 主要样式，通过地球的轮廓完成。

![地形阴影](./media/supported-map-styles/shaded-relief.png)

**适用 API：**
* JS 地图控件
* [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
