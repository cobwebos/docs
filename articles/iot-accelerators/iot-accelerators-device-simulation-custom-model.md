---
title: 配置自定义设备模型 - Azure | Microsoft Docs
description: 本文介绍如何在设备模拟解决方案加速器中配置自定义设备模型。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967468"
---
# <a name="device-models"></a>设备模型

配置模拟时，可以选择将现有的设备模型与一组预定义的传感器结合使用，或使用所选的模拟传感器来创建自定义设备模型。 自定义传感器可让你更准确地设置真实设备的模型。

## <a name="pre-configured-device-models"></a>预配置的设备模型

设备模拟解决方案加速器提供了三个预配置的设备模型：冷却器、升降机和卡车。

预配置的设备模型包括多个具有 JavaScript 文件中定义的高级行为的传感器。 不能在 Web UI 中配置这些行为。

下表显示了适用于每个预配置设备模型的配置：

| 设备型号  | 传感器           | 单位  |
| ------------- | ---------------- | ----- |
| 冷却器       | 湿度         | %     |
|               | 压力         | psig  |
|               | 温度      | F     |
| 升降机      | 层            |       |
|               | 振动        | mm    |
|               | 温度      | F     |
| 卡车         | 纬度         |       |
|               | 经度        |       |
|               | 速度            | mph   |
|               | cargotemperature | F     |

## <a name="custom-device-models"></a>自定义设备模型

可以通过自定义设备模型来设置传感器的模型，使之更能准确地设置自己设备的模型。 一个自定义设备可以有多达 10 个自定义传感器。

选择自定义设备模型类型时，可以通过单击“+ 添加传感器”来添加新的传感器。

[![配置传感器](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

自定义传感器具有以下属性：

| 字段     | 说明 |
| --------- | ----------- |
| 传感器名称 | 传感器的易记名称，例如“温度”或“速度”。  |
| 行为  | 行为促使消息的遥测数据各不相同，这样是为了模拟真实世界的数据。 **递增**使发送的每条消息中的值加 1（从最小值开始）。 达到最大值以后，会从最小值重新开始。 **递减**的行为方式与**递增**相同，只是每次减 1。 **随机**行为生成一个介于最小值和最大值之间的随机值。 |
| 最小值 | 可接受范围的最小数字。 |
| 最大值 | 可接受范围的最大数字。 |
| 单位      | 传感器的度量单位，例如 °F 或 MPH。 |

## <a name="next-steps"></a>后续步骤

在本操作方法指南中，你已了解如何配置用于模拟的自定义设备模型。 接下来，你可能会想要了解其他一些 [IoT 解决方案加速器](about-iot-accelerators.md)。