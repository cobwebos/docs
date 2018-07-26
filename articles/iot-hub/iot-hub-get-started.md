---
title: Azure IoT 中心 - 开始将 IoT 设备连接到云 | Microsoft Docs
description: 了解如何将 IoT 板和初学者工具包连接到 Azure IoT 中心。 设备可以将遥测数据发送到 IoT 中心，IoT 中心可以监视和管理设备。
author: dominicbetts
manager: timlt
keywords: Azure IoT 中心教程
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 77abe7e2187a3cb28b326ffa833a856625d6c33d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125186"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Azure IoT 中心真实设备入门

这些操作说明文章介绍 Azure IoT 中心，以及如何在真实设备上运行设备 SDK。

## <a name="set-up-your-device"></a>设置设备

将 IoT 设备或网关连接到 Azure IoT 中心：

| IoT 设备                       | 编程语言 |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py]、[Node.js][Pi_Nd]、[C][Pi_C]  |
| IoT DevKit                       | [VSCode 中的 Arduino][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd]、[C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| 联机设备模拟器         | [Raspberry Pi (Node.js)][Ol_Sim] |

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
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
