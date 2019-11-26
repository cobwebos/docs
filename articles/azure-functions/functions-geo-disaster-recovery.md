---
title: Azure Functions 异地灾难恢复和高可用性
description: 如何使用地理区域进行冗余并在 Azure Functions 中进行故障转移。
author: wesmc7777
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: jehollan
ms.openlocfilehash: db072d90c39b3856127925306cb1407c5837a0bb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226976"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions 异地灾难恢复

在整个区域或数据中心遭遇停机的情况下，在不同区域进行计算以实现连续处理就显得至关重要。  本文将介绍一些可以用来部署函数以实现灾难恢复的策略。

## <a name="basic-concepts"></a>基本概念

Azure Functions 在特定区域运行。  若要提高可用性，可将相同函数部署到多个区域。  在多个区域中时，可以让函数以“主动/主动”或“主动/被动”模式运行。  

* 主动/主动。 两个区域都主动接收事件（采用重复或循环方式）。 建议将“主动/主动”用于 HTTPS 函数，与 Azure Front Door 组合使用。
* 主动/被动。 一个区域主动接收事件，另一个区域（次要区域）处于空闲状态。  需要进行故障转移时，次要区域激活，开始接管处理操作。  建议将它用于非 HTTP 功能，例如服务总线和事件中心。

有关多区域部署的详细信息，请参阅如何[在多个区域中运行应用](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

## <a name="activeactive-for-https-functions"></a>HTTPS 函数的主动/主动

若要实现函数的主动/主动部署，需要在两个区域之间协调事件的某些组件。  对于 HTTPS 函数，此协调使用[Azure 前门](../frontdoor/front-door-overview.md)完成。  Azure 前门可以在多个区域函数之间路由和轮循 HTTPS 请求。  它还会定期检查每个终结点的运行状况。  如果某个区域函数停止了对运行状况检查的响应，则 Azure 前门会将其从轮换中取出，只将流量转发到正常运行的功能。  

![Azure 前门和功能的体系结构](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>用于非 HTTPS 功能的“主动/主动”模式

仍可针对非 HTTPS 功能实现“主动/主动”部署。  但是，需要考虑这两个区域如何互相交互或协调。  如果向两个区域部署了同一函数应用，每个区域都在同一服务总线队列上触发，那么在对该队列执行取消排队操作时，这两个区域属于竞争性使用者。  虽然这意味着每条消息仅由多个实例中的一个进行处理，但也意味着在单个服务总线上仍然存在单点故障。  如果部署两个服务总线队列（一个在主要区域，一个在次要区域），这两个函数应用指向其区域队列，则现在的难点在于，如何在这两个区域之间分发队列消息。  通常情况下，这意味着每个发布者都会尝试将消息发布到两个区域，每条消息都由两个活动的函数应用进行处理。  虽然这样会形成一个“主动/主动”模式，但也会在复制计算以及在何时合并数据或以何种方式合并数据方面制造其他难题。  出于这些原因，建议由非 HTTPS 触发器来使用“主动/主动”模式。

## <a name="activepassive-for-non-https-functions"></a>用于非 HTTPS 功能的“主动/被动”模式

使用“主动/被动”模式时，每次只有一个函数处理一条消息，但在发生灾难时可以故障转移到次要区域。  Azure Functions 与[Azure 服务总线异地恢复](../service-bus-messaging/service-bus-geo-dr.md)和[azure 事件中心地域恢复](../event-hubs/event-hubs-geo-dr.md)一起工作。

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
