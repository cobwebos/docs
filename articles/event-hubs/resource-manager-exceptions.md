---
title: Azure 事件中心-资源管理器例外 |Microsoft Docs
description: Azure 事件中心异常的列表，azure 资源管理器和建议的操作。
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
ms.sourcegitcommit: 6e42ce0ca0a7ac572398e9d024fcf69906670d74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74936082"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure 事件中心-资源管理器例外
本文列出了使用 Azure 资源管理器通过模板或直接调用与 Azure 事件中心交互时生成的异常。

> [!IMPORTANT]
> 此文档经常更新。 请查看是否有更新。

以下部分提供通过 Azure 资源管理器显示的各种异常/错误。

## <a name="error-code-conflict"></a>错误代码：冲突

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 冲突 | 40300 | 已达到或超过 EventHub 类型的最大资源数。 实际： #，允许的最大值：# | 命名空间已达到它可以包含的事件中心数的[配额](event-hubs-quotas.md)。 | 从命名空间中删除所有未使用或无关的事件中心，或考虑升级到[专用群集](event-hubs-dedicated-overview.md)。 |
| 冲突 | 无 | 无法删除灾难恢复（DR）配置，因为正在进行复制。 在尝试删除 DR 配置之前，故障转移或中断配对。 | 正在进行[GeoDR 复制](event-hubs-geo-dr.md)，因此此时无法删除配置。 | 若要取消阻止删除配置，请等待复制完成，触发故障转移，或中断 GeoDR 配对。 |
| 冲突 | 无 | 命名空间更新失败，后端出现冲突。 | 当前正在此命名空间上执行另一操作。 | 等待当前操作完成，然后重试。 |

## <a name="error-code-429"></a>错误代码：429

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 无 | 转换中的命名空间预配 | 当前正在此命名空间上执行另一操作。 | 等待当前操作完成，然后重试。 |
| 429 | 无 | 正在进行灾难恢复操作。 | 当前正在此命名空间或配对上完成[GeoDR](event-hubs-geo-dr.md)操作。 | 等待当前 GeoDR 操作完成，然后重试。 |

## <a name="error-code-badrequest"></a>错误代码： BadRequest

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | 不能为事件中心更改 PartitionCount。 | Azure 事件中心的基本或标准层不支持更改分区。 | 在基本或标准层命名空间中，创建具有所需分区数的新事件中心。 [专用群集](event-hubs-dedicated-overview.md)支持分区向外扩展。 |
| BadRequest | 40000 | 对于 "基本" 层，MessageRetentionInDays 的 "#" 值无效。 该值不能超过 "1" 天。 | 基本层事件中心命名空间仅支持最长为1天的消息保持。 | 如果需要的消息保留时间超过一天，请[创建一个标准事件中心命名空间](event-hubs-create.md)。 | 
| BadRequest | 无 | 指定的名称不可用。 | 命名空间名称必须是唯一的，且已有指定的名称。 | 如果你是具有指定名称的现有命名空间的所有者，则可以将其删除，这将导致数据丢失。 然后，重试相同的名称。 如果无法安全删除该命名空间（或您不是所有者），请选择另一个命名空间名称。 |
| BadRequest | 无 | 指定的订阅已达到其命名空间配额。 | 你的订阅已达到其可以拥有的命名空间数量的[配额](event-hubs-quotas.md)。 | 请考虑删除此订阅中未使用的命名空间、创建其他订阅或升级到[专用群集](event-hubs-dedicated-overview.md)。 |
| BadRequest | 无 | 无法更新辅助命名空间 | 无法更新命名空间，因为它是[GeoDR 配对](event-hubs-geo-dr.md)中的辅助命名空间。 | 如果需要，请改为对此配对中的主要命名空间进行更改。 否则，中断 GeoDR 配对以进行更改。 |
| BadRequest | 无 | 无法在基本 SKU 中设置自动膨胀 | 无法在基本层事件中心命名空间上启用自动膨胀。 | 若要在命名空间上[启用自动膨胀](event-hubs-auto-inflate.md)，请确保它是标准层。 |
| BadRequest | 无 | 没有足够的容量来创建命名空间。 请联系事件中心管理员。 | 所选区域处于容量，无法创建更多的命名空间。 | 选择另一个区域来容纳命名空间。 |
| BadRequest | 无 | 无法对实体类型 "ConsumerGroup" 执行该操作，因为命名空间 "namespace name" 正在使用 "基本" 层。  | 基本层事件中心命名空间具有一个使用者组（默认值）的 [配额] （（事件中心-配额，# 事件中心-基本和标准---配额和限制）。 不支持创建更多使用者组。 | 继续使用默认使用者组（$Default），或者如果需要更多，请考虑改用标准层事件中心命名空间。 | 
| BadRequest | 无 | 命名空间 "命名空间名称" 不存在。 | 找不到提供的命名空间。 | 请仔细检查命名空间名称是否正确，以及是否可以在订阅中找到该名称。 如果不是，请[创建事件中心命名空间](event-hubs-create.md)。 | 
| BadRequest | 无 | 资源的 location 属性与其包含的命名空间不匹配。 | 在特定区域中创建事件中心失败，因为它不匹配命名空间的区域。 | 尝试在命名空间所在的同一区域中创建事件中心。 | 

## <a name="error-code-internal-server-error"></a>错误代码：内部服务器错误

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 内部服务器错误 | 无 | 内部服务器错误。 | 事件中心服务出现内部错误。 | 重试失败的操作。 如果操作仍失败，请联系支持人员。 |