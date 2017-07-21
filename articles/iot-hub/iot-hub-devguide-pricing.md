---
title: "了解 Azure IoT 中心定价 | Microsoft Docs"
description: "开发人员指南 - 介绍了如何实现 IoT 中心（包括样例）的计数和定价。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: elioda
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3470473e1b2aa107c32643a66092b68bfafd1a37
ms.contentlocale: zh-cn
ms.lasthandoff: 06/01/2017


---

# <a name="azure-iot-hub-pricing-information"></a>Azure IoT 中心定价信息

[Azure IoT 中心定价][lnk-pricing]概述了 IoT 中心的不同 SKU 和定价。 本文还详述了 IoT 中心如何按消息对各种 IoT 中心功能计费。

## <a name="charges-per-operation"></a>每个操作的费用

| 操作 | 计费信息 | 
| --------- | ------------------- |
| 标识注册表操作 <br/> （创建、检索、列表、更新、删除） | 不收费。 |
| 设备到云的消息 | 成功发送的消息按流入 IoT 中心的 4KB 区块大小为单位收费，例如，1 条 6KB 的消息按 2 条消息收费。 |
| 云到设备的消息 | 成功发送的消息按 4KB 区块大小为单位收费，例如，1 条 6KB 的消息按 2 条消息收费。 |
| 文件上传 | 向 Azure 存储传输文件时，不通过 IoT 中心计费。 文件传输时的启动消息和完成消息按 4KB 增量数计费。 例如，传输一个 10MB 的文件时，会在 Azure 存储费用的基础上收取两条消息的费用。 |
| 直接方法 | 成功的方法请求按 4KB 区块大小为单位收费，正文非空的响应作为附加消息以 4KB 为单位收费。 向已断开连接的设备发出的请求被视作消息，以 4KB 块大小为单位收费。 例如，如果某方法的正文为 6KB 且引发的设备响应没有正文，则按两条消息收费；如果某方法的正文为 6KB 且引发的设备响应为 1KB，则请求按两条消息收费，响应按 1 条消息收费。 |
| 设备孪生读取 | 如果设备孪生从设备和解决方案后端进行读取，则将其作为消息以 512 字节块大小为单位收费。 例如，读取 6KB 的设备孪生按 12 条消息收费。 |
| 设备孪生更新（标记和属性） | 从设备和设备进行的设备孪生更新被视作消息，以 512 字节块大小为单位收费。 例如，读取 6KB 的设备孪生按 12 条消息收费。 |
| 设备孪生查询 | 查询被视作消息，根据结果大小以 512 字节块大小为单位收费。 |
| 作业操作 <br/> （创建、更新、列表、删除） | 不收费。 |
| 作业的每设备操作数 | 作业操作（例如设备孪生更新和方法）正常收费。 例如，如果一个作业生成 1000 个方法调用以及 1KB 的请求和正文为空的响应，则按 1000 条消息收费。 |

> [!NOTE]
> 所有大小均按以字节为单位的有效负载大小进行计算（忽略协议组帧）。 就消息（包含属性和正文）来说，大小以无关协议的方式计算，详见 [IoT 中心消息传送开发人员指南][lnk-message-size]。

## <a name="example-1"></a>示例 #1

某个设备每分钟将一条 1KB 的设备到云的消息发送到 IoT 中心，该消息随后由 Azure 流分析读取。 解决方案后端每 10 分钟调用设备上的 1 个方法（有效负载为 512 字节），以便触发特定操作。 设备以 200 字节大小的结果响应方法。

对于设备到云的消息，设备每天使用的消息数为：1 条消息 * 60 分钟 * 24 小时 = 1440 条消息；而对于方法，每天的消息数为：2（请求加响应）* 6 次/小时 * 24 小时 = 288 条消息，总计 1728 条消息/天。

## <a name="example-2"></a>示例 2

某设备每小时发送 1 条 100KB 的设备到云的消息。 它还每隔 4 小时更新一次有效负载为 1KB 的设备孪生。 解决方案后端每天读取一次 14KB 的设备孪生并使用 512 字节的有效负载更新一次，以更改配置。

对于设备到云的消息，设备使用的消息数为：25 (100KB / 4KB) 条消息 * 24 小时，再加上进行设备孪生更新的消息数：1 条消息 * 6 次/天，总计 156 条消息/天。
解决方案后端使用 28 条消息 (14KB/0.5KB) 读取设备孪生，并使用 1 条消息进行更新，总计 29 条消息。

设备和解决方案后端每天总共使用 185 条消息。


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md

