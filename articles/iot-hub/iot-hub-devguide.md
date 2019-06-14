---
title: Azure IoT 中心开发人员指南 | Microsoft Docs
description: Azure IoT 中心开发人员指南讨论了终结点、安全性、标识注册表、设备管理、直接方法、设备孪生、文件上传、作业、IoT 中心查询语言以及消息传送。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 1ff7d430edd3f638ad5efcc5a89604e4ed732211
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400144"
---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT 中心开发人员指南

Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT 中心提供：

* 使用每个设备的安全凭据和访问控制来保护通信安全。

* 多个设备到云和云到设备的超大规模通信选项。

* 各设备状态信息和元数据的可查询存储。

* 通过最流行语言和平台的设备库来方便建立设备连接。

本 IoT 中心开发人员指南包括以下文章：

* [设备到云通信指南](iot-hub-devguide-d2c-guidance.md)帮助你在设备到云消息、设备孪生的报告属性和文件上传之间进行选择。

* [云到设备通信指南](iot-hub-devguide-c2d-guidance.md)帮助你在直接方法、设备孪生的所需属性和云到设备消息之间进行选择。

* [使用 IoT 中心进行设备到云和云到设备的消息传送](iot-hub-devguide-messaging.md)介绍了 IoT 中心公开的消息传递功能（设备到云和云到设备）。

  * [将设备到云消息发送到 IoT 中心](iot-hub-devguide-messages-d2c.md)。

  * [从内置终结点读取设备到云消息](iot-hub-devguide-messages-read-builtin.md)。

  * [对设备到云消息使用自定义终结点和传递规则](iot-hub-devguide-messages-read-custom.md)。

  * [从 IoT 中心发送云到设备消息](iot-hub-devguide-messages-c2d.md)。

  * [创建和读取 IoT 中心消息](iot-hub-devguide-messages-construct.md)。

* [从设备上传文件](iot-hub-devguide-file-upload.md)介绍如何从设备上传文件。 此文章还介绍了上传过程可发送的通知等主题。

* [管理 IoT 中心的设备标识](iot-hub-devguide-identity-registry.md)介绍每个 IoT 中心的标识注册表存储的信息。 本文还介绍了如何对访问和修改这些信息。

* [控制对 IoT 中心的访问](iot-hub-devguide-security.md)说明用于向设备和云组件授予 IoT 中心功能访问权限的安全模型。 此文章包括有关使用令牌和 X.509 证书的信息，以及可以授予的权限的详细信息。

* [使用设备孪生来同步状态和配置](iot-hub-devguide-device-twins.md)介绍*设备孪生*概念。 此外介绍了功能设备孪生公开，如使用设备孪生同步设备。 此文章包括有关设备孪生中存储的数据的信息。

* [在设备上调用直接方法](iot-hub-devguide-direct-methods.md)介绍直接方法的生命周期。 本文介绍如何通过后端应用在设备上调用方法，以及如何在设备上处理直接方法。

* [在多台设备上计划作业](iot-hub-devguide-jobs.md)介绍如何在多台设备上计划作业。 此文章介绍如何提交作业，以在执行直接方法，使用设备孪生更新设备时执行任务。 它还介绍如何查询作业的状态。

* [参考 - 选择通信协议](iot-hub-devguide-protocols.md)介绍了用于设备通信且受 IoT 中心支持的通信协议，并列出了应该打开的端口。

* [参考 - IoT 中心终结点](iot-hub-devguide-endpoints.md)说明了每个 IoT 中心针对运行时和管理操作公开的各种终结点。 此文还介绍了如何在 IoT 中心创建附加终结点，以及如何在非标准方案中使用现场网关实现到 IoT 中心终结点的连接。

* [参考 - 设备孪生、作业和消息路由的 IoT 中心查询语言](iot-hub-devguide-query-language.md)介绍了可用于从中心检索设备孪生和作业相关信息的 IoT 中心查询语言。

* [参考 - 配额和限制](iot-hub-devguide-quotas-throttling.md)总结了 IoT 中心服务中设置的配额，以及当超过配额时会发生的限制。

* [参考 - 定价](iot-hub-devguide-pricing.md)提供有关 IoT 中心的不同 SKU 和定价的一般信息，以及 IoT 中心如何将各种 IoT 中心功能作为消息计量的详细信息。

* [参考 - 设备和服务 SDK](iot-hub-devguide-sdks.md) 列出了用于开发与 IoT 中心交互的设备和服务应用的 Azure IoT SDK。 此文章包括指向联机 API 文档的链接。

* [参考 - IoT 中心 MQTT 支持](iot-hub-mqtt-support.md)详细介绍了 IoT 中心如何支持 MQTT 协议。 此文章介绍对内置到 Azure IoT SDK 的 MQTT 协议的支持，并提供有关直接使用 MQTT 协议的信息。

* [词汇表](iot-hub-devguide-glossary.md)与 IoT 中心相关的常见术语的列表。