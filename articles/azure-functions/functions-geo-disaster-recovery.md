---
title: Azure Functions 异地灾难恢复和高可用性
description: 如何使用地理区域进行冗余并在 Azure Functions 中进行故障转移。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080231"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions 异地灾难恢复

在整个 Azure 区域或数据中心遇到停机时间时，计算在不同区域中继续进行处理是非常重要的。  本文将介绍一些可用于部署功能以实现灾难恢复的策略。

## <a name="basic-concepts"></a>基本概念

Azure Functions 在特定区域中运行。  若要获得更高的可用性，可以将相同的函数部署到多个区域。  在多个区域中，可以让函数在主动/*主动*模式或*主动/被动*模式中运行。  

* 主动/主动。 这两个区域都处于活动状态和正在接收事件（重复或 rotationally）。 建议将 active/active 用于 HTTPS 函数与 Azure 前门结合使用。
* 主动/被动。 一个区域处于活动状态且正在接收事件，而辅助数据库处于空闲状态。  需要故障转移时，将激活辅助区域并接管处理。  对于非 HTTP 函数（如服务总线和事件中心），建议使用此项。

有关多区域部署的详细信息，请参阅如何[在多个区域中运行应用](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

## <a name="activeactive-for-https-functions"></a>HTTPS 函数的主动/主动

若要实现函数的主动/主动部署，需要在两个区域之间协调事件的某些组件。  对于 HTTPS 函数，此协调使用[Azure 前门](../frontdoor/front-door-overview.md)完成。  Azure 前门可以在多个区域函数之间路由和轮循 HTTPS 请求。  它还会定期检查每个终结点的运行状况。  如果某个区域函数停止了对运行状况检查的响应，则 Azure 前门会将其从轮换中取出，只将流量转发到正常运行的功能。  

![Azure 前门和功能的体系结构](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>针对非 HTTPS 函数的主动/主动

你仍可以实现非 HTTPS 函数的主动/主动部署。  但是，您需要考虑这两个区域之间的相互交互或相互协调的方式。  如果将相同的函数应用部署到两个区域中，每个区域都触发同一服务总线队列，则它们将充当对队列进行取消排队的竞争使用者。  尽管这意味着每条消息仅由一个实例进行处理，但这也意味着单个服务总线上仍存在单点故障。  如果你部署了两个服务总线队列（一个在主要区域中，一个位于次要区域中），而两个 function app 指向它们的区域队列，则该挑战现在就是如何在这两个区域之间分布队列消息。  通常，这意味着每个发布服务器都会尝试将消息发布到*这两个*区域，并且每个消息都由两个活动函数应用处理。  尽管这会创建一个主动/主动模式，但它会在重复计算以及何时或如何合并数据时产生其他挑战。  由于这些原因，对于非 HTTPS 触发器，建议使用主动/被动模式。

## <a name="activepassive-for-non-https-functions"></a>非 HTTPS 函数的主动/被动

主动/被动仅提供一种方法来处理每条消息，但提供在发生灾难时故障转移到次要区域的机制。  Azure Functions 与[Azure 服务总线异地恢复](../service-bus-messaging/service-bus-geo-dr.md)和[azure 事件中心地域恢复](../event-hubs/event-hubs-geo-dr.md)一起工作。

使用 Azure 事件中心触发器作为示例，主动/被动模式将涉及以下内容：

* 同时部署到主要区域和次要区域的 Azure 事件中心。
* 已启用异地灾难，以配对主要和次要事件中心。  这也会创建一个 "别名"，用于连接到事件中心，并从主节点切换到辅助副本，而无需更改连接信息。
* 同时部署到主要区域和次要区域的函数应用。
* 函数应用在其各自事件中心的*直接*（非别名）连接字符串上触发。 
* 事件中心的发布服务器应发布到别名连接字符串。 

![主动-被动示例体系结构](media/functions-geo-dr/active-passive.png)

在故障转移之前，发送到共享别名的发布服务器将路由到主事件中心。  主函数应用仅侦听主事件中心。  辅助函数应用将为被动且空闲。  启动故障转移后，发送到共享别名的发布服务器现在会路由到辅助事件中心。  辅助函数应用现在将处于活动状态并开始自动触发。  有效故障转移到次要区域可以完全从事件中心驱动，并且仅当各自的事件中心处于活动状态时，函数才变为活动状态。

详细了解如何在[服务总线](../service-bus-messaging/service-bus-geo-dr.md)和[事件中心](../event-hubs/event-hubs-geo-dr.md)之间进行故障转移。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 前门](../frontdoor/quickstart-create-front-door.md)
* [事件中心故障转移注意事项](../event-hubs/event-hubs-geo-dr.md#considerations)
