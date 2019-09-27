---
title: Azure Service Bus 资源管理器例外 |Microsoft Docs
description: Azure 资源管理器的服务总线异常的列表和建议的操作。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: e666503b9e8888e7d61445639fe0f3adeeffe55f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329300"
---
# <a name="service-bus-resource-manager-exceptions"></a>Service Bus 资源管理器例外

本文列出了使用 Azure 资源管理器通过模板或直接调用与 Azure 服务总线交互时生成的异常。

> [!IMPORTANT]
> 此文档经常更新。 请回头查看是否有更新。

下面是通过 Azure 资源管理器显示的各种异常/错误。

## <a name="error-bad-request"></a>错误：错误请求

"错误的请求" 表示资源管理器收到的请求未通过验证。

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 错误请求 | 40000 | 子代码 = 40000。 创建队列时无法设置属性 *"属性名称"* ，因为命名空间 *"命名空间名称"* 正在使用 "基本" 层。 此操作仅在 "标准" 层或 "高级" 层中受支持。 | 在 Azure 服务总线基本层上，无法设置或更新以下属性- <ul> <li> Queuedescription.requiresduplicatedetection </li> <li> AutoDeleteOnIdle </li> <li>requiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> Microsoft.servicebus.messaging.queuedescription.enableexpress </li> <li> ForwardTo </li> <li> 主题 </li> </ul> | 请考虑从基本层升级到标准层或高级层，以利用此功能。 |
| 错误请求 | 40000 | 子代码 = 40000。 无法更改现有队列的 "Queuedescription.requiresduplicatedetection" 属性的值。 | 创建实体时，必须启用/禁用重复检测。 创建后，不能更改重复检测配置参数。 | 若要对以前创建的队列/订阅启用重复检测，可以创建具有重复检测的新队列，然后从原始队列转发到新队列。 |
| 错误请求 | 40000 | 子代码 = 40000。 指定的值16384无效。 属性 "MaxSizeInMegabytes" 必须为以下值之一：1024; 2048; 3072; 4096; 5120。 | MaxSizeInMegabytes 值无效。 | 确保 MaxSizeInMegabytes 是1024、2048、3072、4096、5120中的一种。 |
| 错误请求 | 40000 | 子代码 = 40000。 无法更改队列的分区。 | 无法更改实体的分区。 | 创建新的实体并启用分区。 | 
| 错误请求 | 无 | 命名空间 *"命名空间名称"* 不存在。 | Azure 订阅中不存在该命名空间。 | 若要解决此错误，请尝试以下 <ul> <li> 确保 Azure 订阅是正确的。 </li> <li> 确保命名空间存在。 </li> <li> 验证命名空间名称是否正确（无拼写错误或空字符串）。 </li> </ul> | 
| 错误请求 | 40400 | 子代码 = 40400。 自动转发目标实体不存在。 | 自动转发目标实体的目标不存在。 | 目标实体（队列或主题）必须存在，然后才能创建源。 请在创建目标实体后重试。 |


## <a name="error-code-429"></a>错误代码：429

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | 子代码 = 50004。 请求已终止，因为正在阻止*命名空间*的命名空间。 | 当传入请求数超出资源限制时，会出现此错误条件。 | 等待几秒钟，然后重试。 <br/> <br/> 详细了解[配额](service-bus-quotas.md)和[Azure 资源管理器请求限制](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | 子代码 = 40901。 正在进行另一个冲突的操作。 | 正在对同一资源/实体执行其他冲突操作 | 等待当前正在进行的操作完成，然后重试。 |
| 429 | 40900 | 子代码 = 40900。 合并. 在资源的当前状态下不允许所请求的操作。 | 当多个请求同时对同一实体（队列、主题、订阅或规则）执行操作时，可能会发生此情况。 | 等待几秒钟，然后重试 |
| 429 | 无 | 发生资源冲突。 可能正在进行另一个冲突的操作。 如果重试失败的操作，后台清理仍处于挂起状态。 请稍后重试。 | 对于同一实体存在挂起的操作时，可能会命中此条件。 | 等待前一个操作完成，然后重试。 |

