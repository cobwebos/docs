---
title: 使用 Azure Maps 构建可访问的应用程序 | Microsoft Docs
description: 如何使用 Azure Maps 构建可访问的应用程序
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 537a8c80dc0d1fcb2f536d0e30200de19a2111a4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867046"
---
# <a name="building-an-accessible-application"></a>构建可访问的应用程序

本文介绍如何构建可由屏幕阅读器使用的地图应用程序。

## <a name="understand-the-code"></a>了解代码

<iframe height='500' scrolling='no' title='构建可访问的应用程序' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>构建可访问的应用程序</a>。
</iframe>

地图是使用某些辅助功能预构建的。 用户可以使用键盘导航地图，如果屏幕阅读器正在运行，地图将通知用户其状态更改。 例如，平移或缩放地图时，将通知用户地图的新纬度、经度、显示比例和地点。 基本地图上的任何附加信息应具有适用于屏幕阅读器用户的相应文本信息。 使用[弹出项](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)是实现此目标的一种方法。 在上述搜索示例中，具有文本信息的弹出项将添加到地图上每个 PIN 对应的地图。 使用[弹出项](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)的附加方法允许通过屏幕阅读器查看弹出项，而不直观显示地图上的弹出项。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的弹出项类及其方法：

* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [attach](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [remove](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

查看我们的[代码示例页](http://aka.ms/AzureMapsSamples)，了解更多映射方案。
