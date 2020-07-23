---
title: 了解 Azure IoT 设备预配服务 MQTT 支持 | Microsoft Docs
description: 开发人员指南 - 为使用 MQTT 协议连接到 Azure IoT 设备预配服务 (DPS) 面向设备的终结点的设备提供支持。
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 213fc3412a2dfad77946e52a355a30774d6860c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680678"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>使用 MQTT 协议与 DPS 通信

DPS 可让设备使用以下协议来与 DPS 设备终结点通信：

* 在端口 8883 上使用 [MQTT v3.1.1](https://mqtt.org/)
* 在端口 443 上使用基于 WebSocket 的 [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127)。

DPS 不是功能完备的 MQTT 中转站，并未支持 MQTT v3.1.1 标准中指定的所有行为。 本文介绍设备如何使用受支持的 MQTT 行为通过 DPS 进行通信。

所有通过 DPS 进行的设备通信都必须使用 TLS/SSL 来保护。 因此，DPS 不支持通过端口 1883 进行的不安全的连接。

 > [!NOTE] 
 > DPS 目前不支持通过 MQTT 协议使用 TPM [证明机制](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism)的设备。

## <a name="connecting-to-dps"></a>连接到 DPS

设备可以通过以下任意选项使用 MQTT 协议连接到 DPS。

* [Azure IoT 预配 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks) 中的库。
* 直接通过 MQTT 协议。

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>直接使用 MQTT 协议（作为设备）

如果设备无法使用设备 SDK，仍可使用端口 8883 上的 MQTT 协议连接到公共设备终结点。 在 **CONNECT** 数据包中，设备应使用以下值：

* 对于 **ClientId** 字段，使用 **registrationId**。

* 对于 **Username** 字段，使用 `{idScope}/registrations/{registration_id}/api-version=2019-03-31`，其中 `{idScope}` 是 DPS 的 [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)。

* “密码”  字段使用 SAS 令牌。 对于 HTTPS 和 AMQP 协议，SAS 令牌的格式是相同的：

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` resourceURI 应采用 `{idScope}/registrations/{registration_id}` 格式。 策略名称应是 `registration`。

  > [!NOTE]
  > 如果使用 X.509 证书身份验证，则不需要使用 SAS 令牌密码。

  有关如何生成 SAS 令牌的详细信息，请参阅[控制对 DPS 的访问](how-to-control-access.md#security-tokens)的安全令牌部分。

下面列出了 DPS 特定于实现的行为：

 * DPS 不支持将 **CleanSession** 标志设置为 **0** 的功能。

 * 当设备应用使用 **QoS 2** 订阅主题时，DPS 会在 **SUBACK** 包中授予最高 QoS 级别 1。 之后，DPS 会使用 QoS 1 将消息传送到设备。

## <a name="tlsssl-configuration"></a>TLS/SSL 配置

若要直接使用 MQTT 协议，客户端必须通过 TLS 1.2 进行连接。 尝试跳过此步骤失败并显示连接错误。


## <a name="registering-a-device"></a>注册设备

若要通过 DPS 注册某个设备，该设备应使用 `$dps/registrations/res/#` 作为**主题筛选器**进行订阅。 主题筛选器中的多级通配符 `#` 仅用于允许设备接收主题名称中的其他属性。 DPS 不允许使用 `#` 或 `?` 通配符筛选子主题。 由于 DPS 不是一般用途的发布-订阅消息传送中转站，因此它仅支持存档的主题名称和主题筛选器。

设备应使用 `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` 作为**主题名称**向 DPS 发布 register 消息。 有效负载应包含 JSON 格式的[设备注册](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration)对象。
如果成功，设备将在 `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` 主题名称中收到响应，其中，x 是以秒为单位的 retry-after 值。 响应的有效负载将包含 JSON 格式的 [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) 对象。

## <a name="polling-for-registration-operation-status"></a>轮询注册操作状态

设备必须定期轮询服务，以接收设备注册操作的结果。 假设设备已如前所述订阅 `$dps/registrations/res/#` 主题，则它可以将 get operationstatus 消息发布到 `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` 主题名称。 此消息中的操作 ID 应是在上一步骤中的 RegistrationOperationStatus 响应消息中收到的值。 如果成功，服务将在 `$dps/registrations/res/200/?$rid={request_id}` 主题中做出响应。 响应的有效负载将包含 RegistrationOperationStatus 对象。 如果在经过与 retry-after 期限相等的延迟时间后响应代码为 202，则设备应不断地轮询服务。 如果服务返回 200 状态代码，则表示设备注册操作成功。

## <a name="connecting-over-websocket"></a>通过 Websocket 进行连接
通过 Websocket 进行连接时，请将子协议指定为 `mqtt`。 遵循 [RFC 6455](https://tools.ietf.org/html/rfc6455)。

## <a name="next-steps"></a>后续步骤

若要了解有关 MQTT 协议的详细信息，请参阅 [MQTT 文档](https://mqtt.org/documentation)。

若要进一步探索 DPS 的功能，请参阅：

* [关于 IoT DPS](about-iot-dps.md)
