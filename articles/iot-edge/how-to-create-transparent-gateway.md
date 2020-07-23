---
title: 创建透明网关设备 - Azure IoT Edge | Microsoft Docs
description: 将 Azure IoT Edge 用作可处理来自下游设备的消息的透明网关
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0155294777e1d732e5ff3874102b90049d9a123d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782579"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

本文详细说明如何将 IoT Edge 设备配置为充当透明网关，供其他设备用来与 IoT 中心通信。 本文使用术语 *IoT Edge 网关*来指代配置为透明网关的 IoT Edge 设备。 有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](./iot-edge-as-gateway.md)。

>[!NOTE]
>当前：
>
> * 支持 Edge 的设备无法连接到 IoT Edge 网关。
> * 下游设备不能使用文件上传。

成功设置透明网关连接需要完成三个常规步骤。 本文介绍其中的第一个步骤：

1. **将网关设备配置为服务器，以便下游设备能够安全地连接到该设备。设置网关以从下游设备接收消息，并将其路由到适当的目标。**
2. 为下游设备创建设备标识，使其能够使用 IoT 中心进行身份验证。 配置下游设备，使其通过网关设备发送消息。 有关详细信息，请参阅[在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
3. 将下游设备连接到网关设备并开始发送消息。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

对于作为网关的设备，需要安全地连接到其下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 要维持合理的安全性，下游设备应确认网关设备的标识。 此标识检查可防止设备连接到潜在的恶意网关。

下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 这些应用程序通常使用[Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 但是，IoT Edge 设备不能位于 IoT Edge 网关的下游。

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们假设使用相同的证书设置来启用 IoT 中心的 [X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)，其中涉及与特定 IoT 中心（IoT 中心根 CA）关联的 X.509 CA 证书，以及通过此 CA 签名的一系列证书和 IoT Edge 设备的 CA。

>[!NOTE]
>在这些文章中使用的术语 "*根 CA 证书*" 是指 PKI 证书链的最顶层颁发机构公共证书，不一定是联合证书颁发机构的证书根目录。 在许多情况下，它实际上是中间 CA 公共证书。

以下步骤将演示创建证书并将它们安装在网关上的正确位置的过程。 可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

安装了 IoT Edge 的 Linux 或 Windows 设备。

## <a name="set-up-the-device-ca-certificate"></a>设置设备 CA 证书

所有 IoT Edge 的网关都需要在其上安装设备 CA 证书。 IoT Edge 安全守护程序使用 IoT Edge 设备 CA 证书为工作负载 CA 证书签名，而工作负载 CA 证书又为 IoT Edge 中心的服务器证书签名。 在连接启动期间，网关将其服务器证书提供给下游设备。 下游设备将进行检查，确保服务器证书是汇总到根 CA 证书的证书链的一部分。 此过程允许下游设备确认网关是否来自受信任的源。 有关详细信息，请参阅[了解 Azure IoT Edge 如何使用证书](iot-edge-certs.md)。

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

根 CA 证书和设备 CA 证书（及其私钥）必须位于 IoT Edge 网关设备上，并在 IoT Edge yaml 文件中配置。 请记住，在这种情况下，*根 CA 证书*表示此 IoT Edge 方案的最顶层证书颁发机构。 网关设备 CA 证书和下游设备证书需要汇总到相同的根 CA 证书。

>[!TIP]
>在[IoT Edge 设备上管理证书](how-to-manage-device-certificates.md)中更详细地介绍了在 IoT Edge 设备上安装根 ca 证书和设备 CA 证书的过程。

准备好以下文件：

* 根 CA 证书
* 设备 CA 证书
* 设备 CA 私钥

对于生产方案，你应该用自己的证书颁发机构生成这些文件。 对于开发和测试方案，可以使用演示证书。

1. 如果使用的是演示证书，请使用以下一组步骤来创建文件：
   1. [创建根 CA 证书](how-to-create-test-certificates.md#create-root-ca-certificate)。 在这些说明结束时，你将拥有一个根 CA 证书文件：
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.

   2. [创建 IoT Edge 设备 CA 证书](how-to-create-test-certificates.md#create-iot-edge-device-ca-certificates)。 在这些说明结束时，你将拥有两个文件：一个设备 CA 证书及其私钥：
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem`与
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. 如果在其他计算机上创建了这些文件，请将它们复制到 IoT Edge 设备上。

3. 在 IoT Edge 设备上，打开安全守护程序配置文件。
   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

4. 查找文件的 "**证书**" 部分，并将三个文件的文件 uri 作为以下属性的值提供：
   * **device_ca_cert**：设备 ca 证书
   * **device_ca_pk**：设备 ca 私钥
   * **trusted_ca_certs**：根 ca 证书

5. 保存并关闭该文件。

6. 重新启动 IoT Edge。
   * Windows：`Restart-Service iotedge`
   * Linux：`sudo systemctl restart iotedge`

## <a name="deploy-edgehub-to-the-gateway"></a>将 edgeHub 部署到网关

首次在设备上安装 IoT Edge 时，只会自动启动一个系统模块，即 IoT Edge 代理。 为设备创建第一个部署后，还会启动第二个系统模块 IoT Edge 集线器。

IoT Edge 中心负责接收来自下游设备的传入消息，并将它们路由到下一个目标。 如果 **edgeHub** 模块未在设备上运行，请为设备创建一个初始部署。 该部署看上去是空的，因为尚未添加任何模块，但它会确保运行这两个系统模块。

可通过以下方式检查设备上正在运行的模块：在 Azure 门户中检查设备详细信息，在 Visual Studio 或 Visual Studio Code 中查看设备状态，或在该设备上运行命令 `iotedge list`。

如果 **edgeAgent** 模块在没有 **edgeHub** 模块的情况下运行，请执行以下步骤：

1. 在 Azure 门户中导航到 IoT 中心。

2. 转到“IoT Edge”并选择要用作网关的 IoT Edge 设备  。

3. 选择“设置模块”  。

4. 选择 "**下一步：路由**"。

5. 在 "**路由**" 页上，应该有一个默认路由，该路由将所有消息从模块或下游设备发送到 IoT 中心。 否则，请使用以下值添加新的路由，然后选择 "**查看 + 创建**"：
   * **名称**：`route`
   * **值**：`FROM /messages/* INTO $upstream`

6. 在 "**查看**" 和 "创建" 页上，选择 "**创建**"。

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

目前，对由下游设备发送的消息进行路由的方式是将它们与由模块发送的消息区分开来。 由模块发送的消息全都包含名为“connectionModuleId”的系统属性，但由下游设备发送的消息则不包含此属性。  可以使用路由的 WHERE 子句以排除包含该系统属性的任何消息。

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

从 IoT Edge 运行时的[1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)开始，可以为连接到它的网关设备和下游设备配置扩展脱机操作。

借助此功能，本地模块或下游设备可根据需要向 IoT Edge 设备重新进行身份验证，即使与 IoT 中心断开连接也可使用消息和方法相互进行通信。 有关详细信息，请参阅[了解 IoT Edge 设备、模块和子设备的扩展脱机功能](offline-capabilities.md)。

若要启用扩展脱机功能，请在 IoT Edge 网关设备和要与之连接的下游设备之间建立父子关系。 本系列的下一篇文章中详细介绍了这些步骤，并对[Azure IoT 中心的下游设备进行身份验证](how-to-authenticate-downstream-device.md)。

## <a name="next-steps"></a>后续步骤

现在，你已将 IoT Edge 设备设置为透明网关，你需要将下游设备配置为信任该网关，并向其发送消息。 继续[在 Azure IoT 中心内对下游设备进行身份验证](how-to-authenticate-downstream-device.md)，以进行设置透明网关方案的后续步骤。
