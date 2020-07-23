---
title: 排查 Azure IoT 消息路由问题
description: 如何对 Azure IoT 消息路由执行故障排除
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 871a4c7d99fc44cf9868f19e41560e6e7a2e22f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84793278"
---
# <a name="troubleshooting-message-routing"></a>消息路由疑难解答

本文提供了有关 IoT 中心[消息路由](iot-hub-devguide-messages-d2c.md)的常见问题和解决方法的监视和故障排除指南。 

## <a name="monitoring-message-routing"></a>监视消息路由

[IoT 中心指标](iot-hub-metrics.md)列出了默认为 IoT 中心启用的所有指标。 建议你监视与消息路由和终结点相关的指标，以便为你概述发送的消息。 同时打开 Azure Monitor 诊断设置中的[诊断日志](iot-hub-monitor-resource-health.md)，以跟踪**路由**的操作。 可以将这些诊断日志发送到 Azure Monitor 日志、事件中心或 Azure 存储进行自定义处理。 了解如何使用[IoT 中心设置和使用指标和诊断日志](tutorial-use-metrics-and-diags.md)。

如果要在任何路由上维护与查询不匹配的消息，建议启用[回退路由](iot-hub-devguide-messages-d2c.md#fallback-route)。 这些设置可以保留在[内置终结点](iot-hub-devguide-messages-read-builtin.md)中，用于配置的保留天数。 

## <a name="top-issues"></a>常见问题

下面是与消息路由有关的最常见问题。 若要开始故障排除，请单击问题以了解详细步骤。

* [设备上的消息未按预期方式路由](#messages-from-my-devices-are-not-being-routed-as-expected)
* [我突然停止了在内置事件中心终结点处获取消息](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>设备上的消息未按预期方式路由

若要解决此问题，请分析以下各项。

#### <a name="the-routing-metrics-for-this-endpoint"></a>此终结点的路由度量值
与路由相关的所有[IoT 中心指标](iot-hub-devguide-endpoints.md)都带有*路由*前缀。 你可以组合来自多项指标的信息来确定问题的根本原因。 例如，使用度量**路由传递尝试**识别已传递到终结点的消息数，或在禁用了任何路由和回退路由上的查询时删除的消息数。 检查**路由延迟**指标以观察消息传递的延迟是否稳定或增加。 增长延迟可能指示特定终结点的问题，我们建议检查[终结点的运行状况](#the-health-of-the-endpoint)。 这些路由度量值还具有一些维度，这些[维度](iot-hub-metrics.md#dimensions)提供有关指标的详细信息，例如终结点类型、特定的终结点名称和无法传递消息的原因。

#### <a name="the-diagnostic-logs-for-any-operational-issues"></a>任何操作问题的诊断日志 
查看**路由**[诊断日志](iot-hub-monitor-resource-health.md#routes)以获取有关路由和终结点[操作](#operation-names)的详细信息，或者标识错误和相关[错误代码](#common-error-codes)，以进一步了解问题。 例如，日志中的操作名称**RouteEvaluationError**表示由于消息格式问题而无法计算路由。 使用为特定[操作名称](#operation-names)提供的提示来缓解此问题。 如果事件记录为错误，日志还将提供有关评估失败原因的详细信息。 例如，如果操作名称为**EndpointUnhealthy**，则[错误代码](#common-error-codes)403004 表示终结点空间不足。

#### <a name="the-health-of-the-endpoint"></a>终结点的运行状况
使用 REST API[获取终结点运行状况](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)，以获取终结点的[运行状况状态](iot-hub-devguide-endpoints.md#custom-endpoints)。 *获取终结点运行状况*API 还提供有关最近一次成功将消息发送到终结点、上[一个已知错误](#last-known-errors-for-iot-hub-routing-endpoints)、上一个已知错误时间以及上次尝试发送此终结点的信息。 使用为特定的[上一个已知错误](#last-known-errors-for-iot-hub-routing-endpoints)提供的可能的缓解措施。

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>我突然停止了在内置终结点处获取消息

若要解决此问题，请分析以下各项。

#### <a name="was-a-new-route-created"></a>是否创建了新的路由？
在创建一个路由后，数据将停止流向内置终结点，除非创建了到该终结点的路由。 若要确保在添加新路由时，消息继续流向内置终结点，请配置到*事件*终结点的路由。 

#### <a name="was-the-fallback-route-disabled"></a>回退路由是否处于禁用状态？
回退路由将所有不满足查询条件的消息发送到与[事件中心](https://docs.microsoft.com/azure/event-hubs/)兼容的[内置事件中心](iot-hub-devguide-messages-read-builtin.md)（消息/事件）的任何现有路由。 如果已启用消息路由，则可以启用此回退路由功能。 如果没有到内置终结点的路由并且已启用回退路由，则仅与路由上的任何查询条件不匹配的消息将被发送到内置终结点。 此外，如果已删除现有路由，必须启用回退路由才能接收内置终结点处的所有数据。

可以在 "Azure 门户 >消息路由" 边栏选项卡中启用/禁用回退路由。 还可以将 Azure 资源管理器用于 [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) 来为回退路由使用自定义终结点。

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>IoT 中心路由终结点的最近已知错误

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-diagnostic-logs"></a>路由诊断日志

下面是在[诊断日志](iot-hub-monitor-resource-health.md#routes)中记录的操作名称和错误代码。

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>操作名称

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>常见错误代码

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>后续步骤

如果需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
