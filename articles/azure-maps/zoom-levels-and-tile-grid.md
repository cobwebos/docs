---
title: Azure Maps 中的缩放级别和磁贴网格 | Microsoft Docs
description: 了解 Azure Maps 中的缩放级别和磁贴网格
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 55441cda7a6fc65ac8103d19510823a7c84a9cbf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599919"
---
# <a name="zoom-levels-and-tile-grid"></a>缩放级别和磁贴网格
Azure Maps 使用球面 Mercator 投影坐标系统 (EPSG: 3857)。

世界分成正方形磁贴。 呈现（光栅）有 19 个缩放级别，其编号为 0 到 18。 呈现（矢量）有 21 个缩放级别，其编号为 0 到 20。 在缩放级别为 0 时，单个磁贴可以容纳整个世界：

![世界磁贴](./media/zoom-levels-and-tile-grid/world0.png)

缩放级别 1 使用 4 个磁贴来呈现世界：一个 2 x 2 的正方形

![世界磁贴左上方](./media/zoom-levels-and-tile-grid/world1a.png)     ![世界磁贴右上方](./media/zoom-levels-and-tile-grid/world1c.png) 

![世界磁贴左下方](./media/zoom-levels-and-tile-grid/world1b.png)     ![世界磁贴右下方](./media/zoom-levels-and-tile-grid/world1d.png) 


每个后续缩放级别都将前一个缩放级别的磁贴分为四份，从而创建一个 2 <sup>缩放</sup> x 2 <sup>缩放</sup>的网格。 缩放级别 20 是一个 2<sup>20</sup> x 2<sup>20</sup> 的网格，或 1,048,576 x 1,048,576 个磁贴（总共为 109,951,162,778 个磁贴）。

下表提供了缩放级别的完整列表值：

|缩放级别|计量/像素|计量/磁贴边|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|

通过缩放级别调用磁贴，x 和 y 坐标对应磁贴在该缩放级别的网格上的位置。

在确定要使用的具体缩放级别时，请记住每个位置在其磁贴上是固定的。 这意味着要显示给定范围区域所需的磁贴数量取决于世界上缩放网格的具体位置。 例如，如果有两个点相距 900 米，则可能仅在缩放级别 17 使用三个磁贴来显示这两点之间的路线。 但是，如果西边的点在磁贴的右边，而东边的点在磁贴的左边，则需要四个磁贴：

![缩放演示比例](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

缩放级别确定后，可以计算 x 和 y 值。 每个缩放网格的左上方磁贴为 x=0、y=0，右下方磁贴为 x=2<sup>缩放-1</sup>，y=2<sup>缩放-1</sup>。

以下是缩放级别 1 的缩放网格：

![缩放级别 1 的缩放网格](./media/zoom-levels-and-tile-grid/api_x_y.png)
