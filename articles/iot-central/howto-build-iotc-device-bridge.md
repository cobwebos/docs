---
title: 构建 Azure IoT Central 设备网桥 | Microsoft Docs
description: 构建 IoT Central 设备网桥，以将其他 IoT 云（Sigfox、Particle、The Things Network 等）连接到你的 IoT Central 应用。
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 246c9ad8ab3083c1b847c1c25230a7193a8192e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59263564"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>构建 IoT Central 设备网桥以将其他 IoT 云连接到 IoT Central

*本主题适用于管理员。*

IoT Central 设备网桥是一个开源解决方案，可将 Sigfox、Particle、The Things Network 和其他云连接到你的 IoT Central 应用。 无论使用的是连接到 Sigfox 的低功耗广域网的资产跟踪设备、Particle Device Cloud 中的空气质量监视设备，还是 TTN 中的土壤湿度监视设备，都可以直接通过 IoT Central 设备网桥来利用 IoT Central 的强大功能。 设备网桥通过将设备发送到其他云的数据转发到 IoT Central 应用，使其他 IoT 云可连接到 IoT Central。 在 IoT Central 应用中，可以生成规则并对该数据运行分析、在 Microsoft Flow 和 Azure 逻辑应用中创建工作流、导出该数据，等等。 从 GitHub 获取 [IoT Central 设备网桥](https://aka.ms/iotcentralgithubdevicebridge)

## <a name="what-is-it-and-how-does-it-work"></a>设备网桥及其工作原理是什么？
IoT Central 设备网桥是 GitHub 中的一个开源解决方案。 随时使用“部署到 Azure”按钮可将包含多个 Azure 资源的自定义 Azure 资源管理器模板部署到 Azure 订阅。 资源包括：
-   Azure 函数应用
-   Azure 存储帐户
-   使用计划
-   Azure 密钥保管库

Function app 是不可或缺的设备桥。 函数应用通过简单的 Webhook 集成从其他 IoT 平台或任何自定义平台接收 HTTP POST 请求。 我们提供了示例用于演示如何连接到 Sigfox、Particle 和 TTN 云。 如果平台可向函数应用发送 HTTP POST 请求，则你可以轻松扩展此解决方案，以连接到自定义 IoT 云。
函数应用将数据转换为 IoT Central 接受的格式，并通过 DPS API 转发此数据。

![Azure Functions 屏幕截图](media/howto-build-iotc-device-bridge/azfunctions.png)

如果 IoT Central 应用可按转发消息中的设备 ID 识别设备，将显示该设备的新度量值。 如果 IoT Central 应用从未看到过设备 ID，则函数应用会尝试使用该设备 ID 注册新设备，该设备在 IoT Central 应用中显示为“未关联的设备”。 

## <a name="how-do-i-set-it-up"></a>如何设置设备网桥？
GitHub 存储库中的自述文件详细列出了说明。 

## <a name="pricing"></a>定价
Azure 资源将托管在 Azure 订阅中。 可以从[自述文件](https://aka.ms/iotcentralgithubdevicebridge)中了解有关定价的详细信息。

## <a name="next-steps"></a>后续步骤
了解如何构建 IoT Central 设备网桥后，建议接下来阅读以下文章：

> [!div class="nextstepaction"]
> [管理设备](howto-manage-devices.md)
