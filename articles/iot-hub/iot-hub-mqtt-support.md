---
title: 了解 Azure IoT 中心 MQTT 支持 | Microsoft Docs
description: 开发人员指南 - 支持设备使用 MQTT 协议连接到面向设备的 IoT 中心终结点。 介绍了 Azure IoT 设备 SDK 中的内置 MQTT 支持。
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9acda980583319414cc9e8668424907947a257db
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2018
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>使用 MQTT 协议与 IoT 中心通信

IoT 中心允许设备通过以下方式与 IoT 中心设备终结点通信：

* 在端口 8883 上使用 [MQTT v3.1.1][lnk-mqtt-org]
* 在端口 443 上使用基于 WebSocket 的 MQTT v3.1.1。

所有通过 IoT 中心进行的设备通信都必须使用 TLS/SSL 来保护。 因此，IoT 中心不支持通过端口 1883 进行的不安全的连接。

## <a name="connecting-to-iot-hub"></a>连接到 IoT 中心

设备可以通过以下方式使用 MQTT 协议连接到 IoT 中心：

* 通过 [Azure IoT SDK][lnk-device-sdks] 中的库。
* 或者直接通过 MQTT 协议。

## <a name="using-the-device-sdks"></a>使用设备 SDK

支持 MQTT 协议的[设备 SDK][lnk-device-sdks] 可用于 Java、Node.js、C、C# 和 Python。 设备 SDK 使用标准 IoT 中心连接字符串来连接到 IoT 中心。 要使用 MQTT 协议，必须将客户端协议参数设置为 **MQTT**。 默认情况下，设备 SDK 在 **CleanSession** 标志设置为 **0** 的情况下连接到 IoT 中心，并使用 **QoS 1** 来与 IoT 中心交换消息。

当设备连接到 IoT 中心时，设备 SDK 会提供方法，让设备与 IoT 中心交换消息。

下表包含了每种受支持语言的代码示例链接，并指定了以 MQTT 协议连接到 IoT 中心时要使用的参数。

| 语言 | 协议参数 |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>将设备应用从 AMQP 迁移到 MQTT

如果使用[设备 SDK][lnk-device-sdks]，则从使用 AMQP 切换到 MQTT 需要在客户端初始化中更改协议参数，如前面所述。

执行此操作时，请确保检查下列各项：

* AMQP 针对许多条件返回错误，而 MQTT 会终止连接。 因此异常处理逻辑可能需要进行一些更改。
* MQTT 在接收[“云到设备”消息][lnk-messaging]时不支持*拒绝*操作。 如果后端应用需要接收来自设备应用的响应，请考虑使用[直接方法][lnk-methods]。

## <a name="using-the-mqtt-protocol-directly"></a>直接使用 MQTT 协议

如果设备无法使用设备 SDK，仍可在 8883 端口使用 MQTT 协议连接到公共设备终结点。 在 **CONNECT** 数据包中，设备应使用以下值：

* **ClientId** 字段使用 **deviceId**。

* “用户名”字段使用 `{iothubhostname}/{device_id}/api-version=2016-11-14`，其中 `{iothubhostname}` 是 IoT 中心的完整 CName。

    例如，如果 IoT 中心的名称为 **contoso.azure-devices.net**，设备的名称为 **MyDevice01**，则完整“用户名”字段应包含：

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* “**密码**”字段使用 SAS 令牌。 对于 HTTPS 和 AMQP 协议，SAS 令牌的格式是相同的：

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > 如果使用 X.509 证书身份验证，则不需要使用 SAS 令牌密码。 有关详细信息，请参阅[在 Azure IoT 中心设置 X.509 安全性][lnk-x509]

  有关如何生成 SAS 令牌的详细信息，请参阅[使用 IoT 中心安全令牌][lnk-sas-tokens]的设备部分。

  测试时也可以使用[设备资源管理器][lnk-device-explorer]工具来快速生成可以复制并粘贴到自己的代码中的 SAS 令牌：

  1. 转到“设备资源管理器”中的“管理”选项卡。
  2. 单击“**SAS 令牌**”（右上角）。
  3. 在 **SASTokenForm** 上，从 **DeviceID** 下拉列表中选择设备。 设置 **TTL**。
  4. 单击“**生成**”创建令牌。

     所生成的 SAS 令牌具有以下结构：

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     此令牌中要用作“密码”字段以便使用 MQTT 进行连接的部分是：

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

对于 MQTT 连接和断开连接数据包，IoT 中心会在**操作监视**通道上发出事件。 此事件包含的其他信息有助于排查连接问题。

设备应用可以在 CONNECT 数据包中指定 Will 消息。 设备应用应该使用 `devices/{device_id}/messages/events/{property_bag}` 或 `devices/{device_id}/messages/events/{property_bag}` 作为 Will 主题名称，用于定义要作为遥测消息转发的 Will 消息。 在此情况下，如果关闭网络连接，但之前未从设备中接收到 DISCONNECT 数据包，则 IoT 中心将 CONNECT 数据包中提供的 Will 消息发送到遥测通道。 遥测通道可以是默认事件终结点或由 IoT 中心路由定义的自定义终结点。 消息具有 iothub-MessageType 属性，其中包含分配给它的 Will 的值。

### <a name="tlsssl-configuration"></a>TLS/SSL 配置

若要直接使用 MQTT 协议，客户端*必须*通过 TLS/SSL 连接。 尝试跳过此步骤失败并显示连接错误。

若要建立 TLS 连接，可能需要下载并引用 DigiCert Baltimore 根证书。 此证书是 Azure 用来保护连接安全的。 可以在 [Azure-iot-sdk-c][lnk-sdk-c-certs] 存储库中找到此证书。 可以在 [Digicert 网站][lnk-digicert-root-certs]上找到有关这些证书的详细信息。

一个说明如何使用 Eclipse Foundation 提供的 Python 版本 [Paho MQTT 库][lnk-paho]实现此功能的示例可能如下所示。

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

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>发送“设备到云”消息

成功建立连接后，设备可以使用 `devices/{device_id}/messages/events/` 或 `devices/{device_id}/messages/events/{property_bag}` 作为**主题名称**将消息发送到 IoT 中心。 `{property_bag}` 元素可让设备使用 URL 编码格式发送包含其他属性的消息。 例如：

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> 此 `{property_bag}` 元素使用的编码与 HTTPS 协议中用于查询字符串的编码相同。

下面列出了 IoT 中心特定于实现的行为：

* IoT 中心不支持 QoS 2 消息。 如果设备应用使用 **QoS 2** 发布消息，IoT 中心将断开网络连接。
* IoT 中心不会保存 Retain 消息。 如果设备在 **RETAIN** 标志设置为 1 的情况下发送消息，则 IoT 中心会在消息中添加 **x-opt-retain** 应用程序属性。 在此情况下，IoT 中心不会存储保留消息，而将其传递到后端应用。
* IoT 中心仅支持每个设备一个活动 MQTT 连接。 代表相同设备 ID 的任何新 MQTT 连接都会导致 IoT 中心删除现有连接。

有关详细信息，请参阅[消息传送开发人员指南][lnk-messaging]。

### <a name="receiving-cloud-to-device-messages"></a>接收“云到设备”消息

若要从 IoT 中心接收消息，设备应使用 `devices/{device_id}/messages/devicebound/#` 作为**主题筛选器**来进行订阅。 主题筛选器中的多级通配符 `#` 仅用于允许设备接收主题名称中的其他属性。 IoT 中心不允许使用 `#` 或 `?` 通配符筛选子主题。 由于 IoT 中心不是常规用途的发布-订阅消息传送中转站，因此它仅支持存档的主题名称和主题筛选器。

在设备成功订阅 `devices/{device_id}/messages/devicebound/#` 主题筛选器表示的设备特定终结点前，不会从 IoT 中心收到任何消息。 建立订阅后，设备会接收建立订阅后发送给它的云到设备消息。 如果设备在 **CleanSession** 标志设置为 0 的情况下进行连接，则订阅在经历不同的会话后仍然持久存在。 在此情况下，下次使用 CleanSession 0 进行连接时，设备会收到断开连接时发送给它的未处理消息。 但是，如果设备使用设置为 1 的 CleanSession 标志，在订阅其设备终结点前，它不会从 IoT 中心收到任何消息。

如有消息属性，IoT 中心会传送包含**主题名称** `devices/{device_id}/messages/devicebound/` 或 `devices/{device_id}/messages/devicebound/{property_bag}` 的消息。 `{property_bag}` 包含 URL 编码的消息属性键/值对。 属性包中只包含应用程序属性和用户可设置的系统属性（例如 **messageId** 或 **correlationId**）。 系统属性名称具有前缀 **$**，但应用程序属性使用没有前缀的原始属性名称。

当设备应用使用 **QoS 2** 订阅主题时，IoT 中心会在 **SUBACK** 包中授予最高 QoS 级别 1。 之后，IoT 中心会使用 QoS 1 将消息传送到设备。

### <a name="retrieving-a-device-twins-properties"></a>检索设备克隆的属性

首先，设备订阅 `$iothub/twin/res/#`，接收操作的响应。 然后，它向主题 `$iothub/twin/GET/?$rid={request id}` 发送一条空消息，其中包含**请求 ID** 的填充值。 服务随后会发送一条响应消息，其中包含关于主题 `$iothub/twin/res/{status}/?$rid={request id}` 的设备孪生数据，并且使用与请求相同的**请求 ID**。

请求 ID 可以是消息属性值的任何有效值（如 [IoT 中心消息传送开发人员指南][lnk-messaging]中所述），且需要验证确保状态是整数。

响应正文包含设备孪生的 properties 节。 以下代码片段表明，标识注册表项的正文限制为“properties”成员，例如：

```json
{
    "properties": {
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
}
```

可能的状态代码为：

|状态 | 说明 |
| ----- | ----------- |
| 200 | 成功 |
| 429 | 请求过多（受限），如 [IoT 中心限制][lnk-quotas]中所述 |
| 5** | 服务器错误 |

有关详细信息，请参阅[设备孪生开发人员指南][lnk-devguide-twin]。

### <a name="update-device-twins-reported-properties"></a>更新设备孪生的报告属性

以下序列描述了设备如何在 IoT 中心中更新设备孪生中报告的属性：

1. 设备必须首先订阅 `$iothub/twin/res/#` 主题才能从 IoT 中心接收操作的响应。

1. 设备将包含设备孪生更新的消息发送到 `$iothub/twin/PATCH/properties/reported/?$rid={request id}` 主题。 此消息包含**请求 ID** 值。

1. 然后，服务发送一个响应消息，其中包含 `$iothub/twin/res/{status}/?$rid={request id}` 主题上报告的属性集合的新 ETag 值。 此响应消息使用与请求相同的**请求 ID**。

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
| 200 | 成功 |
| 400 | 错误的请求。 格式不正确的 JSON |
| 429 | 请求过多（受限），如 [IoT 中心限制][lnk-quotas]中所述 |
| 5** | 服务器错误 |

有关详细信息，请参阅[设备孪生开发人员指南][lnk-devguide-twin]。

### <a name="receiving-desired-properties-update-notifications"></a>接收所需属性更新通知

设备连接时，IoT 中心会向主题 `$iothub/twin/PATCH/properties/desired/?$version={new version}` 发送通知，内附解决方案后端执行的更新内容。 例如：

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

对于属性更新，`null` 值表示正在删除 JSON 对象成员。

> [!IMPORTANT]
> IoT 中心仅在连接设备时才会生成更改通知。 请确保实现[设备重新连接流][lnk-devguide-twin-reconnection]，让 IoT 中心和设备应用之间的所需属性保持同步。

有关详细信息，请参阅[设备孪生开发人员指南][lnk-devguide-twin]。

### <a name="respond-to-a-direct-method"></a>响应直接方法

首先，设备需要订阅 `$iothub/methods/POST/#`。 IoT 中心向主题 `$iothub/methods/POST/{method name}/?$rid={request id}` 发送方法请求，其中包含有效的 JSON 或空正文。

进行响应时，设备向主题 `$iothub/methods/res/{status}/?$rid={request id}` 发送带有有效 JSON 或空正文的消息。 在此消息中，**request ID** 必须与请求消息中的相符，**status** 必须为整数。

有关详细信息，请参阅[直接方法开发人员指南][lnk-methods]。

### <a name="additional-considerations"></a>其他注意事项

最后要考虑的是，若需在客户端自定义 MQTT 协议行为，应参阅 [Azure IoT 协议网关][lnk-azure-protocol-gateway]。 可以通过本软件部署高性能自定义协议网关，该网关直接与 IoT 中心接合。 Azure IoT 协议网关可让你自定义设备协议，以适应要重建的 MQTT 部署或其他自定义协议。 但是，这种方法要求运行并使用自定义协议网关。

## <a name="next-steps"></a>后续步骤

若要了解有关 MQTT 协议的详细信息，请参阅 [MQTT 文档][lnk-mqtt-docs]。

若要深入了解如何规划 IoT 中心部署，请参阅：

* [Azure IoT 认证设备目录][lnk-devices]
* [支持其他协议][lnk-protocols]
* [与事件中心比较][lnk-compare]
* [缩放、高可用性和灾难恢复][lnk-scaling]

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南][lnk-devguide]
* [使用 Azure IoT Edge 将 AI 部署到边缘设备][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
