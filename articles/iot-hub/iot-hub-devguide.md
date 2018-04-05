---
title: Azure IoT 中心开发人员指南 | Microsoft Docs
description: Azure IoT 中心开发人员指南讨论了终结点、安全性、标识注册表、设备管理、直接方法、设备孪生、文件上传、作业、IoT 中心查询语言以及消息传送。
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 31ccf65ccd2ab4e9673900efb1de357ab450b799
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
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

* [从设备到云通信指南][lnk-d2c-guidance]可帮助用户在从设备到云的消息、设备孪生的报告属性和文件上传之间做出选择。
* [云到设备的通信指南][lnk-c2d-guidance]有助于在直接方法、设备孪生的所需属性和云到设备的消息之间做出选择。
* [使用 IoT 中心进行设备到云和云到设备的消息传送][devguide-messaging]介绍了 IoT 中心公开的消息传送功能（从设备到云和从云到设备）。
  * [将设备到云的消息发送到 IoT 中心][devguide-messages-d2c]。
  * [通过内置终结点读取设备到云的消息][devguide-builtin].
  * [对设备到云的消息使用自定义终结点和路由规则][devguide-custom]。
  * [从 IoT 中心发送云到设备的消息][devguide-messages-c2d]。
  * [创建和读取 IoT 中心消息][devguide-format]。
* [从设备上传文件][devguide-upload]介绍如何从设备上传文件。 此文章还介绍了上传过程可发送的通知等主题。
* [管理 IoT 中心的设备标识][devguide-identities]介绍各 IoT 中心的标识注册表存储的信息。 本文还介绍了如何对访问和修改这些信息。
* [控制对 IoT 中心的访问][devguide-security]说明用于向设备和云组件授予 IoT 中心功能访问权限的安全模型。 此文章包括有关使用令牌和 X.509 证书的信息，以及可以授予的权限的详细信息。
* [使用设备孪生来同步状态和配置][devguide-device-twins]介绍设备孪生概念。 本文还介绍了设备孪生具备的功能，例如使用设备孪生来同步设备。 此文章包括有关设备孪生中存储的数据的信息。
* [在设备上调用直接方法][devguide-directmethods]介绍直接方法的生命周期。 本文介绍如何通过后端应用在设备上调用方法，以及如何在设备上处理直接方法。
* [在多台设备上计划作业][devguide-jobs]介绍如何在多台设备上计划作业。 此文章介绍如何提交作业，以在执行直接方法，使用设备孪生更新设备时执行任务。 它还介绍如何查询作业的状态。
* [参考 - 选择通信协议][devguide-protocol]介绍了用于设备通信且受 IoT 中心支持的通信协议，并列出了应该打开的端口。
* [参考 - IoT 中心终结点][devguide-endpoints]说明了每个 IoT 中心针对运行时和管理操作公开的各种终结点。 此文还介绍了如何在 IoT 中心创建附加终结点，以及如何在非标准方案中使用现场网关实现到 IoT 中心终结点的连接。
* [参考 - 设备孪生、作业和消息路由的 IoT 中心查询语言][devguide-query]介绍了可用于从中心检索设备孪生和作业相关信息的 IoT 中心查询语言。
* [参考 - 配额和限制][devguide-quotas]总结了 IoT 中心服务中设置的配额，以及当超过配额时会发生的限制。
* [参考 - 定价][devguide-pricing]提供有关 IoT 中心的不同 SKU 和定价的常规信息，以及 IoT 中心如何将各种 IoT 中心功能作为消息计量的详细信息。
* [参考 - 设备和服务 SDK][devguide-sdks] 列出了用于开发与 IoT 中心交互的设备和服务应用的 Azure IoT SDK。 此文章包括指向联机 API 文档的链接。
* [参考 - IoT 中心 MQTT 支持][devguide-mqtt]详细介绍了 IoT 中心如何支持 MQTT 协议。 此文章介绍对内置到 Azure IoT SDK 的 MQTT 协议的支持，并提供有关直接使用 MQTT 协议的信息。
* [词汇表][devguide-glossary]是与 IoT 中心相关的常见术语的列表。

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
