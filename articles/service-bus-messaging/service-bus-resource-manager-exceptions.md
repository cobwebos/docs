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
ms.openlocfilehash: 9a2d25aba03156d6d14fe5ef9aa58b3748033b85
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296384"
---
# <a name="service-bus-resource-manager-exceptions"></a>Service Bus 资源管理器例外

本文列出了使用 Azure 资源管理器通过模板或直接调用与 Azure 服务总线交互时生成的异常。

> [!IMPORTANT]
> 此文档经常更新。 请回头查看是否有更新。

下面是通过 Azure 资源管理器显示的各种异常/错误。

## <a name="error-bad-request"></a>错误：错误的请求

"错误的请求" 表示资源管理器收到的请求未通过验证。

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 错误的请求 | 40000 | 子代码 = 40000。 创建队列时无法设置属性 *"属性名称"* ，因为命名空间 *"命名空间名称"* 正在使用 "基本" 层。 此操作仅在 "标准" 层或 "高级" 层中受支持。 | 在 Azure 服务总线基本层上，无法设置或更新以下属性- <ul> <li> Queuedescription.requiresduplicatedetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> Microsoft.servicebus.messaging.queuedescription.enableexpress </li> <li> ForwardTo </li> <li> 主题 </li> </ul> | 请考虑从基本层升级到标准层或高级层，以使用此功能。 |
| 错误的请求 | 40000 | 子代码 = 40000。 无法更改现有队列（或主题）的 "Queuedescription.requiresduplicatedetection" 属性的值。 | 创建实体时，必须启用/禁用重复检测。 创建后，不能更改重复检测配置参数。 | 若要对先前创建的队列/主题启用重复检测，可以创建具有重复检测的新队列/主题，然后将原始队列转发到新的队列/主题。 |
| 错误的请求 | 40000 | 子代码 = 40000。 指定的值16384无效。 属性 "MaxSizeInMegabytes" 必须为以下值之一：1024; 2048; 3072; 4096; 5120。 | MaxSizeInMegabytes 值无效。 | 确保 MaxSizeInMegabytes 是1024、2048、3072、4096、5120中的一种。 |
| 错误的请求 | 40000 | 子代码 = 40000。 无法更改队列/主题的分区。 | 无法更改实体的分区。 | 创建新的实体（队列或主题）并启用分区。 | 
| 错误的请求 | 无 | 命名空间 *"命名空间名称"* 不存在。 | Azure 订阅中不存在该命名空间。 | 若要解决此错误，请尝试以下 <ul> <li> 确保 Azure 订阅是正确的。 </li> <li> 确保命名空间存在。 </li> <li> 验证命名空间名称是否正确（无拼写错误或空字符串）。 </li> </ul> | 
| 错误的请求 | 40400 | 子代码 = 40400。 自动转发目标实体不存在。 | 自动转发目标实体的目标不存在。 | 目标实体（队列或主题）必须存在，然后才能创建源。 请在创建目标实体后重试。 |
| 错误的请求 | 40000 | 子代码 = 40000。 提供的锁定时间超出了允许的最大值 "5" 分钟。 | 可锁定消息的时间必须介于1分钟（最小值）和5分钟（最大值）之间。 | 请确保提供的锁定时间介于1分钟到5分钟之间。 |
| 错误的请求 | 40000 | 子代码 = 40000。 不能同时启用 DelayedPersistence 和 Queuedescription.requiresduplicatedetection 属性。 | 启用了重复检测的实体必须是持久的，因此不能延迟持久性。 | 了解有关[重复检测](duplicate-detection.md)的详细信息 |
| 错误的请求 | 40000 | 子代码 = 40000。 无法更改现有队列的 RequiresSession 属性的值。 | 应在创建实体时启用对会话的支持。 一旦创建，就不能启用/禁用现有实体（队列或订阅）上的会话。 | 删除并重新创建启用了 "RequiresSession" 属性的新队列（或订阅）。 |
| 错误的请求 | 40000 | 子代码 = 40000。 "URI_PATH" 包含服务总线不允许使用的字符。 实体段只能包含字母、数字、句点（.）、连字符（-）和下划线（_）。 | 实体段只能包含字母、数字、句点（.）、连字符（-）和下划线（_）。 任何其他字符都会导致请求失败。 | 请确保 URI 路径中没有任何无效字符。 |


## <a name="error-code-429"></a>错误代码：429

与在 HTTP 中一样，"错误代码 429" 指示 "请求太多"。 这意味着特定资源（命名空间）被限制，因为该资源的请求太多（或由于操作冲突）。

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | 子代码 = 50004。 请求已终止，因为正在阻止*命名空间*的命名空间。 | 当传入请求数超出资源限制时，会出现此错误条件。 | 等待几秒钟，然后重试。 <br/> <br/> 详细了解[配额](service-bus-quotas.md)和[Azure 资源管理器请求限制](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | 子代码 = 40901。 正在进行另一个冲突的操作。 | 正在对同一资源/实体执行其他冲突操作 | 等待当前正在进行的操作完成，然后重试。 |
| 429 | 40900 | 子代码 = 40900。 合并. 在资源的当前状态下不允许所请求的操作。 | 当多个请求同时对同一实体（队列、主题、订阅或规则）执行操作时，可能会发生这种情况。 | 等待几秒钟，然后重试 |
| 429 | 40901 | 实体 *"实体名称"* 上的请求与其他请求冲突 | 正在对同一资源/实体执行其他冲突操作 | 等待前一个操作完成，然后重试 |
| 429 | 40901 | 正在为实体 *"实体名称"* 进行另一个更新请求。 | 正在对同一资源/实体执行其他冲突操作 | 等待前一个操作完成，然后重试 |
| 429 | 无 | 发生资源冲突。 可能正在进行另一个冲突的操作。 如果重试失败的操作，后台清理仍处于挂起状态。 请稍后重试。 | 对于同一实体存在挂起的操作时，可能会命中此条件。 | 等待前一个操作完成，然后重试。 |


## <a name="error-code-not-found"></a>错误代码：未找到

此类错误表示找不到资源。

| 错误代码 | 错误子代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 未找到 | 无 | 找不到实体 *"实体名称"* 。 | 找不到操作的实体。 | 请检查实体是否存在，并重试该操作。 |
| 未找到 | 无 | 找不到。 该操作不存在。 | 尝试执行的操作不存在。 | 请检查该操作，然后重试。 |
| 未找到 | 无 | 传入请求未被识别为命名空间策略 put 请求。 | 传入请求正文为 null，因此无法作为 put 请求来执行。 | 请检查请求正文，确保它不为 null。 | 
| 未找到 | 无 | 找不到消息实体 *"实体名称"* 。 | 找不到您尝试对其执行操作的实体。 | 请检查实体是否存在，并重试该操作。 |
