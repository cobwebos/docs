---
title: 什么是 OPC 发布服务器 - Azure | Microsoft Docs
description: OPC 发布服务器概述
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 99b3f6b4495ae5981322c90fce251b9b0a18bb5d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451488"
---
# <a name="what-is-opc-publisher"></a>什么是 OPC 发布服务器？

OPC 发布服务器是一种参考实现，演示了如何：

- 连接到现有的 OPC UA 服务器。
- 使用 JSON 有效负载将 OPC UA 服务器中采用 OPC UA Pub/Sub 格式的 JSON 编码遥测数据发布到 Azure IoT 中心。

可以使用 Azure IoT 中心客户端 SDK 支持的任何传输协议：HTTPS、AMQP 和 MQTT。

参考实现包括：

- 用于连接到网络上现有 OPC UA 服务器的 OPC UA 客户端  。
- 端口 62222 上的 OPC UA *服务器*，你可以使用它来管理已发布的内容，并提供 IoT 中心直接方法来执行同样的操作。

可以从 GitHub 下载 [OPC 发布服务器参考实现](https://github.com/Azure/iot-edge-opc-publisher)。

该应用程序使用 .NET Core 技术实现，可以在 .NET Core 支持的任何平台上运行。

OPC 发布服务器实现重试逻辑以建立与不响应一定数量的保持活动状态请求的终结点的连接。 例如，如果 OPC UA 服务器因断电而停止响应。

对于 OPC UA 服务器的每个不同的发布时间间隔，应用程序将创建一个单独的订阅，将通过该订阅更新采用此发布时间间隔的所有节点。

OPC 发布服务器支持批处理发送到 IoT 中心的数据，以减少网络负载。 仅当达到配置的数据包大小时，此批处理才会向 IoT 中心发送数据包。

此应用程序使用 OPC 基金会 OPC UA 参考堆栈作为 NuGet 包。 有关许可条款，请参阅 [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/)。

### <a name="next-steps"></a>后续步骤

现在你已了解什么是 OPC 发布服务器，建议下一步是了解如何[配置 OPC 发布服务器](howto-opc-publisher-configure.md)。
