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
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686647"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支持的浏览器

Azure Maps Web SDK 提供了一个名为的帮助器函数[atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)。 此函数检测到 web 浏览器是否具有 WebGL 功能支持加载和呈现地图控件所需的最小集。 下面是如何使用该函数的示例：

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌面型

Azure Maps Web SDK 支持以下桌面浏览器：

- Microsoft Edge （当前和以前版本）
- Google Chrome （当前和以前版本）
- Mozilla Firefox （当前和以前版本）
- Apple Safari (Mac OS X) （当前和以前版本）

另请参阅[面向旧版浏览器](#Target-Legacy-Browsers)这篇文章中更高版本。

## <a name="mobile"></a>移动电话

Azure Maps Web SDK 支持以下移动浏览器：

- Android
  - 当前版本的 Chrome Android 6.0 及更高版本
  - Android 6.0 及更高版本的 chrome web 视图
- iOS
  - 移动当前和以前主要版本的 iOS 上的 Safari
  - UIWebView 和 WKWebView 上的 Io 的当前和以前的主版本
  - 适用于 iOS 的当前版本的 Chrome

> [!TIP]
> 如果要通过使用 WebView 控件嵌入在移动应用程序映射，您可能更倾向于使用[npm 包的 Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control)而不是引用托管在 Azure 内容交付的 sdk 版本网络。 这种方法可减少加载时间，因为 SDK 是已为用户的设备上，无需在运行时下载。

## <a name="nodejs"></a>Node.js

以下 Web SDK 模块还支持在 Node.js 中：

- 服务模块 ([文档](how-to-use-services-module.md) | [npm 模块](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>目标旧浏览器

你可能想要面向较旧的浏览器，不支持 WebGL，或者，仅具有有限的支持。 在这种情况下，我们建议使用 Azure Maps 服务，以及等的开放源代码地图控件[宣传册](https://leafletjs.com/)。 下面是一个示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + 宣传册" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + 宣传册</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解有关 Azure Maps Web SDK 的详细信息：

> [!div class="nextstepaction"]
> [地图控件](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [服务模块](how-to-use-services-module.md)
