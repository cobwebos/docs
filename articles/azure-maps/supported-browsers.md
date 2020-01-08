---
title: Web SDK 支持的浏览器-Azure Maps |Microsoft Docs
description: 了解 Azure Maps Web SDK 支持的浏览器
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6aa256f026033a8b204b3c2ab1080f9f63ef63d7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432786"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支持的浏览器

Azure Maps Web SDK 提供了名为[isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)的 helper 函数。 此函数检测 web 浏览器是否具有支持加载和呈现地图控件所需的最小 WebGL 功能集。 下面的示例演示如何使用函数：

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌面型

Azure Maps Web SDK 支持以下桌面浏览器：

- Microsoft Edge （当前和以前的版本）
- Google Chrome （当前版本和以前版本）
- Mozilla Firefox （当前和以前的版本）
- Apple Safari （Mac OS X）（当前和以前的版本）

另请参阅本文后面的[面向旧版浏览器](#Target-Legacy-Browsers)。

## <a name="mobile"></a>移动

Azure Maps Web SDK 支持以下移动浏览器：

- Android
  - Android 6.0 及更高版本上的当前 Chrome 版本
  - Android 6.0 及更高版本上的 Chrome Web 视图
- iOS
  - 当前和以前的 iOS 主版本上的移动 Safari
  - 当前和以前的 iOS 主版本上的 UIWebView 和 WKWebView
  - IOS 的当前版本 Chrome

> [!TIP]
> 如果要使用 web 视图控件在移动应用程序中嵌入地图，你可能更倾向于使用[Azure Maps WEB SDK 的 npm 包](https://www.npmjs.com/package/azure-maps-control)，而不是引用在 Azure 内容分发网络上托管的 SDK 版本。 此方法可减少加载时间，因为 SDK 已在用户的设备上，并且无需在运行时下载。

## <a name="nodejs"></a>Node.js

Node.js 还支持以下 Web SDK 模块：

- 服务模块（[文档](how-to-use-services-module.md) | [npm 模块](https://www.npmjs.com/package/azure-maps-rest)）

## <a name="Target-Legacy-Browsers"></a>定位旧版浏览器

你可能想要面向不支持 WebGL 的旧版浏览器，或仅限对其的支持。 在这种情况下，我们建议你结合使用 Azure Maps 服务和开源地图控件，如[Leaflet](https://leafletjs.com/)。 下面是一个示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a>按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>


## <a name="next-steps"></a>后续步骤

了解有关 Azure Maps Web SDK 的详细信息：

> [!div class="nextstepaction"]
> [地图控件](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [服务模块](how-to-use-services-module.md)
