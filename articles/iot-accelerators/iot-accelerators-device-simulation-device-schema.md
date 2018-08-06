---
title: 设备模拟解决方案中的设备架构 - Azure | Microsoft Docs
description: 本文介绍了用于在设备模拟解决方案中定义模拟设备的 JSON 架构。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: a27624d4c7a0bde4b33aefe8d05881b743ce397d
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295174"
---
# <a name="understand-the-device-model-schema"></a>了解设备模型架构

可以使用设备模拟解决方案加速器为使用 IoT 中心的解决方案生成测试负载。 部署设备模拟解决方案时，会自动预配一个模拟设备集合。 我们可以自定义现有的模拟设备，或创建自己的模拟设备。

本文介绍指定模拟设备的功能和行为的设备模型架构。 设备模型存储在 JSON 文件中。

以下文章与本文相关：

* [实现设备模型行为](iot-accelerators-device-simulation-device-behavior.md)介绍了可用于实现模拟设备行为的 JavaScript 文件。
* [创建新模拟设备](iot-accelerators-remote-monitoring-test.md)将所有相关内容统合在一起，介绍如何在解决方案中部署新的模拟设备类型。

在本文中，学习如何：

>[!div class="checklist"]
> * 使用 JSON 文件定义模拟设备模型
> * 指定模拟设备的属性
> * 指定模拟设备发送的遥测数据
> * 指定设备响应的云到设备方法

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建自己的自定义模拟设备模型。 本文的内容包括：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 使用 JSON 文件定义模拟设备模型
> * 指定模拟设备的属性
> * 指定模拟设备发送的遥测数据
> * 指定设备响应的云到设备方法

了解 JSON 架构后，建议接下来继续学习如何[实现模拟设备的行为](iot-accelerators-device-simulation-device-behavior.md)。

有关设备模拟解决方案的更多开发人员信息，请参阅[开发人员参考指南](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide)。
