---
title: "Azure IoT 中心 - 开始将 IoT 设备连接到云 | Microsoft Docs"
description: "了解如何将 IoT 设备连接到 Azure IoT 中心。 设备可以将遥测数据发送到 IoT 中心，IoT 中心可以监视和管理设备。"
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
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: aeb0b665b8295bba30d8c6c47cc88e446693c91f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/01/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT 中心入门教程

可以使用 Azure IoT 中心和 Azure IoT 设备 SDK 生成物联网 (IoT) 解决方案。

* Azure IoT 中心是在云中完全托管的服务，可安全地连接、监视和管理 IoT 设备。 使用 Azure IoT 设备 SDK 实现 IoT 设备。
* 在更复杂的 IoT 方案中使用 IoT 网关，在这些方案中需要考虑一些因素，例如旧设备、带宽成本、安全和隐私策略或边缘数据处理等。 在这些方案中，可以使用 Azure IoT 网关 SDK 生成用于将设备连接到 IoT 中心的网关设备。

## <a name="what-do-the-tutorials-cover"></a>教程涵盖内容

这些教程介绍 Azure IoT 中心和设备 SDK。 这些教程介绍用于演示 IoT 中心功能的常见 IoT 方案。 这些教程还说明了如何将 IoT 中心与其他 Azure 服务和工具结合在一起，以构建更强大的 IoT 解决方案。 在这些教程中，可以选择是使用模拟 IoT 设备还是使用真实 IoT 设备。 此外，还可以了解如何使用网关使设备能够连接到 IoT 中心。

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>设备安装方案：将 IoT 设备或网关连接到 Azure IoT 中心

可以选择是使用真实设备还是使用模拟设备开始。

| IoT 设备                       | 编程语言 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd]、[C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd]、[C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| 模拟设备                | [.NET][Sim_NET]、[Java][Sim_Jav]、[Node.js][Sim_Nd]、[Python][Sim_Pyth]              |

此外，还可以使用网关使设备能够连接到 IoT 中心。

| 网关设备               | 编程语言 | 平台         |
|------------------------------|----------------------|------------------|
| Intel NUC（模型 DE3815TYKE） | C                    | [Wind River Linux][NUC_Lnx] |
| 模拟网关            | C                    | [Linux][Sim_Lnx]、[Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>扩展 IoT 方案：使用其他 Azure 服务和工具

将设备连接到 IoT 中心后，可以浏览使用其他 Azure 工具和服务的其他方案：

| 方案                                    | Azure 服务或工具              |
|---------------------------------------------|------------------------------------|
| [管理 IoT 中心消息][Mg_IoT_Hub_Msg]                    | iothub-explorer 工具               |
| [管理 IoT 设备][Mg_IoT_Dv]               | iothub-explorer 工具               |
| [将 IoT 中心消息保存到 Azure 存储][Sv_IoT_Msg_Stor]                      | Azure 表存储               |
| [直观显示传感器数据][Vis_Data]             | Microsoft Power BI、Azure Web 应用 |
| [使用传感器数据预测天气][Weather_Forecast] | Azure 机器学习             |
| [自动异常检测和反应][Anomaly_Detect]    | Azure 逻辑应用                   |

## <a name="next-steps"></a>后续步骤

完成这些教程后，可以在[开发人员指南][lnk-dev-guide]中进一步浏览 IoT 中心的功能。 可以在[操作方法][lnk-how-to]部分中找到其他教程。


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
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
[Sim_Lnx]: iot-hub-linux-gateway-sdk-get-started.md
[Sim_Win]: iot-hub-windows-gateway-sdk-get-started.md
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
