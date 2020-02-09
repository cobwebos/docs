---
title: 使用 Azure IoT Central 构建的用水量监测解决方案的参考体系结构 | Microsoft Docs
description: 了解使用 Azure IoT Central 构建的用水量监测解决方案的概念。
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3a64ca5b0c2a092f895873e097ea6beb9a235a37
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017694"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>水消耗量检测参考体系结构 



可以将 Azure IoT Central 应用模板用作快速入门 IoT 应用程序来构建用水量监测解决方案  。 本文提供有关构建端到端解决方案的概要性参考体系结构指南。 

![用水量监测体系结构](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

概念：

1. 设备和连接  
1. IoT Central 
2. 扩展性和集成
3. 业务应用程序

让我们了解一些关键组件，它们往往在用水量监测解决方案中发挥着重要的作用。

## <a name="devices-and-connectivity"></a>设备和连接 
本部分一般将用于智能水务解决方案（例如水质监测或用水量监测）的设备称为智能水务设备。 智能水务设备可以是流量计、水质监测仪、智能阀门、检漏仪等。

智能水务解决方案中使用的设备一般将通过第三方网络运营商提供的低功耗广域网 (LPWAN) 进行连接。 对于此类设备，可以使用 [Azure IoT Central 设备网桥](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)将设备数据发送到 Azure IoT Central 中的 IoT 应用程序。 还可以使用支持 IP 的设备网关，或者直接连接到 IoT Central。

## <a name="iot-central"></a>IoT Central 
Azure IoT Central 是一个 IoT 应用平台，可让你快速启动和运行 IoT 解决方案。 你可以设计、自定义解决方案，并将其与第三方服务相集成。
将智能用水务设备连接到 IoT Central 后，你将获得设备命令与控制、监视和警报、带有内置 RBAC 的用户界面、可配置的见解仪表板和扩展性选项。 


## <a name="extensibility-and-integrations"></a>扩展性和集成 
可以在 IoT Central 中扩展 IoT 应用程序，并可根据需要执行以下操作：
* 转换和集成 IoT 数据以进行高级分析，例如，通过从 IoT Central 应用程序持续导出的数据训练机器学习模型
* 通过 Microsoft Flow 或 IoT Central 应用程序中的 Webhook 触发操作，在其他系统中自动执行工作流
* 通过 IoT Central API 以编程方式访问 IoT Central 中的 IoT 应用程序

## <a name="business-applications"></a>业务应用程序 
IoT 数据可用于为自来水公司中的各种业务应用程序提供支持。 若要了解如何将 IoT Central 用水量监测应用程序与现场服务相连接，请参阅有关[如何与 Dynamics 365 现场服务相集成](./how-to-configure-connected-field-services.md)的教程 


## <a name="next-steps"></a>后续步骤
* 了解如何[创建用水量](./tutorial-water-consumption-monitoring.md) IoT Central 应用程序
* 详细了解[政府用 IoT Central 模板](./overview-iot-central-government.md)
* 若要详细了解 IoT Central，请参阅 [IoT Central 概述](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
