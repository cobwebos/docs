---
title: "Azure IoT 中心入门 | Microsoft 文档"
description: "IoT 中心服务入门方法"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d7360c3d41a0e4cf0d182e510d6bc2fccd915dd1
ms.openlocfilehash: 51ac0c0d3a91070fc8f5b3892409af838e91068e
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-iot-hub-or-azure-iot-gateway-sdk"></a>Azure IoT 中心或 Azure IoT 网关 SDK 入门

可以从多个 IoT 中心服务或网关 SDK 入门教程中选择一个。

## <a name="iot-hub"></a>IoT 中心

Azure IoT 中心是一项完全托管的服务，可在数百万台物联网 (IoT) 设备和一个解决方案后端之间实现安全可靠的双向通信。

使用 IoT 中心服务时，可以采用以下方法来入门：

- 按教程操作，使用在开发计算机上运行的模拟设备。 选择一个入门教程，使用首选的编程语言：[.NET][lnk-dotnet]、[Java][lnk-java] 或 [Node.js][lnk-nodejs]。

- 按教程使用一个物理设备。 选择一个入门教程，使用首选的硬件平台：[Raspberry Pi][lnk-rasp-pi]、[Intel Edison][lnk-edison] 或 [Arduino][lnk-arduino]。 这些教程介绍了获取硬件设备的方法。

- 阅读[适用于 C 语言的 Azure IoT 设备 SDK 简介][lnk-c-intro]一文，了解如何使用 C 语言开发 IoT 设备。

## <a name="gateway-sdk"></a>网关 SDK

可以使用网关 SDK 生成自定义现场网关。 网关执行多种任务，例如运行分析、制定时间敏感型决策以减少延迟、提供设备管理服务、强制实施安全和隐私约束，以及执行协议转换。

网关 SDK 入门可以采用以下方法：

- 按教程操作，使用在开发计算机上运行的模拟网关。 可以选择针对 [Linux][lnk-linux] 或 [Windows][lnk-windows] 的入门教程。

- 按教程使用一个物理设备。 可以选择一个入门教程，以便使用[带 Intel NUC（下一代计算单元）的模拟设备][lnk-gateway-sim]，或者使用[带 Intel NUC 的 SensorTag 设备][lnk-gateway-tag]。

## <a name="next-steps"></a>后续步骤

完成入门教程以后，可以阅读[开发人员指南][lnk-devguide]和[操作方法][lnk-howto]这两个教程，了解 IoT 中心和网关 SDK 的更多功能。

[lnk-dotnet]: ./iot-hub-csharp-csharp-getstarted.md
[lnk-java]: ./iot-hub-java-java-getstarted.md
[lnk-nodejs]: ./iot-hub-node-node-getstarted.md
[lnk-c-intro]: ./iot-hub-device-sdk-c-intro.md
[lnk-rasp-pi]: ./iot-hub-raspberry-pi-kit-node-get-started.md
[lnk-edison]: ./iot-hub-intel-edison-kit-node-get-started.md
[lnk-arduino]: ./iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[lnk-linux]: ./iot-hub-linux-gateway-sdk-get-started.md
[lnk-windows]: ./iot-hub-windows-gateway-sdk-get-started.md
[lnk-gateway-sim]: ./iot-hub-gateway-kit-c-sim-get-started.md
[lnk-gateway-tag]: ./iot-hub-gateway-kit-c-get-started.md
[lnk-devguide]: ./iot-hub-devguide.md
[lnk-howto]: ./iot-hub-how-to.md

