---
title: 如何使用 Azure IoT 中心设备 SDK 管理连接和可靠的消息传送
description: 了解如何在使用 Azure IoT 中心设备 SDK 时改善设备连接和消息传送
services: iot-hub
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 9180c27e64f26c05e6e16007b74f9aa8a98bcfe5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440295"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>使用 Azure IoT 中心设备 SDK 管理连接和可靠的消息传送

本文提供概述性指导，可帮助设计更具弹性的设备应用程序。 它说明如何利用 Azure IoT 设备 SDK 中的连接和可靠消息传送功能。 本指南旨在帮助管理以下方案：

* 修复已删除的网络连接

* 在不同的网络连接之间切换

* 由暂时性服务连接错误导致的重新连接

实现细详信息可能因语言而异。 有关详细信息，请参阅 API 文档或特定 SDK：

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>复原设计

IoT 设备通常依赖非连续或不稳定的网络连接（例如，GSM 或卫星）。 当设备与基于云的服务交互时，由于间歇性服务可用性和基础设施级别故障或暂时性故障，可能会发生错误。 在设备运行的应用程序必须管理连接、 重新连接和发送和接收消息的重试逻辑的机制。 此外，重试策略要求在很大程度上取决于设备的 IoT 方案、上下文和功能。

Azure IoT 中心设备 SDK 旨在简化从云到设备和从设备到云的连接及通信。 这些 SDK 提供了一种连接到 Azure IoT 中心的可靠方式以及一组用于发送和接收消息的全面选项。 开发人员还可以修改现有实现，以针对给定方案自定义更好的重试策略。

支持连接和可靠消息传送的相关 SDK 功能将在以下各节中介绍。

## <a name="connection-and-retry"></a>连接和重试

管理连接时，本部分提供的重新连接，然后重试模式的概述。 它详细介绍了在设备应用程序中使用不同重试策略的实现指南，并列出了设备 SDK 中的相关 API。

### <a name="error-patterns"></a>错误模式

连接故障可能在许多级别中发生：

* 网络错误：断开连接的套接字和名称解析错误

* HTTP、AMQP 和 MQTT 传输的协议级别错误：链接断开或会话过期

* 由本地错误（如无效凭据）或服务行为（例如，超出配额或限制）导致的应用程序级别错误

设备 SDK 在所有三个级别检测错误。 设备 SDK 不会检测和处理与 OS 相关的错误和硬件错误。 SDK 设计基于 Azure 体系结构中心的[暂时性故障处理指南](/azure/architecture/best-practices/transient-faults#general-guidelines)。

### <a name="retry-patterns"></a>重试模式

以下步骤介绍检测到连接错误时的重试过程：

1. SDK 检测网络、协议或应用程序中的错误和相关错误。

2. SDK 使用错误筛选器来确定错误类型并决定是否需要重试。

3. 如果 SDK 确定了**无法恢复的错误**，则会停止连接、发送和接收等操作。 SDK 会通知用户。 无法恢复的错误的示例包括身份验证错误和错误终结点错误。

4. 如果 SDK 确定了**可恢复的错误**，则会根据指定的重试策略进行重试，直到经过定义的超时时间。  请注意，SDK 默认情况下使用**带抖动的指数回退**重试策略。
5. 当定义的超时到期时，SDK 会停止尝试连接或发送。 它会通知用户。

6. SDK 允许用户附加回调以接收连接状态更改。

SDK 提供三种重试策略：

* **带抖动的指数回退**：此默认重试策略往往在开始时激进，并随时间推移减缓，直到达到最大延迟。 该设计基于 [Azure 体系结构中心的重试指南](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)。 

* **自定义重试**：对于某些 SDK 语言，可以设计更适合你的方案的自定义重试策略，然后将其注入 RetryPolicy。 C SDK 中不提供自定义重试。

* **不重试**：可以将重试策略设置为“不重试”，这将禁用重试逻辑。 SDK 假设连接已建立，尝试进行一次连接并发送一次消息。 此策略通常用于有带宽或成本顾虑的方案。 如果选择此选项，则未能发送的消息将丢失且无法恢复。

### <a name="retry-policy-apis"></a>重试策略 API

   | SDK 中 IsInRole 中的声明 | SetRetryPolicy 方法 | 策略实现 | 实施指南 |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **默认**：[IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**自定义：** 使用可用的 [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**不重试：**[IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS 实现](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **默认**：[ExponentialBackoffWithJitter 类](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**自定义：** 实现 [RetryPolicy 接口](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**不重试：**[NoRetry 类](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java 实现](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **默认**：[ExponentialBackoff 类](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**自定义：** 实现 [IRetryPolicy 接口](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**不重试：**[NoRetry 类](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# 实现](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | 节点| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **默认**：[ExponentialBackoffWithJitter 类](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**自定义：** 实现 [RetryPolicy 接口](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**不重试：**[NoRetry 类](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Node 实现](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

以下代码示例说明此流程：

#### <a name="net-implementation-guidance"></a>.NET 实现指南

以下代码示例说明如何定义和设置默认重试策略：

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

为了避免出现高 CPU 使用率，在代码立即失败的情况下会限制重试次数。 例如，当没有到达目的地的网络或路线时。 执行下一次重试的最短时间间隔为 1 秒。

如果服务响应时出现限制错误，则重试策略不同且不能通过公共 API 更改：

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

重试机制在 `DefaultOperationTimeoutInMilliseconds`（目前设置为 4 分钟）后停止。

#### <a name="other-languages-implementation-guidance"></a>其他语言实现指南

有关其他语言的代码示例，请查看以下实现文档。 存储库包含演示如何使用重试策略 API 的示例。

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>后续步骤

* [使用设备和服务 SDK](./iot-hub-devguide-sdks.md)

* [使用适用于 C 的 IoT 设备 SDK](./iot-hub-device-sdk-c-intro.md)

* [针对受约束设备进行开发](./iot-hub-devguide-develop-for-constrained-devices.md)

* [为移动设备进行开发](./iot-hub-how-to-develop-for-mobile-devices.md)

* [排查设备断开连接问题](iot-hub-troubleshoot-connectivity.md)