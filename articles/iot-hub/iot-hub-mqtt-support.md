---
title: 了解 Azure IoT 中心 MQTT 支持 | Microsoft Docs
description: 开发人员指南 - 支持设备使用 MQTT 协议连接到面向设备的 IoT 中心终结点。 介绍了 Azure IoT 设备 SDK 中的内置 MQTT 支持。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 2b200692610302bb135982e5419dcda36d5cfe60
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271156"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>使用 MQTT 协议与 IoT 中心通信

IoT 中心允许设备通过以下方式与 IoT 中心设备终结点通信：

* 端口8883上的[MQTT v 3.1.1](https://mqtt.org/)
* 在端口 443 上使用基于 WebSocket 的 MQTT v3.1.1。

IoT 中心不是功能完备的 MQTT 中转站，并未支持 MQTT v3.1.1 标准中指定的所有行为。 本文介绍设备如何使用受支持的 MQTT 行为来与 IoT 中心通信。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

所有通过 IoT 中心进行的设备通信都必须使用 TLS/SSL 来保护。 因此，IoT 中心不支持通过端口 1883 进行的不安全的连接。

## <a name="connecting-to-iot-hub"></a>连接到 IoT 中心

设备可以使用 MQTT 协议通过以下任意选项连接到 IoT 中心。

* [Azure IoT sdk](https://github.com/Azure/azure-iot-sdks)中的库。
* MQTT 协议。

许多公司和教育网络环境中的 MQTT 端口（8883）被阻止。 如果无法在防火墙中打开端口8883，建议使用 MQTT over Web Socket。 Web 套接字上的 MQTT 通过端口443进行通信，该端口几乎始终在网络环境中打开。 若要了解如何在使用 Azure IoT Sdk 时使用 Web 套接字协议指定 MQTT 和 MQTT，请参阅[使用设备 sdk](#using-the-device-sdks)。

## <a name="using-the-device-sdks"></a>使用设备 SDK

支持 MQTT 协议的[设备 sdk](https://github.com/Azure/azure-iot-sdks)可用于 Java、Node.js、C、 C#和 Python。 设备 SDK 使用标准 IoT 中心连接字符串来连接到 IoT 中心。 要使用 MQTT 协议，必须将客户端协议参数设置为 **MQTT**。 你还可以在客户端协议参数中通过 Web 套接字指定 MQTT。 默认情况下，设备 SDK 在 **CleanSession** 标志设置为 **0** 的情况下连接到 IoT 中心，并使用 **QoS 1** 来与 IoT 中心交换消息。

当设备连接到 IoT 中心时，设备 SDK 会提供方法，让设备与 IoT 中心交换消息。

下表包含指向每个受支持语言的代码示例的链接，并指定要用于使用 MQTT 或 MQTT over Web Socket 协议建立与 IoT 中心的连接的参数。

| 语言 | MQTT 协议参数 | MQTT over Web Socket 协议参数
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure iot-设备 mqtt。Mqtt | azure iot-设备 mqtt。MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable)。MQTT | IotHubClientProtocol MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet)。Mqtt | 如果 MQTT 失败，Mqtt 将回退到 MQTT over Web Socket。 若要仅通过 Web Socket 指定 MQTT，请使用 TransportType。 Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | 默认情况下支持 MQTT | 在调用中添加 `websockets=True` 来创建客户端 |

以下代码片段演示了如何在使用 Azure IoT node.js SDK 时使用 Web 套接字协议指定 MQTT：

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

以下代码片段演示了如何在使用 Azure IoT Python SDK 时使用 Web 套接字协议指定 MQTT：

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>默认保持活动超时

为了确保客户端/IoT 中心连接保持活动状态，服务和客户端定期发送*保持活动状态*的 ping。 使用 IoT SDK 的客户端按下表中定义的间隔发送 keep-alive：

|语言  |默认 keep-alive 间隔  |可配置性  |
|---------|---------|---------|
|Node.js     |   180秒      |     否    |
|Java     |    230秒     |     否    |
|C     | 240秒 |  [是](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 秒 |  [是](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python （V2）   | 60 秒 |  否   |

遵循[MQTT 规范](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)后，IoT 中心保持活动状态的 ping 间隔是客户端 keep-alive 值的1.5 倍。 但是，IoT 中心将最大服务器端超时限制为29.45 分钟（1767秒），因为所有 Azure 服务都绑定到 Azure 负载均衡器 TCP 空闲超时，这是29.45 分钟。 

例如，使用 Java SDK 的设备发送 "keep-alive" ping，然后失去网络连接。 230秒后，设备会因为处于脱机状态而丢失 keep-alive ping。 但是，IoT 中心不会立即关闭连接，它会等待另一个 `(230 * 1.5) - 230 = 115` 秒，然后将设备断开连接，并出现错误[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)。 

可设置的最大客户端 keep-alive 值为 `1767 / 1.5 = 1177` 秒。 任何流量都将重置保持活动状态。 例如，成功的 SAS 令牌刷新会重置保持活动状态。

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>将设备应用从 AMQP 迁移到 MQTT

如果使用[设备 sdk](https://github.com/Azure/azure-iot-sdks)，则从使用 AMQP 切换到 MQTT 需要在客户端初始化中更改协议参数，如前文所述。

执行此操作时，请确保检查下列各项：

* AMQP 针对许多条件返回错误，而 MQTT 会终止连接。 因此异常处理逻辑可能需要进行一些更改。

* MQTT 在接收[云到设备消息](iot-hub-devguide-messaging.md)时不支持*拒绝*操作。 如果后端应用需要接收来自设备应用的响应，请考虑使用[直接方法](iot-hub-devguide-direct-methods.md)。

* Python SDK 中不支持 AMQP

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>直接使用 MQTT 协议（作为设备）

如果设备无法使用设备 SDK，仍可在 8883 端口使用 MQTT 协议连接到公共设备终结点。 在**CONNECT**数据包中，设备应使用以下值：

* **ClientId** 字段使用 **deviceId**。

* “用户名”字段使用 `{iothubhostname}/{device_id}/?api-version=2018-06-30`，其中 `{iothubhostname}` 是 IoT 中心的完整 CName。

    例如，如果 IoT 中心的名称为 **contoso.azure-devices.net**，设备的名称为 **MyDevice01**，则完整“用户名”字段应包含：

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* “**密码**”字段使用 SAS 令牌。 对于 HTTPS 和 AMQP 协议，SAS 令牌的格式是相同的：

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > 如果使用 X.509 证书身份验证，则不需要使用 SAS 令牌密码。 有关详细信息，请参阅[在 Azure IoT 中心设置 x.509 安全性](iot-hub-security-x509-get-started.md)，并按[以下](#tlsssl-configuration)代码说明进行操作。

  有关如何生成 SAS 令牌的详细信息，请参阅[使用 IoT 中心安全令牌](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)的设备部分。

  测试时，你还可以使用适用于 Visual Studio Code 或[Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)工具的跨平台[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)来快速生成可以复制并粘贴到你自己的代码中的 SAS 令牌：

### <a name="for-azure-iot-tools"></a>适用于 Azure IoT 工具

1. 展开 Visual Studio Code 左下角的“AZURE IOT 中心设备”选项卡。
  
2. 右键单击设备，然后选择“为设备生成 SAS 令牌”。
  
3. 设置“到期时间”，然后按 Enter。
  
4. 将创建 SAS 令牌并将其复制到剪贴板。

### <a name="for-device-explorer"></a>对于 Device Explorer

1. 转到“设备资源管理器”中的“管理”选项卡。

2. 单击“**SAS 令牌**”（右上角）。

3. 在 **SASTokenForm** 上，从 **DeviceID** 下拉列表中选择设备。 设置 **TTL**。

4. 单击“**生成**”创建令牌。

   所生成的 SAS 令牌具有以下结构：

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   此令牌中要用作“密码”字段以便使用 MQTT 进行连接的部分是：

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

对于 MQTT 连接和断开连接数据包，IoT 中心会在**操作监视**通道上发出事件。 此事件包含的其他信息有助于排查连接问题。

设备应用可以在 CONNECT 数据包中指定 Will 消息。 设备应用应该使用 `devices/{device_id}/messages/events/` 或 `devices/{device_id}/messages/events/{property_bag}` 作为 Will 主题名称，用于定义要作为遥测消息转发的 Will 消息。 在此情况下，如果关闭网络连接，但之前未从设备中接收到 DISCONNECT 数据包，则 IoT 中心将 CONNECT 数据包中提供的 Will 消息发送到遥测通道。 遥测通道可以是默认事件终结点或由 IoT 中心路由定义的自定义终结点。 消息具有 iothub-MessageType 属性，其中包含分配给它的 Will 的值。

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>使用 MQTT 的 C 代码的示例，不含 Azure IoT C SDK
在此[存储库](https://github.com/Azure-Samples/IoTMQTTSample)中，你会发现几个 CC++ /demo 项目，其中展示了如何发送遥测消息，并在不使用 Azure IoT C SDK 的情况下接收具有 IoT 中心的事件。 

这些示例使用 Eclipse Mosquitto 库向在 IoT 中心内实现的 MQTT Broker 发送消息。

此存储库包含：

**对于 Windows：**

* TelemetryMQTTWin32：包含将遥测消息发送到 Azure IoT 中心、在 Windows 计算机上生成和运行的代码。

* SubscribeMQTTWin32：包含用于在 Windows 计算机上订阅给定 IoT 中心事件的代码。

* DeviceTwinMQTTWin32：包含用于在 Windows 计算机上查询和订阅 Azure IoT 中心内设备的设备克隆事件的代码。

* PnPMQTTWin32：包含用 IoT 插头 & 播放预览版设备功能将遥测消息发送到 Azure IoT 中心的代码，在 Windows 计算机上生成并运行。 有关 IoT 插件 & 的详细信息，请参阅[此处](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**对于 Linux：**

* MQTTLinux：包含要在 Linux 上运行的代码和生成脚本（目前已测试过 WSL、Ubuntu 和 Raspbian）。

* LinuxConsoleVS2019：包含相同的代码，但在 VS2019 项目中面向 WSL （Windows Linux sub system）。 此项目使你可以从 Visual Studio 逐步调试在 Linux 上运行的代码。

**对于 mosquitto_pub：**

此文件夹包含两个用于 Mosquitto.org 提供的 mosquitto_pub 实用工具工具的示例命令。

* Mosquitto_sendmessage：将简单的文本消息发送到充当设备的 Azure IoT 中心。

* Mosquitto_subscribe：查看 Azure IoT 中心发生的事件。

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>直接使用 MQTT 协议（作为模块）

通过 MQTT 并使用模块标识连接到 IoT 中心的操作与设备类似（如[上面](#using-the-mqtt-protocol-directly-as-a-device)所述），但需要：

* 将客户端 ID 设置为 `{device_id}/{module_id}`。

* 如果使用用户名和密码进行身份验证，请将用户名设置为 `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30`，并使用与模块标识关联的 SAS 令牌作为密码。

* 使用 `devices/{device_id}/modules/{module_id}/messages/events/` 作为主题，用于发布遥测。

* 使用 `devices/{device_id}/modules/{module_id}/messages/events/` 作为 WILL 主题。

* 模块和设备的孪生 GET 和 PATCH 主题是相同的。

* 模块和设备的孪生状态主题是相同的。

## <a name="tlsssl-configuration"></a>TLS/SSL 配置

若要直接使用 MQTT 协议，客户端*必须*通过 TLS/SSL 连接。 尝试跳过此步骤失败并显示连接错误。

若要建立 TLS 连接，可能需要下载并引用 DigiCert Baltimore 根证书。 此证书是 Azure 用来保护连接安全的。 可在[Azure iot-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c)存储库中找到此证书。 可在[Digicert 的网站](https://www.digicert.com/digicert-root-certificates.htm)上找到有关这些证书的详细信息。

下面是一个示例，演示如何通过 Eclipse Foundation 使用[PAHO MQTT 库](https://pypi.python.org/pypi/paho-mqtt)的 Python 版本实现此操作。

首先，从命令行环境安装 Paho 库：

```cmd/sh
pip install paho-mqtt
```

然后，在 Python 脚本中实现客户端。 替换这些占位符，如下所示：

* `<local path to digicert.cer>` 是包含 DigiCert Baltimore Root 证书的本地文件的路径。 创建此文件时，可以在用于 C 的 Azure IoT SDK 中复制 [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) 中的证书信息。包括 `-----BEGIN CERTIFICATE-----` 行和 `-----END CERTIFICATE-----` 行，删除每行开头和结尾的 `"` 标记，并删除每行结尾的 `\r\n` 字符。

* `<device id from device registry>` 是添加到 IoT 中心的设备的 ID。

* `<generated SAS token>` 是已创建设备的 SAS 令牌，如本文前面所述。

* `<iot hub name>`：IoT 中心的名称。

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

若要使用设备证书进行身份验证，请使用以下更改更新上面的代码片段（请参阅如何获取有关如何为基于证书的身份验证做好准备的[X.509 CA 证书](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate)）：

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>发送“设备到云”消息

成功建立连接后，设备可以使用 `devices/{device_id}/messages/events/` 或 `devices/{device_id}/messages/events/{property_bag}` 作为**主题名称**将消息发送到 IoT 中心。 `{property_bag}` 元素可让设备使用 URL 编码格式发送包含其他属性的消息。 例如：

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> 此 `{property_bag}` 元素使用与 HTTPS 协议中的查询字符串相同的编码。

下面列出了 IoT 中心特定于实现的行为：

* IoT 中心不支持 QoS 2 消息。 如果设备应用使用 **QoS 2** 发布消息，IoT 中心将断开网络连接。

* IoT 中心不会保存 Retain 消息。 如果设备在 **RETAIN** 标志设置为 1 的情况下发送消息，则 IoT 中心会在消息中添加 **x-opt-retain** 应用程序属性。 在此情况下，IoT 中心不会存储保留消息，而将其传递到后端应用。

* IoT 中心仅支持每个设备一个活动 MQTT 连接。 代表相同设备 ID 的任何新 MQTT 连接都会导致 IoT 中心删除现有连接。

有关详细信息，请参阅[消息传送开发人员指南](iot-hub-devguide-messaging.md)。

## <a name="receiving-cloud-to-device-messages"></a>接收“云到设备”消息

若要从 IoT 中心接收消息，设备应使用 `devices/{device_id}/messages/devicebound/#` 作为**主题筛选器**来进行订阅。 主题筛选器中的多级通配符 `#` 仅用于允许设备接收主题名称中的其他属性。 IoT 中心不允许使用 `#` 或 `?` 通配符筛选子主题。 由于 IoT 中心不是常规用途的发布-订阅消息传送中转站，因此它仅支持存档的主题名称和主题筛选器。

在设备成功订阅 `devices/{device_id}/messages/devicebound/#` 主题筛选器表示的设备特定终结点前，不会从 IoT 中心收到任何消息。 建立订阅后，设备会接收建立订阅后发送给它的云到设备消息。 如果设备在 **CleanSession** 标志设置为 0 的情况下进行连接，则订阅在经历不同的会话后仍然持久存在。 在此情况下，下次使用 CleanSession 0 进行连接时，设备会收到断开连接时发送给它的未处理消息。 但是，如果设备使用设置为 1 的 CleanSession 标志，在订阅其设备终结点前，它不会从 IoT 中心收到任何消息。

当存在消息属性时，IoT 中心会将具有**主题名称**的消息传递 `devices/{device_id}/messages/devicebound/`或 `devices/{device_id}/messages/devicebound/{property_bag}`。 `{property_bag}` 包含 URL 编码的消息属性键/值对。 属性包中只包含应用程序属性和用户可设置的系统属性（例如 **messageId** 或 **correlationId**）。 系统属性名称具有前缀 **$** ，但应用程序属性使用没有前缀的原始属性名称。

当设备应用使用 **QoS 2** 订阅主题时，IoT 中心会在 **SUBACK** 包中授予最高 QoS 级别 1。 之后，IoT 中心会使用 QoS 1 将消息传送到设备。

## <a name="retrieving-a-device-twins-properties"></a>检索设备克隆的属性

首先，设备订阅 `$iothub/twin/res/#`，接收操作的响应。 然后，它向主题 `$iothub/twin/GET/?$rid={request id}` 发送一条空消息，其中包含**请求 ID** 的填充值。 服务随后会发送一条响应消息，其中包含关于主题 `$iothub/twin/res/{status}/?$rid={request id}` 的设备孪生数据，并且使用与请求相同的**请求 ID**。

请求 ID 可以是消息属性值的任何有效值，如每个[IoT 中心消息传送开发人员指南](iot-hub-devguide-messaging.md)一样，状态将验证为一个整数。

响应正文包含设备孪生的 properties 节，如以下响应示例所示：

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

可能的状态代码为：

|状态 | 说明 |
| ----- | ----------- |
| 200 | Success |
| 429 | 请求过多（受限），因为每个[IoT 中心限制](iot-hub-devguide-quotas-throttling.md) |
| 5** | 服务器错误 |

有关详细信息，请参阅[设备孪生开发人员指南](iot-hub-devguide-device-twins.md)。

## <a name="update-device-twins-reported-properties"></a>更新设备孪生的报告属性

若要更新已报告的属性，设备通过指定的 MQTT 主题上的发布向 IoT 中心发出请求。 处理完请求后，IoT 中心会通过发布到其他主题来响应更新操作的成功或失败状态。 设备可以订阅此主题，以便通知它有关其孪生更新请求的结果。 若要在 MQTT 中实现这种类型的请求/响应交互，我们将利用设备最初在其更新请求中提供的请求 ID （`$rid`）的概念。 此请求 ID 还包括在来自 IoT 中心的响应中，以允许设备将响应与其特定的先前请求关联起来。

以下序列描述了设备如何在 IoT 中心中更新设备孪生中报告的属性：

1. 设备必须首先订阅 `$iothub/twin/res/#` 主题才能从 IoT 中心接收操作的响应。

2. 设备将包含设备孪生更新的消息发送到 `$iothub/twin/PATCH/properties/reported/?$rid={request id}` 主题。 此消息包含**请求 ID** 值。

3. 然后，服务发送一个响应消息，其中包含 `$iothub/twin/res/{status}/?$rid={request id}` 主题上报告的属性集合的新 ETag 值。 此响应消息使用与请求相同的**请求 ID**。

请求消息正文包含 JSON 文档，该文档包含已报告属性的新值。 JSON 文档中的每个成员都会在设备克隆文档中更新或添加相应成员。 如果某个成员设置为 `null`，则会从包含方对象中删除该成员。 例如：

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

可能的状态代码为：

|状态 | 说明 |
| ----- | ----------- |
| 204 | 成功（不返回任何内容） |
| 400 | 错误的请求。 格式不正确的 JSON |
| 429 | 请求过多（受限），因为每个[IoT 中心限制](iot-hub-devguide-quotas-throttling.md) |
| 5** | 服务器错误 |

下面的 python 代码片段演示了 MQTT 上孪生已报告属性更新过程（使用 Paho MQTT 客户端）：

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

上述孪生已报告属性更新操作成功后，来自 IoT 中心的发布消息将具有以下主题：`$iothub/twin/res/204/?$rid=1&$version=6`，其中 `204` 是表示成功的状态代码，`$rid=1` 对应于代码中设备提供的请求 ID，`$version` 对应于更新后设备孪生已报告属性部分的版本。

有关详细信息，请参阅[设备孪生开发人员指南](iot-hub-devguide-device-twins.md)。

## <a name="receiving-desired-properties-update-notifications"></a>接收所需属性更新通知

设备连接时，IoT 中心会向主题 `$iothub/twin/PATCH/properties/desired/?$version={new version}` 发送通知，内附解决方案后端执行的更新内容。 例如：

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

对于属性更新，`null` 值表示正在删除 JSON 对象成员。 另请注意，`$version` 指示孪生的所需属性部分的新版本。

> [!IMPORTANT]
> IoT 中心仅在连接设备时才会生成更改通知。 请确保实现设备重新[连接流](iot-hub-devguide-device-twins.md#device-reconnection-flow)，使 IoT 中心和设备应用之间的所需属性保持同步。

有关详细信息，请参阅[设备孪生开发人员指南](iot-hub-devguide-device-twins.md)。

## <a name="respond-to-a-direct-method"></a>响应直接方法

首先，设备需要订阅 `$iothub/methods/POST/#`。 IoT 中心向主题 `$iothub/methods/POST/{method name}/?$rid={request id}` 发送方法请求，其中包含有效的 JSON 或空正文。

进行响应时，设备向主题 `$iothub/methods/res/{status}/?$rid={request id}` 发送带有有效 JSON 或空正文的消息。 在此消息中，**request ID** 必须与请求消息中的相符，**status** 必须为整数。

有关详细信息，请参阅[直接方法开发人员指南](iot-hub-devguide-direct-methods.md)。

## <a name="additional-considerations"></a>其他注意事项

最后需要注意的是，如果需要在云端自定义 MQTT 协议行为，则应查看[Azure IoT 协议网关](iot-hub-protocol-gateway.md)。 可以通过本软件部署高性能自定义协议网关，该网关直接与 IoT 中心接合。 Azure IoT 协议网关可让你自定义设备协议，以适应要重建的 MQTT 部署或其他自定义协议。 但是，这种方法要求运行并使用自定义协议网关。

## <a name="next-steps"></a>后续步骤

若要了解有关 MQTT 协议的详细信息，请参阅[MQTT 文档](https://mqtt.org/documentation)。

若要深入了解如何规划 IoT 中心部署，请参阅：

* [Azure IoT 已认证设备目录](https://catalog.azureiotsolutions.com/)
* [支持其他协议](iot-hub-protocol-gateway.md)
* [与事件中心比较](iot-hub-compare-event-hubs.md)
* [缩放、HA 和 DR](iot-hub-scaling.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)
