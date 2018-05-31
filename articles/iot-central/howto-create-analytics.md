---
title: 为 Azure IoT Central 应用程序创建自定义分析 | Microsoft Docs
description: 如何以操作员身份为 Azure IoT Central 应用程序创建自定义分析。
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 2f19998429daab9000e35b520673c417b4bc828a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199929"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>如何使用分析功能来分析设备数据

Microsoft Azure IoT Central 提供各种分析功能对来自设备的大量数据进行分析。 可以使用分析功能查看和分析应用程序中[设备集](howto-use-device-sets.md)的数据。 设备集是用户定义的设备组。 可以将分析范围缩小到小的设备集或单个设备。

以操作员身份依次选择左侧导航菜单上的“分析”、设备集、要在图上显示的度量。 下面是一些可以用来对数据进行进一步切分的方法：

* **度量：** 选择要显示的度量，例如温度和湿度。
* **聚合：** 选择度量的聚合函数。 例如，“最小值”或“平均值”。
* **拆分方式：** 按设备属性或设备名称拆分数据，以便向下钻取。 例如，按设备位置拆分：

     ![分析主页](media\howto-create-analytics\analytics-main.png)

* **时间范围：** 可以从预定义的时间范围中选择时间范围，也可以创建自定义时间范围：![分析时间范围](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>更改可视化效果

可以选择三种模式之一，根据自己的可视化效果要求对图进行更改：

* **堆积：** 将每个度量的图堆积在一起，每个图都有自己的 Y 轴。 在选择多个度量的情况下，如果需要以独立的视图来呈现这些度量，则可使用堆积图。
* **非堆积：** 使用一个 Y 轴为每个度量值绘图，但 Y 轴的值会随突出显示的度量值而更改。 如果需要将多个度量值重叠在一起，并且需要跨这些度量值查看同一时间范围的模式，则可使用非堆积图。
* **共享 Y 轴：** 所有图共享同一 Y 轴，轴的值不会更改。 如果需要在使用拆分方式对数据进行切分时查看单个度量值，则可使用共享 Y 轴图。

## <a name="next-steps"></a>后续步骤

了解如何创建适用于 Azure IoT Central 应用程序的自定义分析后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [准备和连接 Node.js 应用程序](howto-connect-nodejs.md)