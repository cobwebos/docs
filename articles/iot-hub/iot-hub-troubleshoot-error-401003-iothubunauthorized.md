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
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960628"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

本文介绍**401003 IoTHubUnauthorized**错误的原因和解决方案。

## <a name="symptoms"></a>症状

### <a name="symptom-1"></a>症状1

在诊断日志中，你将看到设备的模式与**401003 IoTHubUnauthorized**断开连接，后跟**404104 DeviceConnectionClosedRemotely**，然后很快就会成功连接。

### <a name="symptom-2"></a>症状2

对 IoT 中心的请求失败，并出现以下错误消息之一：

* 缺少授权标头
* IotHub "\*" 不包含指定的设备 "\*"
* 授权规则 "\*" 不允许访问 "\*"
* 此设备的身份验证失败，请续订令牌或证书，然后重新连接
* 指纹与配置不匹配：指纹： SHA1Hash =\*，SHA2Hash =\*;配置： PrimaryThumbprint =\*，SecondaryThumbprint =\*

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

对于 MQTT，某些 Sdk 依赖 IoT 中心在 SAS 令牌过期时发出断开连接，以了解何时刷新它。 否则 

1. SAS 令牌过期
1. IoT 中心会注意到过期时间，并将设备与**401003 IoTHubUnauthorized**断开连接
1. 设备完成了与**404104 DeviceConnectionClosedRemotely**的断开连接
1. IoT SDK 生成新的 SAS 令牌
1. 设备成功重新连接到 IoT 中心

### <a name="cause-2"></a>原因 2

IoT 中心无法对身份验证标头、规则或密钥进行身份验证。

## <a name="solution"></a>解决方案

### <a name="solution-1"></a>解决方案 1

如果对使用设备连接字符串的 IoT SDK 进行连接，则无需执行任何操作。 IoT SDK 重新生成新令牌，以在 SAS 令牌过期时重新连接。 

如果需要考虑错误量，请切换到 C SDK，这会在过期之前续订 SAS 令牌。 此外，对于 AMQP，SAS 令牌可以在不断开连接的情况下进行刷新。

### <a name="solution-2"></a>解决方案 2

通常情况下，出现的错误消息应说明如何修复此错误。 如果由于某种原因无法访问错误消息详细信息，请确保：

- 你使用的 SAS 或其他安全令牌不会过期。 
- 授权凭据的格式正确，适用于所使用的协议。 若要了解详细信息，请参阅[IoT 中心访问控制](iot-hub-devguide-security.md)。
- 使用的授权规则具有请求的操作的权限。

## <a name="next-steps"></a>后续步骤

为了更轻松地向 IoT 中心进行身份验证，我们建议使用[Azure IoT sdk](iot-hub-devguide-sdks.md)。