---
title: 用于 Azure IoT Central 的设备开发 | Microsoft Docs
description: Azure IoT Central 是一种 IoT 应用程序平台，用于简化创建 IoT 解决方案。 本文概述了如何开发设备以连接到 IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 6fabd7d8cf5c19f05bd31c2d0b12863fd6e25382
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017517"
---
# <a name="iot-central-device-development-overview"></a>IoT Central 设备开发概述

本文适用于设备开发人员。

IoT Central 应用程序使你可以在设备整个生命周期内监视和管理数百万台设备。 本概述适用于要执行以下操作的设备开发人员：实现代码以在连接到 IoT Central 的设备上运行。

设备使用以下基元与 IoT Central 应用程序进行交互：

- “遥测”是设备发送给 IoT Central 的数据。 例如，来自内载传感器的温度值流。
- “属性”是设备报告给 IoT Central 的状态值。 例如，设备的当前固件版本。 你还可以拥有可写属性，IoT Central 可在设备上对其进行更新。
- “命令”由 IoT Central 进行调用以控制设备的行为。 例如，IoT Central 应用程序可能会调用命令来重启设备。

解决方案生成器负责在 IoT Central Web UI 中配置仪表板和视图，以直观呈现遥测、管理属性和调用命令。

## <a name="types-of-device"></a>设备类型

以下部分介绍了可连接到 IoT Central 应用程序的设备的主要类型：

### <a name="standalone-device"></a>独立设备

独立设备可直接连接到 IoT Central。 独立设备通常会将遥测从其内载或联网的传感器发送到 IoT Central 应用程序， 并且还可以报告属性值、接收可写属性值以及响应命令。

### <a name="gateway-device"></a>网关设备

网关设备管理连接到 IoT Central 应用程序的一个或多个下游设备。 可使用 IoT Central 配置下游设备与网关设备之间的关系。 若要了解详细信息，请参阅[在 Azure IoT Central 应用程序中定义新的 IoT 网关设备类型](./tutorial-define-gateway-device-type.md)。

### <a name="edge-device"></a>边缘设备

边缘设备直接连接到 IoT Central，但充当其他设备（称为“叶设备”）的中介。 边缘设备通常位于叶设备（边缘设备充当其中介）附近。 使用边缘设备的场景包括：

- 使无法直接连接到 IoT Central 的设备能够通过边缘设备连接。 例如，叶设备可能使用蓝牙连接到边缘设备，后者随后通过 Internet 连接到 IoT Central。
- 在将遥测发送到 IoT Central 之前，对其进行聚合。 此方法有助于降低向 IoT Central 发送数据的成本。
- 本地控制叶设备，避免与通过 Internet 连接到 IoT Central 相关的延迟。

边缘设备还可以发送自己的遥测，报告其属性，并响应可写的属性更新和命令。

IoT Central 仅可看到边缘设备，看不到连接到边缘设备的叶设备。

若要了解详细信息，请参阅[将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序](./tutorial-add-edge-as-leaf-device.md)。

## <a name="connect-a-device"></a>连接一个设备

Azure IoT Central 使用 [Azure IoT 中心设备预配服务 (DPS)](../../iot-dps/about-iot-dps.md) 来管理所有设备注册和连接。

使用 DPS：

- 可让 IoT Central 支持大规模的设备加入和连接。
- 可让你离线生成设备凭据并配置设备，而无需通过 IoT Central UI 注册设备。
- 可让你使用自己的设备 ID 在 IoT Central 中注册设备。 使用自己的设备 ID 可以简化与现有后端办公系统的集成。
- 可以通过一致的单一方式将设备连接到 IoT Central。

有关详细信息，请参阅[连接到 Azure IoT Central](./concepts-get-connected.md)。

### <a name="security"></a>安全性

设备与 IoT Central 应用程序之间的连接使用[共享访问签名](./concepts-get-connected.md#connect-devices-at-scale-using-sas)或[行业标准 X.509 证书](./concepts-get-connected.md#connect-devices-using-x509-certificates)来得到保护。

### <a name="communication-protocols"></a>通信协议

设备可用于连接到 IoT Central 的通信协议包括 MQTT、AMQP 和 HTTPS。 在内部，IoT Central 使用 IoT 中心来启用设备连接。 有关 IoT 中心针对设备连接支持的通信协议的详细信息，请参阅[选择通信协议](../../iot-hub/iot-hub-devguide-protocols.md)。

## <a name="implement-the-device"></a>实现设备

使用 [Azure IoT 设备 SDK](#languages-and-sdks) 中的一个来实现设备行为。 代码应做到以下几点：

- 将设备注册到 DPS，并使用 DPS 中的信息连接到 IoT Central 应用程序中的内部 IoT 中心。
- 以 IoT Central 中的设备模板指定的格式发送遥测。 IoT Central 使用设备模板来确定如何使用遥测，以便实现可视化效果和分析。
- 同步设备和 IoT Central 之间的属性值。 设备模板指定属性名称和数据类型，以便 IoT Central 可以显示信息。
- 为设备模板中指定的命令实现命令处理程序。 设备模板指定设备应使用的命令名称和参数。

有关设备模板的角色的详细信息，请参阅[什么是设备模板？](./concepts-device-templates.md)。

有关一些示例代码，请参阅[创建并连接 Node.js 客户端应用程序](./tutorial-connect-device-nodejs.md)或[创建并连接 Python 客户端应用程序](./tutorial-connect-device-python.md)。

### <a name="languages-and-sdks"></a>语言和 SDK

有关支持的语言和 SDK 的详细信息，请参阅[了解和使用 Azure IoT 中心设备 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)。

## <a name="next-steps"></a>后续步骤

如果你是设备开发人员并想深入了解某种代码，建议执行的下一步骤是[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device-nodejs.md)。

如果希望了解有关使用 IoT Central 的详细信息，建议的后续步骤是尝试快速入门，从[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)开始。
