---
title: 在 Azure IoT Central 中配置规则和操作 | Microsoft Docs
description: 本操作指南文章介绍构建人员如何在 Azure IoT Central 应用程序中配置基于遥测的规则和操作。
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f6e85ba5aafaad973d28f799a251b6f3aae548e3
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871944"
---
# <a name="configure-rules"></a>配置规则

*本文适用于操作员、构建者和管理员。*

IoT Central 中的规则充当一种可自定义的响应手段，它们是基于已连接设备发生的、有效监视到的事件触发的。 以下部分介绍如何评估规则。

## <a name="select-target-devices"></a>选择目标设备

使用“目标设备”部分选择要对哪种类型的设备应用此规则。 使用筛选器可以进一步具体化要包含的设备。 筛选器使用设备模板中的属性来筛选设备集。 筛选器本身不会触发操作。 在以下屏幕截图中，目标设备的设备模板类型为“冰箱”。  该筛选器指出，规则只能包含“制造地所在州”属性为“华盛顿”的“冰箱”。   

![条件](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>使用多个条件

条件是规则的触发依据。 目前，在将多个条件添加到规则时，这些条件将以逻辑 AND 运算符联接到一起。 换言之，必须满足所有条件才会将规则评估为 true。  

在下面的屏幕截图中，当温度大于 70&deg; F 并且湿度小于10时，将检查条件。 如果这两个语句均为 true，则规则将评估为 true 并触发操作。

![条件](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>在值字段中使用云属性

您可以通过 "**值**" 字段中的 "设备" 模板引用某个条件的云属性。 Cloud 属性和遥测值必须具有相似的类型。 例如，如果**温度**为 double，则仅在 "**值**" 下拉箭头中的 "double" 类型的云属性为 "选项"。

如果选择事件类型 "遥测" 值，则 "**值**" 下拉选项将包含选项**Any**。 **Any**选项表示当应用程序接收到该类型的事件时引发的规则，无论负载是什么。

## <a name="use-aggregate-windowing"></a>使用聚合开窗

规则将聚合时间窗口作为翻转窗口评估。 在以下屏幕截图中，时间窗口为 5 分钟。 每隔五分钟根据过去五分钟的数据评估规则。 数据只会在其对应的窗口中评估一次。

![翻转窗口](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>在 IoT Edge 模块中使用规则

应用到 IoT Edge 模块的规则存在一种限制。 基于来自不同模块的遥测数据的规则不会评估为有效规则。 下面是一个示例。 该规则的第一个条件是模块 A 的温度遥测数据。该规则的第二个条件是模块 B 的湿度遥测数据。由于这两个条件来自不同的模块，因此这是一组无效的条件。 该规则无效，尝试保存该规则时会引发错误。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中配置规则后，可以：

> [!div class="nextstepaction"]
> [即时分析数据](howto-create-analytics.md)
