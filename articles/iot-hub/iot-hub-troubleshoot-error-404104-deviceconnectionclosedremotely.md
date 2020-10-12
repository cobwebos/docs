---
title: 排查 Azure IoT 中心错误 404104 DeviceConnectionClosedRemotely
description: 了解如何修复错误 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81758729"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

本文介绍了 **404104 DeviceConnectionClosedRemotely** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

### <a name="symptom-1"></a>症状 1

设备按固定间隔（例如每 65 分钟）断开连接，并且你会在 IoT 中心诊断日志中看到 **404104 DeviceConnectionClosedRemotely**。 有时，你还会看到 **401003 IoTHubUnauthorized** 和不到一分钟后出现的成功设备连接事件。

### <a name="symptom-2"></a>症状 2

设备随机断开连接，并且你会在 IoT 中心诊断日志中看到 **404104 DeviceConnectionClosedRemotely**。

### <a name="symptom-3"></a>症状 3

许多设备同时断开连接时，你会在[“连接的设备”指标](iot-hub-metrics.md)中看到一个 dip，在诊断日志中有比平常更多的 **404104 DeviceConnectionClosedRemotely** 和 [500xxx 内部错误](iot-hub-troubleshoot-error-500xxx-internal-errors.md)。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 1

[用来连接到 IoT 中心的 SAS 令牌](iot-hub-devguide-security.md#security-tokens)过期，这导致 IoT 中心将设备断开连接。 当设备刷新令牌时，将重新建立连接。 例如，[对于 C SDK，SAS 令牌默认情况下每小时过期](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)，这会导致定期断开连接。

若要了解详细信息，请参阅 [401003 IoTHubUnauthorized 原因](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)。

### <a name="cause-2"></a>原因 2

一些可能的原因包括：

- 设备失去基础网络连接的时间超过了 [MQTT keep alive](iot-hub-mqtt-support.md#default-keep-alive-timeout) 时长，导致远程空闲超时。 每个设备的 MQTT keep-alive 设置可能不同。

- 设备发送了一个 TCP/IP 级重置，但是未发送应用程序级 `MQTT DISCONNECT`。 从根本上说，设备突然关闭了基础套接字连接。 有时候，此问题是由较旧版本的 Azure IoT SDK 中的 bug 导致的。

- 设备端应用程序崩溃。

### <a name="cause-3"></a>原因 3

IoT 中心可能遇到了暂时性问题。 请参阅 [IoT 中心内部服务器错误原因](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)。

## <a name="solutions"></a>解决方案

### <a name="solution-1"></a>解决方案 1

请参阅 [401003 IoTHubUnauthorized 解决方案 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>解决方案 2

- 通过[测试连接](tutorial-connectivity.md)，确保设备能够正常连接到 IoT 中心。 如果网络不可靠或时断时续，我们不建议增大 keep-alive 值，因为这可能会导致检测（例如通过 Azure Monitor 警报）花费更长时间。 

- 使用最新版本的 [IoT SDK](iot-hub-devguide-sdks.md)。

### <a name="solution-3"></a>解决方案 3

请参阅 [IoT 中心内部服务器错误的解决方案](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)。

## <a name="next-steps"></a>后续步骤

我们建议使用 Azure IoT 设备 SDK 来可靠地管理连接。 若要了解详细信息，请参阅[使用 Azure IoT 中心设备 SDK 管理连接和可靠的消息传送](iot-hub-reliability-features-in-sdks.md)