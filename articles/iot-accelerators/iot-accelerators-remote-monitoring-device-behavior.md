---
title: 远程监视解决方案中的模拟设备行为 - Azure | Microsoft Docs
description: 本文介绍如何使用 JavaScript 来定义远程监视解决方案中模拟设备的行为。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: edd5da35d33aa7e79fc3e972f3ea1cfe2ef40973
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631407"
---
# <a name="implement-the-device-model-behavior"></a>实现设备模型的行为

[了解设备模型架构](iot-accelerators-remote-monitoring-device-schema.md)一文中介绍了定义模拟设备模型的架构。 该文章参考了实现模拟设备行为的两种 JavaScript 文件：

- 按固定间隔运行，更新设备内部状态的**状态** JavaScript 文件。
- 解决方案在设备上调用方法时运行的**方法** JavaScript 文件。

> [!NOTE]
> 设备模型行为仅适用于在设备模拟服务中托管的模拟设备。 如果想要创建物理设备，请参阅[将设备连接到远程监视解决方案加速器](iot-accelerators-connecting-devices.md)。

在本文中，学习如何：

>[!div class="checklist"]
> * 控制模拟设备的状态
> * 定义模拟设备如何响应来自远程监视解决方案的方法调用
> * 调试脚本

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>后续步骤

本文介绍了如何定义自己的自定义模拟设备模型的行为。 本文的内容包括：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 控制模拟设备的状态
> * 定义模拟设备如何响应来自远程监视解决方案的方法调用
> * 调试脚本

了解如何指定模拟设备的行为后，建议接下来继续学习如何[创建模拟设备](iot-accelerators-remote-monitoring-create-simulated-device.md)。

有关可供开发人员参考的远程监视解决方案详细信息，请参阅：

* [开发人员参考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [开发人员故障排除指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
