---
title: 高可用性和负载平衡 - Azure AD 应用程序代理
description: 流量分发如何与应用程序代理部署配合使用。 包括有关如何优化连接器性能和为后端服务器使用负载平衡的提示。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3202c2fbfedfce0b0b52be94b1e0d165a6e72546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481307"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>应用程序代理连接器和应用程序的高可用性和负载平衡

本文介绍了流量分发如何与应用程序代理部署一起工作。 我们将讨论：

- 流量如何在用户和连接器之间分配，以及优化连接器性能的提示

- 连接器和后端应用服务器之间的流量如何流动，以及多个后端服务器之间负载平衡的建议

## <a name="traffic-distribution-across-connectors"></a>连接器之间的流量分布

连接器基于高可用性原则建立其连接。 不能保证流量始终在连接器之间均匀分布，并且没有会话相关性。 但是，使用情况各不相同，请求会随机发送到应用程序代理服务实例。 因此，流量通常几乎均匀地分布在连接器上。 下图和步骤说明了如何在用户和连接器之间建立连接。

![显示用户和连接器之间连接的图表](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. 客户端设备上的用户尝试访问通过应用程序代理发布的本地应用程序。
2. 请求通过 Azure 负载均衡器来确定哪个应用程序代理服务实例应接受该请求。 每个区域都有数十个实例可用于接受请求。 此方法有助于跨服务实例均匀分配流量。
3. 请求发送到[服务总线](https://docs.microsoft.com/azure/service-bus-messaging/)。
4. 维修总线检查连接以前是否使用了连接器组中的现有连接器。 如果是这样，它将重用连接。 如果尚未将连接器与连接配对，它将随机选择可用连接器以向其发出信号。 然后，连接器从服务总线接收请求。

   - 在步骤 2 中，请求转到不同的应用程序代理服务实例，因此更有可能使用不同的连接器进行连接。 因此，连接器在组中几乎均匀使用。

   - 仅当连接断开或出现 10 分钟的空闲时间时，才会重新建立连接。 例如，当计算机或连接器服务重新启动或网络中断时，连接可能会中断。

5. 连接器将请求传递到应用程序的后端服务器。 然后，应用程序将响应发送回连接器。
6. 连接器通过打开到请求来自的服务实例的出站连接来完成响应。 然后，此连接立即关闭。 默认情况下，每个连接器限制为 200 个并发出站连接。
7. 然后，响应从服务实例传回客户端。
8. 来自同一连接的后续请求重复上述步骤，直到此连接断开或空闲 10 分钟。

应用程序通常具有许多资源，并在加载时打开多个连接。 每个连接都经过上述步骤，以分配给服务实例，如果连接以前尚未与连接器配对，请选择新的可用连接器。


## <a name="best-practices-for-high-availability-of-connectors"></a>连接器高可用性的最佳做法

- 由于流量在连接器之间分配的方式，因此连接器组中始终至少有两个连接器至关重要。 三个连接器优先在连接器之间提供额外的缓冲。 要确定所需的连接器的正确数量，请遵循容量规划文档。

- 将连接器放在不同的出站连接上，以避免单点故障。 如果连接器使用相同的出站连接，则连接的网络问题可能会影响使用该连接的所有连接器。

- 避免在连接到生产应用程序时强制连接器重新启动。 这样做可能会对连接器之间的流量分布产生负面影响。 重新启动连接器会导致更多连接器不可用，并强制连接到其余可用连接器。 结果是连接器最初使用不均匀。

- 避免对连接器和 Azure 之间的出站 TLS 通信进行各种形式的内联检查。 这种类型的内联检查会导致通信流下降。

- 确保保持连接器的自动更新运行。 如果应用程序代理连接器更新程序服务正在运行，则连接器将自动更新并接收最新的升级。 如果在服务器上未看到连接器更新程序服务，需要重新安装连接器才能获得所有更新。

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>连接器和后端应用程序服务器之间的流量

高可用性是一个因素的另一个关键领域是连接器和后端服务器之间的连接。 当应用程序通过 Azure AD 应用程序代理发布时，从用户到应用程序的流量通过三个跃点流：

1. 用户连接到 Azure 上的 Azure AD 应用程序代理服务公共终结点。 在客户端的原始客户端 IP 地址（公共）和应用程序代理终结点的 IP 地址之间建立连接。
2. 应用程序代理连接器从应用程序代理服务中拉出客户端的 HTTP 请求。
3. 应用程序代理连接器连接到目标应用程序。 连接器使用自己的 IP 地址建立连接。

![通过应用程序代理连接到应用程序的用户图](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-前行-用于标头字段注意事项
在某些情况下（如审核、负载平衡等），需要与本地环境共享外部客户端的原始 IP 地址。 为了满足这一要求，Azure AD 应用程序代理连接器将具有原始客户端 IP 地址（公共）的 X-前转-for 标头字段添加到 HTTP 请求中。 然后，相应的网络设备（负载均衡器、防火墙）或 Web 服务器或后端应用程序可以读取和使用信息。

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>多个应用服务器之间负载平衡的最佳做法
当分配给应用程序代理应用程序的连接器组具有两个或多个连接器，并且您在多个服务器（服务器场）上运行后端 Web 应用程序时，需要良好的负载平衡策略。 一个好的策略可确保服务器均匀地接收客户端请求，并防止服务器场中服务器过度使用或利用率低。
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>方案 1：后端应用程序不需要会话持久性
最简单的方案是后端 Web 应用程序不需要会话粘性（会话持久性）。 来自用户的任何请求都可以由服务器场中的任何后端应用程序实例处理。 您可以使用第 4 层负载均衡器，并在没有相关性下对其进行配置。 某些选项包括 Microsoft 网络负载平衡和 Azure 负载均衡器或来自其他供应商的负载均衡器。 或者，可以配置循环 DNS。
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>方案 2：后端应用程序需要会话持久性
在这种情况下，后端 Web 应用程序需要在经过身份验证的会话期间会话粘性（会话持久性）。 来自用户的所有请求必须由在服务器场中的同一服务器上运行的后端应用程序实例处理。
此方案可能更为复杂，因为客户端通常建立到应用程序代理服务的多个连接。 不同连接的请求可能会到达服务器场中不同的连接器和服务器。 由于每个连接器都使用自己的 IP 地址进行此通信，因此负载均衡器无法根据连接器的 IP 地址确保会话粘性。 源 IP 关联也无法使用。
以下是方案 2 的一些选项：

- 选项 1：将会话持久性基于负载均衡器设置的会话 Cookie。 建议使用此选项，因为它允许负载在后端服务器之间更均匀地分布。 它需要具有此功能的第 7 层负载均衡器，该平衡器可以处理 HTTP 流量并终止 TLS 连接。 您可以使用 Azure 应用程序网关（会话关联）或其他供应商的负载均衡器。

- 选项 2：将会话持久性基于 X-前行-for 标头字段。 此选项需要具有此功能的第 7 层负载均衡器，该平衡器可以处理 HTTP 流量并终止 TLS 连接。  

- 选项 3：将后端应用程序配置为不需要会话持久性。

请参阅软件供应商的文档，了解后端应用程序的负载平衡要求。

## <a name="next-steps"></a>后续步骤

- [启用应用程序代理](application-proxy-add-on-premises-application.md)
- [启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
- [启用条件访问](application-proxy-integrate-with-sharepoint-server.md)
- [解决使用应用程序代理时遇到的问题](application-proxy-troubleshoot.md)
- [了解 Azure AD 体系结构如何支持高可用性](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
