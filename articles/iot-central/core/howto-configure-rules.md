---
title: 在 Azure IoT Central 中配置规则和操作 | Microsoft Docs
description: 本操作指南文章演示，作为构建者，如何在 Azure IoT Central 应用程序中配置基于遥测的规则和操作。
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8391202e991c240b03bd79b947c1c3c6dcc9afd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362531"
---
# <a name="configure-rules"></a>配置规则

*本文适用于操作员、构建者和管理员。*

IoT Central 中的规则充当可自定义的响应工具，针对已连接设备的主动监视事件进行触发。 以下部分介绍如何评估规则。

## <a name="select-target-devices"></a>选择目标设备

使用目标设备部分选择将此规则应用于的设备类型。 筛选器可以进一步优化应该包含的设备。 筛选器使用设备模板上的属性来筛选一组设备。 筛选器本身不触发操作。 在以下屏幕截图中，目标设备的设备模板类型是冰箱。   筛选器表明规则应只包含冰箱，其中“产地”属性等于“华盛顿”。

![条件](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>使用多个条件

条件是规则触发操作所根据的内容。 目前，向规则添加多个条件时，它们通过 AND 以逻辑方式合并在一起。 换句话说，必须满足所有条件，规则才能评估为 true。  

在下面的屏幕截图中，条件检查何时温度大于 70&deg; F 且湿度小于 10。 当这两个语句都为 true 时，规则的结果为 true，并触发操作。

![屏幕截图显示了一个冰箱监视器，其中包含为温度和湿度指定的条件。](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>在值字段中使用云属性

可以在“值”字段中为条件引用来自设备模板的云属性。 云属性和遥测值必须具有相似的类型。  例如，如果温度是一个 double 值，则只有 double 类型的云属性显示在“值”下拉列表的选项中。

 如果选择事件类型的遥测值，则“值”下拉列表包含“任一”选项。 “任一”选项意味着当应用程序收到该类型的事件时将触发规则（无论有效负载是什么）。

## <a name="use-aggregate-windowing"></a>使用聚合窗口

规则将聚合时间窗口评估为翻转窗口。 在下面的屏幕截图中，时间窗口为 5 分钟。 每隔五分钟，规则将评估过去五分钟的数据。 只有当数据位于其对应的窗口时才评估它。

![翻转窗口](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>将规则用于 IoT Edge 模块

应用于 IoT Edge 模块的规则有一个限制。 不同模块的遥测规则不会评估为有效规则。 请看以下示例。 规则的第一个条件是关于模块 A 中的温度遥测。规则的第二个条件是关于模块 B 中的湿度遥测。由于这两个条件来自不同的模块，因此这是一组无效的条件。 规则无效，在尝试保存规则时会引发错误。

## <a name="next-steps"></a>后续步骤

现在已了解如何在 Azure IoT Central 应用程序中配置规则，接下来将了解如何使用 Power Automate 或 Azure 逻辑应用[配置高级规则](howto-configure-rules-advanced.md)。
