---
title: Web SDK 支持的浏览器-Azure Maps |Microsoft Docs
description: 了解 Azure Maps Web SDK 支持的浏览器
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577333"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支持的浏览器

Azure Maps Web SDK 提供了一个帮助器函数[atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)来检测 web 浏览器是否具有所需的最低必需的 WebGL 功能以支持加载和呈现地图控件。 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌面型

Azure Maps Web SDK 支持以下桌面浏览器。

- 当前版本和以前版本的 Microsoft Edge 
- 当前和以前版本的 Chrome 
- 当前和以前版本的 Firefox 
- 当前和以前版本的 Safari (Mac OS X) 

另请参阅[面向旧版浏览器](#Target-Legacy-Browsers)。

## <a name="mobile"></a>移动型

Azure Maps Web SDK 支持以下移动浏览器。

-  Android
    * 在 Android 6.0 + 的当前版本的 Chrome
    * 在 Android 6.0 + 上的 chrome web 视图
- iOS
    * 移动当前和以前主要版本的 iOS 上的 Safari
    * UIWebView 和 WKWebView 上的 Io 的当前和以前的主版本
    * 适用于 iOS 的当前版本的 Chrome

> [!TIP]
> 如果要嵌入地图内使用 WebView 控件的移动应用程序，您可能更倾向于使用[npm 包的 Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control)而不是引用 CDN 托管版本的 SDK。 这将减少加载时间，因为 SDK 将用户的设备上已经有这些并不需要在运行时下载。

## <a name="nodejs"></a>Node.js

以下 Web SDK 模块还支持在 Node.js 中：

- 服务模块 ([文档](how-to-use-services-module.md) | [npm 模块](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>目标旧浏览器

如果需要面向较旧的浏览器可能不支持或提供有限的 WebGL 的支持，建议使用与开放源代码地图控件结合使用 Azure 地图服务，如[宣传册](https://leafletjs.com/)。 


<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + 宣传册" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + 宣传册</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解有关 Azure Maps Web SDK 的详细信息。

> [!div class="nextstepaction"]
> [地图控件](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [服务模块](how-to-use-services-module.md)
