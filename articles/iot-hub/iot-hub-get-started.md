---
title: "Azure IoT 中心 - 开始将 IoT 设备连接到云 | Microsoft Docs"
description: "了解如何将 IoT 板和初学者工具包连接到 Azure IoT 中心。 设备可以将遥测数据发送到 IoT 中心，IoT 中心可以监视和管理设备。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT 中心教程"
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 45016e6383761ffe78f13ccef1112ab3d9753498
ms.contentlocale: zh-cn
ms.lasthandoff: 08/25/2017

---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT 中心入门教程

可以使用 Azure IoT 中心和 Azure IoT 设备 SDK 生成物联网 (IoT) 解决方案：

* Azure IoT 中心是在云中完全托管的服务，可安全地连接、监视和管理 IoT 设备。 使用 Azure IoT 设备 SDK 实现 IoT 设备。
* 在更复杂的 IoT 方案中使用 IoT 网关。 例如在这些方案中需要考虑一些因素，如旧设备、带宽成本、安全性和隐私策略或边缘数据处理等。 在这些方案中，可以使用 Azure IoT Edge 实现用于将设备连接到 IoT 中心的网关。

## <a name="what-the-tutorials-cover"></a>教程涵盖内容

这些教程介绍 Azure IoT 中心和设备 SDK。 这些教程介绍用于演示 IoT 中心功能的常见 IoT 方案。 这些教程还说明了如何将 IoT 中心与其他 Azure 服务和工具结合在一起，以构建更强大的 IoT 解决方案。 在这些教程中，可以选择是使用模拟 IoT 设备还是使用真实 IoT 设备。 此外，还可以了解如何使用网关使设备能够连接到 IoT 中心。

## <a name="set-up-your-device"></a>设置设备

将 IoT 设备或网关连接到 Azure IoT 中心。 可以选择是使用物理设备还是使用模拟设备开始：

| IoT 设备                       | 编程语言 |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py]、[Node.js][Pi_Nd]、[C][Pi_C]  |
| IoT DevKit                       | [VSCode 中的 Arduino][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd]、[C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| PC 上的模拟设备           | [.NET][Sim_NET]、[Java][Sim_Jav]、[Node.js][Sim_Nd]、[Python][Sim_Pyth] |
| 联机设备模拟器         | [Raspberry Pi (Node.js)][Ol_Sim] |

此外，还可以使用 IoT Edge 网关使设备能够连接到 IoT 中心：

| 网关设备               | 编程语言 | 平台         |
|------------------------------|----------------------|------------------|
| Intel NUC（模型 DE3815TYKE） | C                    | [Wind River Linux][NUC_Lnx] |
| 模拟网关            | C                    | [Linux][Sim_Lnx]、[Windows][Sim_Win] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md

