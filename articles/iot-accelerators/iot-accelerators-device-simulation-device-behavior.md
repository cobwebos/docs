---
title: 设备模拟解决方案中的模拟设备行为 - Azure | Microsoft Docs
description: 本文介绍了如何使用 JavaScript 来定义设备模拟解决方案中模拟设备的行为。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b68550bce1f4e3fbe3845c21598720083c8e384c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295170"
---
# <a name="implement-the-device-model-behavior"></a>实现设备模型的行为

[了解设备模型架构](iot-accelerators-device-simulation-device-schema.md)一文中介绍了定义模拟设备模型的架构。 该文章参考了实现模拟设备行为的两种 JavaScript 文件：

- **状态**：以固定时间间隔运行来更新设备的内部状态的 JavaScript 文件。
- **方法**：解决方案在设备上调用某个方法时运行的 JavaScript 文件。

在本文中，学习如何：

>[!div class="checklist"]
> * 控制模拟设备的状态
> * 定义模拟设备如何响应来自它连接到的 IoT 中心的方法调用
> * 调试脚本

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>后续步骤

本文介绍了如何定义自己的自定义模拟设备模型的行为。 本文的内容包括：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 控制模拟设备的状态
> * 定义模拟设备如何响应来自它连接到的 IoT 中心的方法调用
> * 调试脚本

了解如何指定模拟设备的行为后，建议接下来继续学习如何[创建模拟设备](iot-accelerators-remote-monitoring-test.md)。

有关设备模拟解决方案的更多开发人员信息，请参阅[开发人员参考指南](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide)。
