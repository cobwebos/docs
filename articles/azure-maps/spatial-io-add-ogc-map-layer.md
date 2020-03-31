---
title: 添加开放地理空间联盟 （OGC） 地图图层 |微软 Azure 地图
description: 了解如何在地图上叠加 OGC 地图图层，以及如何在 OgcMapLayer 类中使用不同的选项。
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334288"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>从开放地理空间联盟 （OGC） 添加地图图层

该`atlas.layer.OgcMapLayer`类可以在地图上叠加 Web 地图服务 （WMS） 影像和 Web 地图切片服务 （WMTS） 影像。 WMS 是 OGC 为在互联网上提供地理参考地图图像而开发的标准协议。 图像地理参照是将图像与地理位置相关联的过程。 WMTS 也是 OGC 开发的标准协议。 它专为提供预渲染和地理参考地图切片而设计。

以下各节概述了`OgcMapLayer`类支持的 Web 地图服务功能。

**网络地图服务**

- 支持的版本： `1.0.0` `1.1.0`、 `1.1.1`、 和`1.3.0`
- 服务必须支持`EPSG:3857`投影系统，或处理重新投影。
- GetFeatureInfo 需要该服务来支持`EPSG:4326`或处理重新投影。 
- 支持的操作：

    | | |
    | :-- | :-- |
    | 获取功能 | 使用受支持的功能检索有关服务的元数据 |
    | 获取映射 | 检索指定区域的地图图像 |
    | 获取功能信息 | 检索`feature_info`，其中包含有关该功能的基础数据 |

**Web 地图磁贴服务 （WMTS）**

- 支持的版本：`1.0.0`
- 磁贴必须为正方形，`TileWidth == TileHeight`因此 。
- 支持 CRS：`EPSG:3857`或`GoogleMapsCompatible` 
- TileMatrix 标识符必须是对应于地图上缩放级别的整数值。 在 Azure 地图上，缩放级别是 和`"0"``"22"`之间的值。 因此，`"0"`受支持，但`"00"`不受支持。
- 支持的操作：

    | | |
    | :-- | :-- |
    | 获取功能 | 检索支持的操作和功能 |
    | GetTile | 检索特定磁贴的影像 |

## <a name="overlay-an-ogc-map-layer"></a>覆盖 OGC 地图图层

`url`可以是服务的基本 URL，也可以是具有获取服务功能的查询的完整 URL。 根据提供的详细信息，WFS 客户端可能会尝试几种标准 URL 格式来确定最初如何访问服务。

以下代码演示如何在地图上叠加 OGC 地图图层。

<br/>

<iframe height='700' scrolling='no' title='OGC 地图图层示例' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 的笔<a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC 地图图层示例</a>。
</iframe>

## <a name="ogc-map-layer-options"></a>OGC 地图图层选项

下面的示例演示了不同的 OGC 地图图层选项。 您可以单击右上角的代码笔按钮来编辑代码笔。

<br/>

<iframe height='700' scrolling='no' title='OGC 地图图层选项' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 地图<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC 地图图层选项</a>。
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web 地图服务资源管理器

以下工具将 Web 地图服务 （WMS） 和 Web 地图磁贴服务 （WMTS） 中的影像叠加为图层。 您可以选择服务中的哪些图层在地图上呈现。 您还可以查看这些图层的关联图例。

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web 地图服务资源管理器' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，通过 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看 Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web 地图服务资源管理器</a>。
</iframe>

您还可以指定地图设置以使用代理服务。 代理服务允许您加载托管在未启用 CORS 的域中的资源。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayer选项](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

请参阅以下文章，其中包含可以添加到地图的代码示例：

> [!div class="nextstepaction"]
> [连接到 WFS 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心业务](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)
