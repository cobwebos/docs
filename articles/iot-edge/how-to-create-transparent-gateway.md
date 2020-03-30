---
title: 创建透明网关设备 - Azure IoT Edge | Microsoft Docs
description: 将 Azure IoT Edge 用作可处理来自下游设备的消息的透明网关
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6069e0782f69d0dfb73d9be2998cbb11d59d7d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529163"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

本文详细说明如何将 IoT Edge 设备配置为充当透明网关，供其他设备用来与 IoT 中心通信。 本文使用术语 *IoT Edge 网关*来指代配置为透明网关的 IoT Edge 设备。 有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](./iot-edge-as-gateway.md)。

>[!NOTE]
>当前：
>
> * 支持 Edge 的设备无法连接到 IoT Edge 网关。
> * 下游设备不能使用文件上传。

成功设置透明网关连接需要完成三个常规步骤。 本文介绍其中的第一个步骤：

1. **网关设备需要能够安全连接到下游设备，从下游设备接收通信，并将消息路由到正确的目标。**
2. 下游设备需有一个设备标识，才能在 IoT 中心进行身份验证并知道要通过其网关设备进行通信。 有关详细信息，请参阅[在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
3. 下游设备需安全连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

充当网关的设备必须能够安全地连接到下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 要维持合理的安全性，下游设备应确认网关设备的标识。 此标识检查可防止设备连接到潜在的恶意网关。

透明网关方案中的下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 在各种实际用途中，下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 但是，IoT Edge 设备不能位于 IoT Edge 网关的下游。

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们假设使用相同的证书设置来启用 IoT 中心的 [X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)，其中涉及与特定 IoT 中心（IoT 中心根 CA）关联的 X.509 CA 证书，以及通过此 CA 签名的一系列证书和 IoT Edge 设备的 CA。

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>在整篇文章中使用的术语“根 CA”是指 PKI 证书链最顶层的颁发机构公共证书，而不一定是联合证书颁发机构的证书根。 在许多情况下，它实际上是中间 CA 公共证书。

网关在连接启动期间向下游设备出示其 IoT Edge 设备 CA 证书。 下游设备检查以确保 IoT Edge 设备 CA 证书由根 CA 证书签名。 此过程允许下游设备确认网关是否来自受信任的源。

以下步骤将演示创建证书并将它们安装在网关上的正确位置的过程。 可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

配置了[生产证书](how-to-manage-device-certificates.md)的 Azure IoT Edge 设备。

## <a name="deploy-edgehub-to-the-gateway"></a>将 edgeHub 部署到网关

首次在设备上安装 IoT Edge 时，只会自动启动一个系统模块，即 IoT Edge 代理。 创建第一个部署后，还会再启动一个设备，即第二个系统模块（IoT Edge 中心）。

IoT Edge 中心负责接收来自下游设备的传入消息，并将它们路由到下一个目标。 如果 **edgeHub** 模块未在设备上运行，请为设备创建一个初始部署。 该部署看上去是空的，因为尚未添加任何模块，但它会确保运行这两个系统模块。

可通过以下方式检查设备上正在运行的模块：在 Azure 门户中检查设备详细信息，在 Visual Studio 或 Visual Studio Code 中查看设备状态，或在该设备上运行命令 `iotedge list`。

如果 **edgeAgent** 模块在没有 **edgeHub** 模块的情况下运行，请执行以下步骤：

1. 在 Azure 门户中导航到 IoT 中心。

2. 转到“IoT Edge”并选择要用作网关的 IoT Edge 设备****。

3. 选择**设置模块**。

4. 选择“下一步”。

5. 在“指定路由”页中，应有一个默认路由可将来自所有模块的所有消息发送到 IoT 中心。**** 如果没有，请添加以下代码，然后选择“下一步”。****

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. 在“审阅模板”页中选择“提交”********。

## <a name="open-ports-on-gateway-device"></a>在网关设备上打开端口

标准 IoT Edge 设备不需要任何入站连接便可工作，因为与 IoT 中心之间的所有通信都是通过出站连接执行的。 网关设备则不同，因为它们需要从其下游设备接收消息。 如果下游设备与网关设备之间有防火墙，则也需要能够通过防火墙进行通信。

要使网关方案能够正常工作，必须为来自下游设备的入站流量打开 IoT Edge 中心的至少一个受支持协议。 受支持的协议包括 MQTT、AMQP、HTTPS、基于 WebSocket 的 MQTT 和基于 Websocket 的 AMQP。

| 端口 | 协议 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>路由来自下游设备的消息

IoT Edge 运行时可以像模块发送的消息一样路由从下游设备发送的消息。 使用此功能可将任何数据发送到云之前在网关上运行的模块中执行分析。

目前，对由下游设备发送的消息进行路由的方式是将它们与由模块发送的消息区分开来。 由模块发送的消息全都包含名为“connectionModuleId”的系统属性，但由下游设备发送的消息则不包含此属性。**** 可以使用路由的 WHERE 子句以排除包含该系统属性的任何消息。

以下示例路由可将消息从任何下游设备发送到名为 `ai_insights` 的模块，然后从 `ai_insights` 发送到 IoT 中心。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

有关消息路由的详细信息，请参阅[部署模块和建立路由](./module-composition.md#declare-routes)。

## <a name="enable-extended-offline-operation"></a>启用扩展脱机操作

从 IoT Edge 运行时 [v1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)开始，可配置网关设备和与之连接的下游设备，以处理扩展脱机操作。

借助此功能，本地模块或下游设备可根据需要向 IoT Edge 设备重新进行身份验证，即使从 IoT 中心断开连接也可使用消息和方法相互进行通信。 有关详细信息，请参阅[了解 IoT Edge 设备、模块和子设备的扩展脱机功能](offline-capabilities.md)。

若要启用扩展脱机功能，请在 IoT Edge 网关设备和要与之连接的下游设备之间建立父子关系。 [在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)中更详细地介绍了这些步骤。

## <a name="next-steps"></a>后续步骤

配置一个充当透明网关的 IoT Edge 设备后，需将下游设备配置为信任该网关，并向其发送消息。 继续[在 Azure IoT 中心内对下游设备进行身份验证](how-to-authenticate-downstream-device.md)，以进行设置透明网关方案的后续步骤。
