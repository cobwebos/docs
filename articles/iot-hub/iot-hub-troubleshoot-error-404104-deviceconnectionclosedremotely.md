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
ms.openlocfilehash: 686946b65b4b7540f404a291c87c5ad9c7b7a0ca
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960797"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

本文介绍**404104 DeviceConnectionClosedRemotely**错误的原因和解决方案。

## <a name="symptoms"></a>症状

### <a name="symptom-1"></a>症状1

设备会按固定间隔（例如每65分钟）断开连接，并在 IoT 中心诊断日志中看到**404104 DeviceConnectionClosedRemotely** 。 有时，稍后会出现**401003 IoTHubUnauthorized**和成功的设备连接事件。

### <a name="symptom-2"></a>症状2

设备会随机断开连接，并且会在 IoT 中心诊断日志中看到**404104 DeviceConnectionClosedRemotely** 。

### <a name="symptom-3"></a>症状3

许多设备同时断开连接时，会在 "连接的[设备" 指标](iot-hub-metrics.md)中看到一个 dip，而且诊断日志中的 DeviceConnectionClosedRemotely 和[500xxx 的内部错误](iot-hub-troubleshoot-error-500xxx-internal-errors.md)比平时**404104**多。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 1

[用于连接到 Iot 中心的 SAS 令牌](iot-hub-devguide-security.md#security-tokens)已过期，导致 iot 中心断开设备的连接。 当设备刷新令牌时，将重新建立连接。 例如，[默认情况下，对于 C SDK，SAS 令牌每小时过期一次](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)，这可能会导致定期断开连接。

若要了解详细信息，请参阅[401003 IoTHubUnauthorized 原因](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)。

### <a name="cause-2"></a>原因 2

一些可能的原因包括：

- 设备断开基础网络连接的时间超过[MQTT 的持续](iot-hub-mqtt-support.md#default-keep-alive-timeout)时间，导致远程空闲超时。 每台设备的 MQTT 保持活动设置可能不同。

- 设备发送了 TCP/IP 级别的重置，但未发送应用程序级别的 `MQTT DISCONNECT`。 基本上，设备突然关闭了基础套接字连接。 有时，此问题是由旧版 Azure IoT SDK 中的 bug 引起的。

- 设备端应用程序崩溃。

### <a name="cause-3"></a>原因 3

IoT 中心可能遇到暂时性问题。 请参阅[IoT 中心内部服务器错误原因](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)。

## <a name="solutions"></a>解决方案

### <a name="solution-1"></a>解决方案 1

请参阅[401003 IoTHubUnauthorized 解决方案 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>解决方案 2

- 通过[测试连接](tutorial-connectivity.md)，确保设备可以正常连接到 IoT 中心。 如果网络不可靠或不正常，我们不建议增加 keep-alive 值，因为这可能会导致检测（例如通过 Azure Monitor 警报）需要更长时间。 

- 使用最新版本的[IoT sdk](iot-hub-devguide-sdks.md)。

### <a name="solution-3"></a>解决方案 3

请参阅[IoT 中心内部服务器错误的解决方案](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)。

## <a name="next-steps"></a>后续步骤

建议使用 Azure IoT 设备 Sdk 来可靠地管理连接。 若要了解详细信息，请参阅[使用 Azure IoT 中心设备 Sdk 管理连接和可靠消息传送](iot-hub-reliability-features-in-sdks.md)