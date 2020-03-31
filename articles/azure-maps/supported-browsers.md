---
title: 支持 Web SDK 的浏览器 |微软 Azure 地图
description: 在本文中，您将了解 Microsoft Azure 地图 Web SDK 受支持的浏览器以及如何检查浏览器是否受支持的浏览器。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988781"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支持的浏览器

Azure 地图 Web SDK 提供了一个称为[atlas.is 支持的](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)帮助器函数。 此功能检测 Web 浏览器是否具有支持加载和呈现地图控件所需的最小 WebGL 功能集。 下面是如何使用 函数的示例：

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌面

Azure 映射 Web SDK 支持以下桌面浏览器：

- 微软边缘（当前版本和以前的版本）
- 谷歌Chrome（当前版本和以前的版本）
- Mozilla 火狐（当前和以前的版本）
- 苹果野生动物园（Mac OS X）（当前和以前的版本）

请参阅本文后面的[目标旧版浏览器](#Target-Legacy-Browsers)。

## <a name="mobile"></a>移动电话

Azure 地图 Web SDK 支持以下移动浏览器：

- Android
  - Android 6.0 及更高版本的 Chrome
  - 在 Android 6.0 及更高版本上查看 Chrome 网络视图
- iOS
  - iOS 当前和以前的主要版本的移动 Safari
  - UIWebView 和 WKWebView 在当前和以前的 iOS 主要版本上
  - 适用于 iOS 的当前版本的 Chrome

> [!TIP]
> 如果使用 WebView 控件将地图嵌入移动应用程序中，则可能更喜欢使用 Azure 地图 Web SDK[的 npm 包](https://www.npmjs.com/package/azure-maps-control)，而不是引用 Azure 内容交付网络上托管的 SDK 版本。 此方法减少了加载时间，因为 SDK 已在用户的设备上，并且不需要在运行时下载。

## <a name="nodejs"></a>Node.js

Node.js 中还支持以下 Web SDK 模块：

- 服务模块 （[文档](how-to-use-services-module.md) | [npm 模块](https://www.npmjs.com/package/azure-maps-rest)）

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>目标旧版浏览器

您可能希望将目标对准不支持 WebGL 或仅对 WebGL 支持有限的较旧的浏览器。 在这种情况下，我们建议您将 Azure 地图服务与开源地图控件（如["传单"）](https://leafletjs.com/)一起使用。 下面是一个示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure 地图 + 传单" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 显示的笔<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure 地图和传单</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解有关 Azure 地图 Web SDK 的更多内容：

> [!div class="nextstepaction"]
> [地图控件](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [服务模块](how-to-use-services-module.md)
