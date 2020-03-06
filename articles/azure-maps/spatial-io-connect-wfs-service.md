---
title: 连接到 Web 功能服务（WF）服务 |Microsoft Azure 映射
description: 了解如何连接到 WF 服务，然后使用 Azure Maps web SDK 和空间 IO 模块查询 WF 服务。
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 18ac583837c7cb8b2dabbfa6f7d7210c8afe3fcb
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402753"
---
# <a name="connect-to-a-wfs-service"></a>连接到 WF 服务

Web 功能服务（WF）是一个 web 服务，用于查询具有标准化 API 的空间数据（已由开放地理空间信息联盟（OGC）定义）。 使用空间 IO 模块中的 `WfsClient` 类，开发人员可以连接到 WF 服务并查询该服务中的数据。

`WfsClient` 类支持以下功能：

- 支持的版本： `1.0.0`、`1.1.0`和 `2.0.0`
- 支持的筛选器运算符：二进制比较、逻辑、数学、值和 `bbox`。
- 只使用 `HTTP GET` 进行请求。
- 支持的操作：

    | | |
    | :-- | :-- |
    | GetCapabilities | 使用有效的 WF 操作和参数生成元数据文档 |
    | GetFeature | 返回数据源中的选定功能 |
    | DescribeFeatureType | 返回支持的功能类型 |

## <a name="using-the-wfs-client"></a>使用 WF 客户端

使用空间 IO 模块中的 `atlas.io.ogc.WfsClient` 类可以轻松地查询 WF 服务并将响应转换为 GeoJSON 对象。 然后，可以将此 GeoJSON 对象用于其他映射目的。

下面的代码查询 WF 服务，并在地图上呈现返回的功能。

<br/>

<iframe height='700' scrolling='no' title='简单的 WF 示例' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 WF 上的 "笔简单的<a href='https://codepen.io'>" Azure Maps</a><a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>示例</a>（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="supported-filters"></a>支持的筛选器

WF 标准规范使用 OGC 筛选器。 WF 客户端支持以下筛选器，假定调用的服务还支持这些筛选器。 自定义筛选器字符串可以传递到 `CustomFilter` 类。

**逻辑运算符**

- `And`
- `Or`
- `Not`

**值运算符**

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

下面的代码演示如何将不同筛选器与 WF 客户端一起使用。

<br/>

<iframe height='500' scrolling='no' title= 'WF 筛选器示例' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>wf 筛选器示例</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="wfs-service-explorer"></a>WF 服务资源管理器

以下代码使用 WF 客户端来浏览 WF 服务。 选择服务中的属性类型层，并查看关联的图例。

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WF 服务资源管理器' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的 " <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>wf" 服务资源管理器</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

你还可以使用代理服务加载在不启用 CORs 的域上托管的资源。 首先定义一个变量来保存代理服务 url，并为 WF 客户端设置 `proxyService` 选项。 若要为用户呈现代理服务选项，请将用户输入添加到 UI。 在单击输入时加载服务 url。 以下代码片段演示如何使用代理服务。

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

以下 HTML 代码段对应于上面的 JavaScript 代码：

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [利用核心操作](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)
