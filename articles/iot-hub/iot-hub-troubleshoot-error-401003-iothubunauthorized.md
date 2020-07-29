---
title: 排查 Azure IoT 中心错误 401003 IoTHubUnauthorized
description: 了解如何修复错误 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759592"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

本文介绍了 **401003 IoTHubUnauthorized** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

### <a name="symptom-1"></a>症状 1

在诊断日志中，你会看到这样一种情况：设备断开连接并出现 **401003 IoTHubUnauthorized** 错误，接着出现 **404104 DeviceConnectionClosedRemotely** 错误，然后在不久之后成功连接。

### <a name="symptom-2"></a>症状 2

对 IoT 中心的请求失败，并出现以下错误消息之一：

* 缺少 Authorization 标头
* IotHub '\*' 未包含指定的设备 '\*'
* 授权规则 '\*' 不允许访问 '\*'
* 此设备的身份验证失败，请续订令牌或证书，然后重新连接
* 指纹与配置不匹配：指纹：SHA1Hash=\*, SHA2Hash=\*；配置：PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

对于 MQTT，某些 SDK 依赖于 IoT 中心在 SAS 令牌过期时发出断开连接的指令，以便知道何时刷新它。 因此， 

1. SAS 令牌过期
1. IoT 中心会注意到过期，将设备断开连接并显示 **401003 IoTHubUnauthorized**
1. 设备完成断开连接并显示 **404104 DeviceConnectionClosedRemotely**
1. IoT SDK 生成一个新的 SAS 令牌
1. 设备成功重新连接到 IoT 中心

### <a name="cause-2"></a>原因 2

IoT 中心无法对 auth 标头、规则或密钥进行身份验证。

## <a name="solution"></a>解决方案

### <a name="solution-1"></a>解决方案 1

如果使用 IoT SDK 通过设备连接字符串进行连接，则不需要执行任何操作。 IoT SDK 会重新生成新令牌，以在 SAS 令牌过期时重新连接。 

如果担心错误数量太多，请切换到 C SDK，这会在过期之前续订 SAS 令牌。 此外，对于 AMQP，SAS 令牌可以在不断开连接的情况下进行刷新。

### <a name="solution-2"></a>解决方案 2

通常情况下，出现的错误消息应说明如何修复此错误。 如果由于某种原因无法访问错误消息详细信息，请确保：

- 所用的 SAS 或其他安全令牌未过期。 
- 授权凭据的格式正确，适用于所使用的协议。 若要了解详细信息，请参阅 [IoT 中心访问控制](iot-hub-devguide-security.md)。
- 使用的授权规则对所请求的操作具有权限。

## <a name="next-steps"></a>后续步骤

为了更轻松地向 IoT 中心进行身份验证，我们建议使用 [Azure IoT SDK](iot-hub-devguide-sdks.md)。