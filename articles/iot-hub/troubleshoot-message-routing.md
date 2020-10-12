---
title: Azure IoT 消息路由故障排除
description: 如何对 Azure IoT 消息路由问题进行故障排除
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 871a4c7d99fc44cf9868f19e41560e6e7a2e22f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793278"
---
# <a name="troubleshooting-message-routing"></a>消息路由故障排除

本文提供关于如何监视和解决 IoT 中心[消息路由](iot-hub-devguide-messages-d2c.md)的常见问题的指导。 

## <a name="monitoring-message-routing"></a>监视消息路由

[IoT 中心指标](iot-hub-metrics.md)列出了默认为 IoT 中心启用的所有指标。 建议监视与消息路由和终结点相关的指标，以便对发送的消息有个宏观了解。 同时在 Azure Monitor 诊断设置中启用[诊断日志](iot-hub-monitor-resource-health.md)，以跟踪有关路由的操作。 可以将这些诊断日志发送到 Azure Monitor 日志、事件中心或 Azure 存储进行自定义处理。 了解如何[通过 IoT 中心设置和使用指标和诊断日志](tutorial-use-metrics-and-diags.md)。

如果要维护与任何路由上的查询不匹配的消息，建议同时启用[回退路由](iot-hub-devguide-messages-d2c.md#fallback-route)。 这些路由可以在[内置终结点](iot-hub-devguide-messages-read-builtin.md)中保留至配置的保留天数。 

## <a name="top-issues"></a>常见问题

以下是观察到的最常见的消息路由问题。 若要开始排除故障，请单击问题，了解详细步骤。

* [设备上的消息未按预期方式路由](#messages-from-my-devices-are-not-being-routed-as-expected)
* [从内置事件中心终结点处获取消息的过程突然停止](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>设备上的消息未按预期方式路由

若要解决此问题，请分析以下内容。

#### <a name="the-routing-metrics-for-this-endpoint"></a>此终结点的路由指标
所有与路由相关的 [IoT 中心指标](iot-hub-devguide-endpoints.md)都以“Routing”作为前缀。 你可以组合来自多项指标的信息来确定问题的根本原因。 例如，使用指标“路由传递尝试数”来确定当消息与任何路由上的查询不匹配并且已禁用回退路由时传递到终结点或已删除的消息数。 检查“路由延迟”指标来观察消息传递的延迟是否稳定或增加。 延迟的增加可能表示特定终结点有问题，建议检查[终结点的运行状况](#the-health-of-the-endpoint)。 这些路由指标还具有[维度](iot-hub-metrics.md#dimensions)，这些维度提供有关指标的详细信息，如终结点类型、特定终结点名称和消息未传递的原因。

#### <a name="the-diagnostic-logs-for-any-operational-issues"></a>操作问题的诊断日志 
观察“路由”[诊断日志](iot-hub-monitor-resource-health.md#routes)，获取有关路由和终结点[操作](#operation-names)的详细信息，或者确定错误和相关的[错误代码](#common-error-codes)以进一步了解问题。 例如，日志中的操作名称“RouteEvaluationError”指示由于消息格式的问题，无法计算路由。 使用为特定的[操作名称](#operation-names)提供的提示来缓解此问题。 将某个事件记录为一个错误时，日志还提供计算失败原因的详细信息。 例如，如果操作名称是“EndpointUnhealthy”，则 [错误代码](#common-error-codes) 403004 指示终结点空间不足。

#### <a name="the-health-of-the-endpoint"></a>终结点的运行状况
使用 REST API [获取终结点运行状况](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 获取终结点的[运行状况状态](iot-hub-devguide-endpoints.md#custom-endpoints)。 “获取终结点运行状况”API 还提供有关消息上一次成功发送到终结点的时间、[上一个已知错误](#last-known-errors-for-iot-hub-routing-endpoints)、上一次发生已知错误的时间以及上一次尝试对此终结点发送的时间的信息。 使用为特定的[上一个已知错误](#last-known-errors-for-iot-hub-routing-endpoints)提供的缓解措施。

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>从内置终结点处获取消息的过程突然停止

若要解决此问题，请分析以下内容。

#### <a name="was-a-new-route-created"></a>是否创建了新的路由？
在创建一个路由后，数据将停止流向内置终结点，除非创建了到该终结点的路由。 若要确保在添加新路由后消息继续流向内置终结点，应配置一个指向事件终结点的路由。 

#### <a name="was-the-fallback-route-disabled"></a>回退路由是否处于禁用状态？
回退路由将所有不满足任何现有路由上的查询条件的消息发送到与[事件中心](https://docs.microsoft.com/azure/event-hubs/)兼容的[内置事件中心](iot-hub-devguide-messages-read-builtin.md)（消息/事件）。 如果已启用消息路由，则可以启用此回退路由功能。 如果没有到内置终结点的路由并且已启用回退路由，则仅与路由上的任何查询条件不匹配的消息将被发送到内置终结点。 此外，如果已删除现有路由，必须启用回退路由才能接收内置终结点处的所有数据。

可以在 Azure 门户->“消息路由”边栏选项卡中启用/禁用回退路由。 还可以将 Azure 资源管理器用于 [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) 来为回退路由使用自定义终结点。

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>上一个有个 IoT 中心路由终结点的已知错误

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-diagnostic-logs"></a>路由诊断日志

以下是[诊断日志](iot-hub-monitor-resource-health.md#routes)中记录的操作名称和错误代码。

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>操作名称

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>常见错误代码

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>后续步骤

如果需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
