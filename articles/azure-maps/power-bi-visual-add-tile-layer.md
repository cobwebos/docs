---
title: 将图块层添加到 Azure Maps Power BI 视觉对象 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Power BI 的 Microsoft Azure 地图视觉对象中的图块层。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8a0d930d1c0fd30c48d97b0d1d4b94548077fbca
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261680"
---
# <a name="add-a-tile-layer"></a>添加图块层

图块层功能（如参考层功能）允许在地图上重叠附加数据以提供更多上下文。 磁贴层允许您将图像叠加到 Azure Maps 的地图图块的顶部。 这是一种覆盖大型或复杂数据集的好方法，例如来自无人机的图像或数百万行数据。

> [!div class="mx-imgBorder"]
> ![在图块上显示气泡图层的地图，其中显示了当前的红外天气数据 Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

图块层从服务器的磁贴中加载。 这些映像可以是预呈现的或动态呈现的。 使用图块层识别的命名约定，预先呈现的图像与服务器上的任何其他图像存储在一起。 动态呈现的图像使用服务将图像加载到接近实时的时间。 磁贴层是在地图上可视化大型数据集的好方法。 矢量数据不仅可以从图像生成，还可以作为图块层呈现。

磁贴服务可用的边界框和缩放范围可以作为设置传递，以限制请求磁贴的位置。 这同时是视觉对象和磁贴服务的性能增强。 下面概述了在 "**图块层**" 部分中提供的 "**格式**" 窗格中提供的所有设置。

| 设置        | 说明   |
|----------------|---------------|
| Url            | 指向磁贴服务的格式化 URL。  |
| 图块大小      | 一个整数值，该值指定图块的宽度和高度尺寸。   |
| 北方边界    | 可用磁贴的边界框的北纬度。 |
| 南界限    | 可用磁贴的边界框的南部纬度。 |
| 东边界     | 可用磁贴的边界框的东部经度。  |
| 西北     | 可用磁贴的边界框的西方经度。   |
| 透明度   | 图块层的透明度。   |
| 为 TM         | 磁贴地图服务，这是一个反转图块层的 Y 坐标轴的规范。 |
| 最小缩放       | 最小缩放级别磁贴可用。 |
| 最大缩放       | 最大缩放级别磁贴可用。  |
| 层位置 | 指定层相对于其他地图层的位置。 |

## <a name="tile-url-formatting"></a>磁贴 URL 格式

Azure Maps 视觉对象支持三种不同的平铺服务命名约定：

-   **X、Y、缩放表示法**-X 是列、y 是磁贴网格中磁贴的行位置，而缩放表示法基于缩放级别。
-   **Quadkey 表示法**-将 x、y 和缩放信息合并为一个字符串值。 此字符串值将成为单个磁贴的唯一标识符。
-   **边界框**-指定边界方框坐标格式的图像： `{west},{south},{east},{north}` 。 此格式通常由[Web 映射服务 (WMS) ](https://www.opengeospatial.org/standards/wms)使用。

磁贴使用以下参数将 https URL 指向磁贴 URL 模板：

-   `{x}` - 图块的 X 位置。 还需要 `{y}` 和 `{z}`。
-   `{y}` - 图块的 Y 位置。 还需要 `{x}` 和 `{z}`。
-   `{z}` - 图块的缩放级别。 还需要 `{x}` 和 `{y}`。
-   `{quadkey}`- `quadkey` 基于 Bing 地图图块系统命名约定的磁贴标识符。
-   `{bbox-epsg-3857}`- `{west},{south},{east},{north}` EPSG 3857 空间引用系统中具有格式的边界框字符串。

例如，下面是 Azure Maps 中的[天气雷达图磁贴服务](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)的格式化磁贴 URL。 请注意， `[subscription-key]` 是 Azure Maps 订阅密钥的占位符。

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

有关 Azure Maps 平铺系统的详细信息，请参阅 [缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)。

## <a name="next-steps"></a>后续步骤

向映射添加更多上下文：

> [!div class="nextstepaction"]
> [显示实时流量](power-bi-visual-show-real-time-traffic.md)
