---
title: include 文件
description: include 文件
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 362c13771e7382ead1ba5aebd99a69fd86cd718c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793268"
---
<!-- operation names for the diag logs for IoT Hub -->

|操作名称|Level|说明|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|信息|无法使用给定条件对消息进行评估。 例如，如果消息中缺少路由查询条件中的属性。 详细了解[路由查询语法](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)。|
|RouteEvaluationError|错误|由于消息格式存在问题，因此评估消息时出错。 例如，如果消息中未指定内容编码或内容类型无效，则会记录此错误。 必须在[系统属性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中设置这些内容。|
|DroppedMessage|错误|消息已被丢弃且未进行路由。 造成这种问题的原因可能是消息与任何路由查询都不匹配或终结点失效并且在多次重试后仍然无法传递消息等。 建议使用 REST API [get endpoint health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 来获取有关端点的更多详细信息。|
|EndpointUnhealthy|错误|终结点未接受来自 IoT 中心的消息，IoT 中心正在尝试重新发送消息。 建议通过 REST API [get endpoint health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 来观察上一个已知错误。|
|EndpointDead|错误|终结点已经有一个多小时未接收来自 IoT 中心的消息了。 建议通过 REST API [get endpoint health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 来观察上一个已知错误。|
|EndpointHealthy|信息|终结点正常运行，并从 IoT 中心接收消息。 此消息不会持续记录，仅当终结点再次恢复正常时才记录。 此消息表示 IoT 中心无法将消息发送到终结点，但终结点当前正常。|
|OrphanedMessage|信息|消息与任何路由都不匹配。|
|InvalidMessage|错误|由于与终结点不兼容，消息无效。 建议检查终结点的配置。|


*UndefinedRouteEvaluation* 、*RouteEvaluationError* 和 *OrphanedMessage* 操作限制为每个 IoT 中心每分钟记录一次。