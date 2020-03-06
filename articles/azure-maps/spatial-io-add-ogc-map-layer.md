---
title: 添加开放地理空间信息联盟（OGC）地图层 |Microsoft Azure 映射
description: 了解如何覆盖地图上的 OGC 地图层，以及如何使用 OgcMapLayer 类中的不同选项。
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c59376344d2df7e9e9c76e630a4462b26343d187
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371007"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>从开放地理空间信息联盟添加地图层（OGC）

`atlas.layer.OgcMapLayer` 类可以覆盖地图上的 Web 映射服务（WMS）图像和 Web 映射磁贴服务（WMTS）图像。 WMS 是由 OGC 开发的一种标准协议，用于通过 internet 提供 georeferenced 地图图像。 Image georeferencing 是将映像关联到地理位置的过程。 WMTS 也是 OGC 开发的标准协议。 它旨在提供预呈现的地图磁贴和 georeferenced 地图图块。

以下部分概述了 `OgcMapLayer` 类支持的 Web 映射服务功能。

**Web 映射服务（WMS）**

- 支持的版本： `1.0.0`、`1.1.0`、`1.1.1`和 `1.3.0`
- 此服务必须支持 `EPSG:3857` 投影系统，或者服务必须能够处理 reprojections。
- GetFeatureInfo 要求服务支持 `EPSG:4326` 或句柄 reprojections。 
- 支持的操作：

    | | |
    | :-- | :-- |
    | GetCapabilities | 检索有关具有支持的功能的服务的元数据 |
    | GetMap | 检索指定区域的地图图像 |
    | GetFeatureInfo | 检索 `feature_info`，其中包含有关该功能的基本数据 |

**Web 映射磁贴服务（WMTS）**

- 支持的版本： `1.0.0`
- 磁贴必须是正方形，以便 `TileWidth == TileHeight`。
- 支持 CRS： `EPSG:3857` 或 `GoogleMapsCompatible` 
- TileMatrix 标识符必须是对应于地图上缩放级别的整数值。 在 azure 地图上，缩放级别是介于 `"0"` 和 `"22"`之间的值。 因此支持 `"0"`，但不支持 `"00"`。
- 支持的操作：

    | | |
    | :-- | :-- |
    | GetCapabilities | 检索支持的操作和功能 |
    | GetTile | 检索特定磁贴的图像 |

## <a name="overlay-an-ogc-map-layer"></a>覆盖 OGC 地图层

`url` 可以是服务的基 URL，也可以是包含用于获取服务功能的查询的完整 URL。 根据提供的详细信息，WF 客户端可能会尝试使用几种标准 URL 格式来确定如何首次访问服务。

下面的代码演示如何覆盖地图上的 OGC 地图层。

<br/>

<iframe height='700' scrolling='no' title='OGC 地图层示例' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC 地图层示例</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="ogc-map-layer-options"></a>OGC 地图层选项

下面的示例演示了不同的 OGC 地图层选项。 可以单击右上角的 "代码笔" 按钮来编辑代码笔。

<br/>

<iframe height='700' scrolling='no' title='OGC 地图层选项' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC 地图层选项</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web 映射服务资源管理器

以下工具将来自 Web 地图服务（WMS）和 Web 地图磁贴服务（WMTS）的图像叠加为层。 您可以选择在地图上呈现的服务中的层。 你还可以查看这些层的关联图例。

<br/>

<iframe height='700' scrolling='no' title='OGC Web 映射服务资源管理器' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web 映射服务资源管理器</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

你还可以指定要使用代理服务的映射设置。 代理服务允许你加载在未启用 CORs 的域上托管的资源。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

请参阅以下文章，其中包含可以添加到映射的代码示例：

> [!div class="nextstepaction"]
> [连接到 WF 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心操作](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)
