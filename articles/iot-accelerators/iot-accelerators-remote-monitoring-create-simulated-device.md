---
title: 带有 IoT 远程监视的设备模拟 - Azure | Microsoft Docs
description: 本操作说明指南介绍如何在远程监视解决方案加速器中使用设备模拟器。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 81efd9dc5d33ed23574b1cb66f26ccb444d5a3ff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180528"
---
# <a name="create-and-test-a-new-simulated-device"></a>创建并测试新的模拟设备

借助远程监视解决方案加速器，可以定义自己的模拟设备。 本文介绍如何定义新的模拟灯泡设备，然后在本地对其进行测试。 解决方案加速器包括诸如冷却器和卡车的模拟设备。 但是，可以定义自己的模拟设备以在部署实际设备之前对 IoT 解决方案进行测试。

> [!NOTE]
> 本文介绍如何使用设备模拟服务中托管的模拟设备。 如果想要创建真实设备，请参阅[将设备连接到远程监视解决方案加速器](iot-accelerators-connecting-devices.md)。

本操作说明指南介绍如何自定义设备模拟微服务。 此微服务是远程监视解决方案加速器的一部分。 为了演示设备模拟功能，本操作说明指南在 Contoso IoT 应用程序中使用了两个方案：

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>后续步骤

本指南介绍了如何创建自定义模拟设备类型，以及如何通过本地运行设备模拟微服务来对其进行测试。

建议的下一步是学习如何将自定义模拟设备类型部署到[远程监视解决方案加速器](iot-accelerators-remote-monitoring-deploy-simulated-device.md)。
