---
title: Azure IoT 中心 - 开始将 IoT 设备连接到云 | Microsoft Docs
description: 了解如何将 IoT 板和初学者工具包连接到 Azure IoT 中心。 设备可以将遥测数据发送到 IoT 中心，IoT 中心可以监视和管理设备。
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
keywords: Azure IoT 中心教程
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: dafb8aca34a5a41f45f76d526aa3b8f3b1b792c4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Azure IoT 中心真实设备入门

可以使用 Azure IoT 中心和 Azure IoT 设备 SDK 生成物联网 (IoT) 解决方案：

* Azure IoT 中心是在云中完全托管的服务，可安全地连接、监视和管理 IoT 设备。 使用 Azure IoT 设备 SDK 实现 IoT 设备。
* 在更复杂的 IoT 方案中使用 IoT 网关。 例如在这些方案中需要考虑一些因素，如旧设备、带宽成本、安全性和隐私策略或边缘数据处理等。 在这些方案中，使用 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) 实现用于将设备连接到 IoT 中心的网关。

## <a name="what-the-how-to-articles-cover"></a>操作方法文章所涉及的内容

这些文章介绍 Azure IoT 中心和设备 SDK。 这些文章介绍用于演示 IoT 中心功能的常见 IoT 方案。 这些文章还说明了如何将 IoT 中心与其他 Azure 服务和工具结合在一起，以构建更强大的 IoT 解决方案。 在这些文章中，将使用真实 IoT 设备。

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
