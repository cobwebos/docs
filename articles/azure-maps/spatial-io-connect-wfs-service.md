---
title: 连接到 Web 功能服务 （WFS） 服务 |微软 Azure 地图
description: 了解如何连接到 WFS 服务，然后使用 Azure 地图 Web SDK 和空间 IO 模块查询 WFS 服务。
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334206"
---
# <a name="connect-to-a-wfs-service"></a>连接到 WFS 服务

Web 要素服务 （WFS） 是一种用于查询具有由开放地理空间联盟 （OGC） 定义的标准化 API 的空间数据的 Web 服务。 空间`WfsClient`IO 模块中的类允许开发人员连接到 WFS 服务并从该服务查询数据。

`WfsClient`类支持以下功能：

- 支持的版本： `1.0.0` `1.1.0`、 和`2.0.0`
- 支持的筛选器运算符：二进制比较、逻辑、数学、值和`bbox`。
- 仅使用`HTTP GET`请求。
- 支持的操作：

    | | |
    | :-- | :-- |
    | 获取功能 | 生成具有有效 WFS 操作和参数的元数据文档 |
    | 获取功能 | 从数据源返回一系列要素 |
    | 描述功能类型 | 返回受支持的要素类型 |

## <a name="using-the-wfs-client"></a>使用 WFS 客户端

空间`atlas.io.ogc.WfsClient`IO 模块中的类便于查询 WFS 服务并将响应转换为 GeoJSON 对象。 然后，此 GeoJSON 对象可用于其他映射目的。

以下代码查询 WFS 服务并呈现地图上返回的要素。

<br/>

<iframe height='700' scrolling='no' title='简单 WFS 示例' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>简单 WFS 示例</a>。
</iframe>

## <a name="supported-filters"></a>支持的筛选器

WFS 标准的规范使用 OGC 滤波器。 假定被调用的服务也支持这些筛选器，则 WFS 客户端支持以下筛选器。 自定义筛选器字符串可以传递到类中`CustomFilter`。

**逻辑运算符**

- `And`
- `Or`
- `Not`

**价值运算符**

- `GmlObjectId`
- `ResourceId`

**数学运算符**

- `Add`
- `Sub`
- `Mul`
- `Div`

**比较运算符**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

以下代码演示了对 WFS 客户端使用不同的筛选器。

<br/>

<iframe height='500' scrolling='no' title= 'WFS 筛选器示例' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS 筛选器示例</a>。
</iframe>

## <a name="wfs-service-explorer"></a>WFS 服务资源管理器

以下代码使用 WFS 客户端来探索 WFS 服务。 选择服务中的属性类型图层，并查看关联的图例。

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS 服务资源管理器' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，通过 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看 Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS 服务资源管理器</a>。
</iframe>

要访问托管在未启用 CORS 的终结点上的 WFS 服务，可以将启用 CORS 的`proxyService`代理服务传递到 WFS 客户端的选项中，如下所示。 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [Wfs服务选项](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [利用核心业务](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)
