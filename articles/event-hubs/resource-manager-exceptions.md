---
title: Azure 事件中心 - 资源管理器异常 | Microsoft Docs
description: Azure 资源管理器公开的 Azure 事件中心异常列表以及建议的处理措施。
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936082"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure 事件中心 - 资源管理器异常
本文列出了使用 Azure 资源管理器通过模板或直接调用来与 Azure 事件中心交互时生成的异常。

> [!IMPORTANT]
> 本文档经常更新。 请回头查看是否有更新。

以下部分提供了通过 Azure 资源管理器公开的各种异常/错误。

## <a name="error-code-conflict"></a>错误代码：冲突

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 冲突 | 40300 | 已达到或超过 EventHub 类型的资源数上限。 实际数目: #，最大允许数目: # | 命名空间已达到其可以包含的事件中心数[配额](event-hubs-quotas.md)。 | 从命名空间中删除任何未使用的或无关的事件中心，或考虑升级到[专用群集](event-hubs-dedicated-overview.md)。 |
| 冲突 | none | 由于复制正在进行，无法删除灾难恢复 (DR) 配置。 在尝试删除 DR 配置之前进行故障转移或中断配对。 | [GeoDR 复制](event-hubs-geo-dr.md)正在进行中，因此此时无法删除配置。 | 若要取消阻止删除配置，请等待复制完成，触发故障转移，或中断异地灾难恢复配对。 |
| 冲突 | none | 后端存在冲突，命名空间更新失败。 | 当前正在对此命名空间执行另一项操作。 | 等待当前操作完成，然后重试。 |

## <a name="error-code-429"></a>错误代码： 429

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | none | 命名空间预配正在转换 | 当前正在对此命名空间执行另一项操作。 | 等待当前操作完成，然后重试。 |
| 429 | none | 灾难恢复操作正在进行。 | 当前正在此命名空间或配对上执行[GeoDR](event-hubs-geo-dr.md)操作。 | 等待当前异地灾难恢复操作完成，然后重试。 |

## <a name="error-code-badrequest"></a>错误代码：错误请求

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | 无法更改事件中心的 PartitionCount。 | Azure 事件中心基本层或标准层不支持更改分区。 | 在基本或标准层命名空间中使用所需的分区数创建新的事件中心。 [专用群集](event-hubs-dedicated-overview.md)支持分区横向扩展。 |
| BadRequest | 40000 | MessageRetentionInDays 的 '#' 值对于基本层无效。 该值不能超过 '1' 天。 | 基本层事件中心命名空间仅支持最长 1 天的消息保留期。 | 如果需要一天以上的消息保留期，请[创建标准事件中心命名空间](event-hubs-create.md)。 | 
| BadRequest | none | 指定的名称不可用。 | 命名空间名称必须唯一，而指定的名称已被使用。 | 如果你是使用指定名称的现有命名空间的所有者，可将其删除，但这会导致数据丢失。 然后重试相同的名称。 如果不能安全删除该命名空间（或者你不是所有者），请选择另一个命名空间名称。 |
| BadRequest | none | 指定的订阅已达到其命名空间配额。 | 订阅已达到其可以包含的命名空间数[配额](event-hubs-quotas.md)。 | 考虑删除此订阅中未使用的命名空间、创建另一个订阅，或升级到[专用群集](event-hubs-dedicated-overview.md)。 |
| BadRequest | none | 无法更新辅助命名空间 | 无法更新命名空间，因为它是[GeoDR 配对](event-hubs-geo-dr.md)中的辅助命名空间。 | 如果合适，请改为更改此配对中的主命名空间。 否则，中断 GeoDR 配对以进行更改。 |
| BadRequest | none | 无法在基本 SKU 中设置自动扩充 | 无法在基本层事件中心命名空间中启用自动扩充。 | 若要在命名空间中[启用自动扩充](event-hubs-auto-inflate.md)，请确保它位于标准层。 |
| BadRequest | none | 容量不足，无法创建命名空间。 请联系事件中心管理员。 | 所选区域已达到容量限制，无法创建更多命名空间。 | 选择另一个区域来容纳命名空间。 |
| BadRequest | none | 无法对实体类型 'ConsumerGroup' 执行该操作，因为命名空间 '<命名空间名称>' 使用的是“基本”层。  | 基本层事件中心命名空间的 [配额]((event-hubs-quotas.md#event-hubs-basic-and-standard---quotas-and-limits) 为一个使用者组（默认设置）。 不支持创建更多使用者组。 | 继续使用默认使用者组 ($Default)，如果需要更多使用者组，请考虑改用标准层事件中心命名空间。 | 
| BadRequest | none | 命名空间 '<命名空间名称>' 不存在。 | 找不到提供的命名空间。 | 仔细检查命名空间名称是否正确，以及是否可以在订阅中找到该名称。 如果找不到，请[创建事件中心命名空间](event-hubs-create.md)。 | 
| BadRequest | none | 资源的 location 属性与其包含命名空间不匹配。 | 在特定区域中创建事件中心失败，因为此区域与命名空间的区域不匹配。 | 尝试在命名空间所在的同一区域中创建事件中心。 | 

## <a name="error-code-internal-server-error"></a>错误代码：内部服务器错误

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 内部服务器错误 | none | 内部服务器错误。 | 事件中心服务出现内部错误。 | 重试失败的操作。 如果操作仍然失败，请联系支持人员。 |