---
title: 了解 Azure IoT 设备预配服务 MQTT 支持 |Microsoft Docs
description: 开发人员指南-支持设备使用 MQTT 协议连接到 Azure IoT 设备预配服务面向设备的终结点。
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: fb39979e321c4815aec74c801cd2d04e81b7e533
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093899"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>使用 MQTT 协议与你的 DPS 通信

DPS 使设备能够使用以下内容与 DPS 设备终结点进行通信：

* 在端口 8883 上使用 [MQTT v3.1.1](https://mqtt.org/)
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) over 端口443上的 WebSocket。

DPS 不是功能完备的 MQTT broker，不支持 MQTT v 3.1.1 标准中指定的所有行为。 本文介绍如何使用支持的 MQTT 行为与 DPS 通信。

使用 TLS/SSL 时，必须确保与 DPS 的所有设备通信安全。 因此，DPS 不支持端口1883上的非安全连接。

 > [!NOTE] 
 > DPS 目前不支持通过 MQTT 协议使用 TPM[证明机制](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism)的设备。

## <a name="connecting-to-dps"></a>连接到 DPS

设备可以使用 MQTT 协议通过以下任意选项连接到 DPS。

* [Azure IoT 预配 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks)中的库。
* 直接通过 MQTT 协议。

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>直接使用 MQTT 协议（作为设备）

如果设备无法使用设备 SDK，仍可在 8883 端口使用 MQTT 协议连接到公共设备终结点。 在**CONNECT**数据包中，设备应使用以下值：

* 对于**ClientId**字段，请使用**registrationId**。

* 对于 "**用户名**" 字段，请使用 `{idScope}/registrations/{registration_id}/api-version=2019-03-31`，其中 `{idScope}` 是 DPS 的[idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) 。

* “**密码**”字段使用 SAS 令牌。 对于 HTTPS 和 AMQP 协议，SAS 令牌的格式是相同的：

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` resourceURI 应采用 `{idScope}/registrations/{registration_id}`格式。 策略名称应 `registration`。

  > [!NOTE]
  > 如果使用 X.509 证书身份验证，则不需要使用 SAS 令牌密码。

  有关如何生成 SAS 令牌的详细信息，请参阅[控制对 DPS 的访问](how-to-control-access.md#security-tokens)的安全令牌部分。

下面列出了特定于 DPS 实现的行为：

 * DPS 不支持设置为**0**的**CleanSession**标志功能。

 * 当设备应用使用**QoS 2**订阅主题时，DPS 在**SUBACK**数据包中授予最高 QoS 级别1。 然后，DPS 使用 QoS 1 将消息传送到设备。

## <a name="tlsssl-configuration"></a>TLS/SSL 配置

若要直接使用 MQTT 协议，客户端*必须*通过 TLS 1.2 连接。 尝试跳过此步骤失败并显示连接错误。


## <a name="registering-a-device"></a>注册设备

若要通过 DPS 注册设备，设备应使用 `$dps/registrations/res/#` 作为**主题筛选器**来进行订阅。 主题筛选器中的多级通配符 `#` 仅用于允许设备接收主题名称中的其他属性。 DPS 不允许使用 `#` 或 `?` 通配符来筛选副标题。 由于 DPS 并非常规用途的发布-子消息传递代理，因此它仅支持记录的主题名称和主题筛选器。

设备应使用 `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` 作为**主题名称**向 DPS 发布注册消息。 负载应包含 JSON 格式的[设备注册](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration)对象。
在成功的情况下，设备将在 `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` 主题名称上收到响应，其中 x 是以秒为单位的重试值。 响应的有效负载将包含 JSON 格式的[RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus)对象。

## <a name="polling-for-registration-operation-status"></a>轮询注册操作状态

设备必须定期轮询服务以接收设备注册操作的结果。 假定设备已订阅上述 `$dps/registrations/res/#` 主题，则可以将 get operationstatus 消息发布到 `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` 主题名称。 此消息中的操作 ID 应该是在上一步中 RegistrationOperationStatus 响应消息中接收的值。 在成功的情况下，该服务将在 `$dps/registrations/res/200/?$rid={request_id}` 主题上做出响应。 响应的有效负载将包含 RegistrationOperationStatus 对象。 如果在延迟时间等于重试后的延迟时间为202，则设备应始终轮询服务。 如果服务返回200状态代码，则设备注册操作成功。

## <a name="connecting-over-websocket"></a>通过 Websocket 连接
通过 Websocket 进行连接时，将子协议指定为 `mqtt`。 遵循[RFC 6455](https://tools.ietf.org/html/rfc6455)。

## <a name="next-steps"></a>后续步骤

若要了解有关 MQTT 协议的详细信息，请参阅 [MQTT 文档](https://mqtt.org/documentation)。

若要进一步探索 DPS 的功能，请参阅：

* [关于 IoT DPS](about-iot-dps.md)
