---
title: Azure 事件网格支持的指标
description: 本文提供 Azure 事件网格服务支持的 Azure Monitor 指标。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3b22beafc9f88d2d95b25fd7ad2f2308a4df9097
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116414"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure 事件网格支持的指标
本文提供按命名空间分类的事件网格指标的列表。 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|主题|
|PublishFailCount|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|Topic,ErrorType,Error|
|PublishSuccessLatencyInMs|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName,Error,ErrorType|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|无|
|PublishFailCount|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType,Error|
|UnmatchedEventCount|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无|
|PublishSuccessLatencyInMs|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|EventSubscriptionName|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|EventSubscriptionName|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|EventSubscriptionName|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason、EventSubscriptionName|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason、EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|无|
|PublishFailCount|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|UnmatchedEventCount|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无|
|PublishSuccessLatencyInMs|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|EventSubscriptionName|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|EventSubscriptionName|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|EventSubscriptionName|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason、EventSubscriptionName|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason、EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|无|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error, ErrorType|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|无|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|无|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|无|
|PublishFailCount|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|UnmatchedEventCount|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无|
|PublishSuccessLatencyInMs|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无|

## <a name="next-steps"></a>后续步骤
请参阅以下文章：[诊断日志](diagnostic-logs.md)
