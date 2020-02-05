---
title: 在 Azure IoT Central 中配置规则和操作 | Microsoft Docs
description: 本操作指南文章介绍了如何在 Azure IoT Central 应用程序中配置基于遥测的规则和操作。
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ca308522d0c6b9c3c5b39d73a2f4a278a5b20db8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026986"
---
# <a name="configure-rules"></a>配置规则



*本文适用于操作员、构建者和管理员。*

IoT Central 中的规则充当可自定义的响应工具，该工具会在来自连接设备的主动监视事件上触发。 以下部分介绍如何计算规则。

## <a name="select-target-devices"></a>选择目标设备

使用 "目标设备" 部分可以选择将应用此规则的设备类型。 利用筛选器，您可以进一步优化应该包含的设备。 筛选器使用设备模板上的属性来筛选设备集。 筛选器本身不会触发操作。 在以下屏幕截图中，目标设备的设备模板类型为 "**冰箱**"。 该筛选器表明规则应该只包含**制造状态**属性等于 "**华盛顿**" 的**冰箱**。

![条件](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>使用多个条件

条件是规则的触发条件。 目前，当你向规则添加多个条件时，它们将以逻辑方式和组合在一起。 换句话说，必须满足所有条件，规则才能评估为 true。  

在下面的屏幕截图中，当温度大于 70&deg; F，湿度小于10时，将检查条件。 当这两个语句都为 true 时，规则的计算结果为 true，并触发操作。

![条件](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>使用聚合窗口

规则将聚合时间窗口评估为翻转 windows。 在下面的屏幕截图中，时间窗口为5分钟。 每隔五分钟，规则将在过去五分钟的数据上计算。 数据只在其对应的窗口中计算一次。

![翻转 Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>将规则用于 IoT Edge 模块

限制适用于应用于 IoT Edge 模块的规则。 不同模块的遥测规则不会评估为有效规则。 请以下面的示例为例。 规则的第一个条件是模块 A 中的温度遥测。规则的第二个条件是模块 B 上的湿度遥测。由于这两个条件来自不同的模块，因此这是一组无效的条件。 规则无效，在尝试保存规则时会引发错误。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中配置规则，你可以：

> [!div class="nextstepaction"]
> [动态分析数据](howto-create-analytics.md)
