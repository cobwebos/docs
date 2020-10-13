---
title: 向 Azure Maps Power BI 视觉对象添加引用层 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Power BI 的 Microsoft Azure 映射视觉对象中的参考层。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261681"
---
# <a name="add-a-reference-layer"></a>添加引用层

通过 "引用层" 功能，可以将辅助空间数据集上传到视觉对象，并将其叠加到地图上，以提供附加上下文。 此数据集由 Power BI 承载，并且必须为具有或文件扩展名的 [GeoJSON 文件](https://wikipedia.org/wiki/GeoJSON) `.json` `.geojson` 。

若要将 **GeoJSON** 文件添加为引用层，请在 " **格式** " 窗格中，展开 " **引用层** " 部分，然后按 " **+ 添加本地文件** " 按钮。

将 GeoJSON 文件添加到引用层后，该文件的名称将显示在 " **+ 添加本地文件** " 按钮的位置，其中包含一个 **X** 。 按 " **X** " 按钮，从视觉对象中删除数据，并从 Power BI 中删除 GeoJSON 文件。

下图显示了 [科罗拉多的2016人口 tracts](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson)，彩色按人口。

> [!div class="mx-imgBorder"]
> ![为科罗拉多显示2016人口 tracts 的地图，按人口作为参考层着色](media/power-bi-visual/reference-layer-CO-census-tract.png)

下面是 " **格式** " 窗格中的所有设置，可在 " **引用层** " 部分中找到。

| 设置              | 描述   |
|----------------------|---------------|
| 参考层数据 | 要作为地图内的附加层上载到视觉对象的数据 GeoJSON 文件。 " **+ 添加本地文件** " 按钮将打开一个文件对话框，用户可以使用该对话框选择具有 `.json` 或文件扩展名的 GeoJSON 文件 `.geojson` 。 |

> [!NOTE]
> 在此 Azure Maps 视觉对象预览中，引用层只将前5000个形状特征加载到地图中。 此限制将在未来的更新中增加。

## <a name="styling-data-in-a-reference-layer"></a>设置引用层中数据的样式

可以将属性添加到 GeoJSON 文件中的每个功能，以自定义其在地图上的样式。 此功能使用 Azure Maps Web SDK 中的简单数据层功能。 有关详细信息，请参阅支持的 [样式属性](spatial-io-add-simple-data-layer.md#default-supported-style-properties)上的此文档。 在 Azure Maps 视觉对象中，不支持自定义图标图像作为安全预防措施。

下面是 GeoJSON 点功能的一个示例，它将其显示的颜色设置为红色。

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>后续步骤

向映射添加更多上下文：

> [!div class="nextstepaction"]
> [添加图块层](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [显示实时流量](power-bi-visual-show-real-time-traffic.md)
