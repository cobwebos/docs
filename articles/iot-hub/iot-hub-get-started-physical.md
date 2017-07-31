---
title: "将物理设备连接到 Azure IoT 中心入门 | Microsoft Docs"
description: "了解如何将物理设备和板连接到 Azure IoT 中心。 设备可以将遥测数据发送到 IoT 中心，IoT 中心可以监视和管理设备。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT 中心教程"
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: dce80d060538adc061149f686b71992a09d190da
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Azure IoT 中心物理设备入门教程

这些教程介绍 Azure IoT 中心和设备 SDK。 这些教程介绍用于演示 IoT 中心功能的常见 IoT 方案。 这些教程还说明了如何将 IoT 中心与其他 Azure 服务和工具结合在一起，以构建更强大的 IoT 解决方案。 下表中列出的教程介绍了如何创建物理 IoT 设备。

| IoT 设备                       | 编程语言 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd]、[C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd]、[C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

此外，还可以使用 IoT Edge 网关使设备能够连接到 IoT 中心。

| 网关设备               | 编程语言 | 平台         |
|------------------------------|----------------------|------------------|
| Intel NUC（模型 DE3815TYKE） | C                    | [Wind River Linux][NUC_Lnx] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

