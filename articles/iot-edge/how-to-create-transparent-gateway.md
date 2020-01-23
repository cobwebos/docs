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
ms.openlocfilehash: bf60bfb41e48220845e9aa26dc26f20e6ed60d16
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510679"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

本文详细说明了如何将 IoT Edge 设备配置为可用作其他设备与 IoT 中心通信的透明网关。 本文使用术语 " *IoT Edge 网关*" 引用配置为透明网关的 IoT Edge 设备。 有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](./iot-edge-as-gateway.md)。

>[!NOTE]
>当前：
>
> * 支持 Edge 的设备无法连接到 IoT Edge 网关。
> * 下游设备不能使用文件上传。

设置成功透明的网关连接有三个常规步骤。 本文介绍第一步：

1. **网关设备需要能够安全地连接到下游设备，接收来自下游设备的通信，并将消息路由到适当的目标。**
2. 下游设备需要具有设备标识，才能通过 IoT 中心进行身份验证，并且知道要通过其网关设备进行通信。 有关详细信息，请参阅[对 Azure IoT 中心的下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
3. 下游设备需要安全连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

要使设备充当网关，需要能够安全地连接到其下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 为了保持合理的安全性，下游设备应确认网关设备的标识。 此标识检查可防止你的设备连接到可能的恶意网关。

透明网关方案中的下游设备可以是具有使用[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 在各种实际用途中，下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 但是，IoT Edge 设备不能是 IoT Edge 网关的下游。

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们将采用与在 IoT 中心中启用[X.509 ca 安全性](../iot-hub/iot-hub-x509ca-overview.md)相同的证书设置，其中包括与特定 IoT 中心关联的 x.509 ca 证书（iot 中心根 CA）、一系列使用此 CA 签名的证书，以及 IoT Edge 设备的 CA。

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>本文中使用的 "根 CA" 一词是指 PKI 证书链的最顶层颁发机构公共证书，不一定是联合证书颁发机构的证书根目录。 在许多情况下，它实际上是一个中间 CA 公共证书。

网关在连接启动过程中向下游设备提供其 IoT Edge 设备 CA 证书。 下游设备将进行检查以确保 IoT Edge 设备 CA 证书已由根 CA 证书签名。 此过程允许下游设备确认网关来自受信任的源。

以下步骤将引导完成创建证书的过程，并将其安装在网关上的适当位置。 可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。

## <a name="prerequisites"></a>必备组件

Azure IoT Edge 设备，使用[生产证书](how-to-install-production-certificates.md)进行配置。

## <a name="deploy-edgehub-to-the-gateway"></a>将 edgeHub 部署到网关

首次在设备上安装 IoT Edge 时，只会自动启动一个系统模块： IoT Edge 代理。 创建第一个部署后，还会启动第二个系统模块 IoT Edge 集线器。

IoT Edge 中心负责接收来自下游设备的传入消息，并将这些消息路由到下一个目标。 如果**edgeHub**模块未在你的设备上运行，请为你的设备创建初始部署。 部署将显示为空，因为你没有添加任何模块，但它将确保两个系统模块都在运行。

通过在 Azure 门户中检查设备的详细信息、在 Visual Studio 中查看设备状态或 Visual Studio Code，或在设备本身上运行命令 `iotedge list`，可以检查设备上运行的模块。

如果在没有**edgeHub**模块的情况下运行**edgeAgent**模块，请使用以下步骤：

1. 在 Azure 门户中导航到 IoT 中心。

2. 转到“IoT Edge”并选择要用作网关的 IoT Edge 设备。

3. 选择“设置模块”。

4. 选择“**下一页**”。

5. 在“指定路由”页中，应有一个默认路由可将来自所有模块的所有消息发送到 IoT 中心。 如果没有，请添加以下代码，然后选择“下一步”。

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. 在“审阅模板”页中选择“提交”。

## <a name="open-ports-on-gateway-device"></a>打开网关设备上的端口

标准 IoT Edge 设备不需要任何入站连接即可正常工作，因为与 IoT 中心之间的所有通信都是通过出站连接来完成的。 网关设备不同，因为它们需要从其下游设备接收消息。 如果防火墙位于下游设备和网关设备之间，则还需要通过防火墙进行通信。

要使网关方案正常工作，必须为来自下游设备的入站流量打开至少一个 IoT Edge 集线器支持的协议。 受支持的协议包括： MQTT、AMQP、HTTPS、MQTT over Websocket 和大于 Websocket 的 AMQP。

| Port | 协议 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>路由来自下游设备的消息

IoT Edge 运行时可以像模块发送的消息一样路由从下游设备发送的消息。 此功能可让你在将任何数据发送到云之前，在网关上运行的模块中执行分析。

目前，对由下游设备发送的消息进行路由的方式是将它们与由模块发送的消息区分开来。 由模块发送的消息全都包含名为“connectionModuleId”的系统属性，但由下游设备发送的消息则不包含此属性。 可以使用路由的 WHERE 子句以排除包含该系统属性的任何消息。

下面的路由是一个示例，该示例将消息从任何下游设备发送到名为 `ai_insights`的模块，然后从 `ai_insights` 发送到 IoT 中心。

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

从 IoT Edge 运行时的[v 1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)开始，可以为连接到该网关设备的网关设备和下游设备配置扩展脱机操作。

利用此功能，本地模块或下游设备可以根据需要重新进行身份验证，并使用消息和方法彼此通信，即使在从 IoT 中心断开连接时也 IoT Edge 是如此。 有关详细信息，请参阅[了解 IoT Edge 设备、模块和子设备的扩展脱机功能](offline-capabilities.md)。

若要启用扩展的脱机功能，请在将连接到 IoT Edge 网关设备与下游设备之间建立父子关系。 [向 Azure IoT 中心验证下游设备的身份](how-to-authenticate-downstream-device.md)中更详细地介绍了这些步骤。

## <a name="next-steps"></a>后续步骤

配置一个充当透明网关的 IoT Edge 设备后，需将下游设备配置为信任该网关，并向其发送消息。 继续在[Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)，了解设置透明网关方案的后续步骤。
