---
title: 如何使用 Azure IoT 中心设备 SDK 管理连接和可靠的消息传送
description: 了解如何在使用 Azure IoT 中心设备 SDK 时改善设备连接和消息传送
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d321e82560f7676f77ed9161c1bce0304b7d5914
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397101"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>如何使用 Azure IoT 中心设备 SDK 管理连接和可靠的消息传送

本指南利用 Azure IoT 设备 SDK 中的连接和可靠的消息传送功能，为设计弹性设备应用程序提供了高级指导。 本文旨在回答问题和处理以下这些情况：

- 管理已删除的网络连接
- 管理不同网络连接之间的切换
- 管理由暂时性服务连接错误导致的重新连接

实现详情可能因语言而异，有关更多详细信息，请参阅链接的 API 文档或特定 SDK。

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>复原设计

IoT 设备常依赖非连续和/或不稳定的网络连接，如 GSM 或卫星。 此外，在与基于云的服务交互时，可能会由于间歇性服务可用性和基础结构级别的故障（通常称为暂时性故障）等临时情况而发生错误。 在设备上运行的应用程序需要管理连接和重新连接机制，以及发送/接收消息的重试逻辑。 此外，重试策略要求在很大程度上取决于设备参与的 IoT 场景，以及设备的上下文和功能。

Azure IoT 中心设备 SDK 旨在提供可靠而全面的方法来连接 Azure IoT 中心并从 Azure IoT 中心发送/接收消息，以此简化云到设备和设备到云的连接和通信。 开发人员还可以修改现有的实现，为给定场景开发正确的重试策略。

支持连接和可靠消息传送的相关 SDK 功能将在以下各节中介绍。

## <a name="connection-and-retry"></a>连接和重试

本节概述了管理连接时可用的重新连接和重试模式、在设备应用程序中使用不同重试策略的实施指南以及设备 SDK 的相关 API。

### <a name="error-patterns"></a>错误模式
连接故障可能发生在许多级别中：

-  网络错误，如断开的套接字和名称解析错误
- HTTP、AMQP 和 MQTT 传输的协议级错误，如链接断开或会话过期
- 由本地错误（如无效凭据）或服务行为（如超出配额或限制）导致的应用程序级错误

设备 SDK 在所有三个级别中检测错误。  设备 SDK 不会检测和处理与 OS 相关的错误和硬件错误。  该设计基于 Azure 体系结构中心的[暂时性故障处理指南](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines)。

### <a name="retry-patterns"></a>重试模式

检测到连接错误时重试的整个过程是： 
1. SDK 检测网络、协议或应用程序中的错误和相关错误。
2. 根据错误类型，SDK 使用错误筛选器来决定是否需要执行重试。  如果 SDK 发现不可恢复的错误，操作（连接和发送/接收）将停止，并且 SDK 将通知用户。 对于不可恢复的错误，SDK 可识别并确定错误是否无法恢复，例如身份验证或无效终结点错误。
3. 如果发现可恢复的错误，SDK 会开始使用指定的重试策略重试，直到定义的超时到期。
4. 定义的超时到期时，SDK 停止尝试连接或发送，并通知用户。
5.  SDK 允许用户附加回调以接收连接状态更改。 

提供有三个重试策略：
- **带抖动的指数回退**：这是默认应用的重试策略。  此策略往往在开始时很激进，随后逐渐减缓，然后达到限制内的最大延迟。  该设计基于 [Azure 体系结构中心的重试指南](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)。
- **自定义重试**：可实现自定义重试策略，并根据所选语言将其插入 RetryPolicy 中。 可以设计适合相应场景的重试策略。  这在 C SDK 上不可用。
- **不重试**：可以选择将重试策略设置为“不重试”，这将禁用重试逻辑。  SDK 假设连接已建立，尝试进行一次连接并发送一次消息。 此策略通常用于存在带宽或成本问题的情况。   如果选择此选项，则未能发送的消息将丢失且无法恢复。 

### <a name="retry-policy-apis"></a>重试策略 API

   | SDK 中 IsInRole 中的声明 | SetRetryPolicy 方法 | 策略实现 | 实施指南 |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **默认**：[IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**自定义：** 使用可用的 [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**不重试：**[IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS 实现](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **默认**：[ExponentialBackoffWithJitter 类](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**自定义：** 实现 [RetryPolicy 接口](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**不重试：**[NoRetry 类](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java 实现](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **默认**：[ExponentialBackoff 类](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**自定义：** 实现 [IRetryPolicy 接口](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**不重试：**[NoRetry 类](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# 实现]() |
   | 节点| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **默认**：[ExponentialBackoffWithJitter 类](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**自定义：** 实现 [RetryPolicy 接口](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**不重试：**[NoRetry 类](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Node 实现](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

下面是说明此流的代码示例。 

#### <a name="net-implementation-guidance"></a>.NET 实现指南

以下代码示例显示了如何定义和设置默认重试策略：

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

为避免 CPU 使用率过高，如果代码立即失败（例如，没有网络或通往目标的路由时），重试会受到限制，这样执行下一次重试的最短时间即为 1 秒。 

如果服务响应时出现限制错误，则重试策略不同且不能通过公共 API 更改：

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

重试机制将在 `DefaultOperationTimeoutInMilliseconds`（当前设置为 4 分钟）之后停止。

#### <a name="other-languages-implementation-guidance"></a>其他语言实现指南
对于其他语言，请查看以下实现文档。  存储库中提供了演示使用重试策略 API 的示例。
- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>后续步骤
- [使用设备和服务 SDK](.\iot-hub-devguide-sdks.md)
- [使用适用于 C 的 IoT 设备 SDK](.\iot-hub-device-sdk-c-intro.md)
- [针对受约束设备进行开发](.\iot-hub-devguide-develop-for-constrained-devices.md)
- [为移动设备进行开发](.\iot-hub-how-to-develop-for-mobile-devices.md)
- [排查设备断开连接问题](iot-hub-troubleshoot-connectivity.md)
