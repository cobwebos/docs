---
title: Azure AD 应用程序代理的高可用性和负载平衡 |Microsoft Docs
description: 流量分布如何与应用程序代理部署一起工作。 包括有关如何优化连接器性能和对后端服务器使用负载平衡的提示。
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
ms.openlocfilehash: 014fcf37930800858cd70f15c19e3f494d3f3776
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169808"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>应用程序代理连接器和应用程序的高可用性和负载均衡

本文介绍如何使用应用程序代理部署进行流量分布。 我们将讨论：

- 如何在用户和连接器之间分配流量，以及用于优化连接器性能的提示

- 流量如何在连接器与后端应用服务器之间流动，并提供有关多个后端服务器之间负载平衡的建议

## <a name="traffic-distribution-across-connectors"></a>跨连接器的流量分布

连接器基于高可用性原则建立其连接。 不保证流量始终均匀地分布在连接器之间，并且没有会话关联。 但是，使用情况有所不同，请求会随机发送到应用程序代理服务实例。 因此，通常会在连接器之间均匀分布流量。 以下关系图和步骤演示如何在用户和连接器之间建立连接。

![显示用户和连接器之间的连接的关系图](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. 客户端设备上的用户尝试访问通过应用程序代理发布的本地应用程序。
2. 该请求将通过 Azure 负载均衡器来确定哪些应用程序代理服务实例应该获取请求。 每个区域中有数十个可接受请求的实例。 此方法有助于在服务实例之间均匀分布流量。
3. 请求将发送到[服务总线](https://docs.microsoft.com/azure/service-bus-messaging/)。
4. 服务总线检查连接以前是否使用了连接器组中的现有连接器。 如果是这样，则重新连接。 如果没有连接器与连接配对，则它会随机选择可用的连接器以向发送信号。 然后，连接器将从服务总线中选取请求。

   - 在步骤2中，请求会转向不同的应用程序代理服务实例，因此连接更有可能与不同的连接器建立连接。 因此，在组中几乎使用了连接器。

   - 仅当连接中断或出现空闲时间为10分钟时，才重新建立连接。 例如，当计算机或连接器服务重新启动或网络中断时，连接可能会断开。

5. 连接器将请求传递给应用程序的后端服务器。 然后，应用程序将响应发送回连接器。
6. 连接器通过打开与请求来自的服务实例的出站连接来完成响应。 然后，此连接将立即关闭。 默认情况下，每个连接器的并发出站连接数限制为200。
7. 然后从服务实例将响应传递回客户端。
8. 来自相同连接的后续请求将重复上述步骤，直到此连接中断或空闲10分钟。

应用程序通常具有多个资源，并在加载时打开多个连接。 如果连接以前尚未与连接器配对，则每个连接都会经历以上步骤以分配给服务实例，并选择新的可用连接器。


## <a name="best-practices-for-high-availability-of-connectors"></a>连接器高可用性最佳方案

- 由于流量在连接器之间分布以实现高可用性，因此必须始终在连接器组中至少有两个连接器。 首选三个连接器，以便在连接器之间提供附加缓冲区。 若要确定所需连接器的正确数量，请遵循容量规划文档。

- 将连接器放置在不同的出站连接上，以避免单点故障。 如果连接器使用相同的出站连接，则连接的网络问题可能会影响使用该连接的所有连接器。

- 避免在连接到生产应用程序时强制连接器重启。 这样做可能会对跨连接器的流量分布产生负面影响。 重新启动连接器会导致更多连接器不可用，并强制连接到剩余的可用连接器。 因此，最初不会使用连接器。

- 避免连接器和 Azure 之间的出站 TLS 通信的所有形式的内联检查。 这种类型的内联检查会导致通信流下降。

- 请确保为连接器保持自动更新运行。 如果应用程序代理连接器更新服务服务正在运行，则连接器会自动更新并接收最新升级的。 如果你在服务器上看不到连接器更新服务服务，则需要重新安装连接器才能获取任何更新。

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>连接器与后端应用程序服务器之间的通信流

高可用性的另一个关键方面是连接器与后端服务器之间的连接。 当通过 Azure AD 应用程序代理发布应用程序时，从用户到应用程序的流量将流经三个跃点：

1. 用户连接到 Azure 上的 Azure AD 应用程序代理服务公共终结点。 在客户端的源客户端 IP 地址（公共）和应用程序代理终结点的 IP 地址之间建立连接。
2. 应用程序代理连接器从应用程序代理服务中提取客户端的 HTTP 请求。
3. 应用程序代理连接器连接到目标应用程序。 连接器使用其自己的 IP 地址来建立连接。

![通过应用程序代理连接到应用程序的用户关系图](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X 转发-标头字段注意事项
在某些情况下（例如审核、负载平衡等），需要将外部客户端的原始 IP 地址与本地环境共享。 为了满足此要求，Azure AD 应用程序代理连接器会将具有原始客户端 IP 地址（公共）的 X 转发的标头字段添加到 HTTP 请求。 然后，相应的网络设备（负载均衡器、防火墙）或 web 服务器或后端应用程序可以读取和使用该信息。

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>多个应用服务器之间负载平衡的最佳实践
如果分配给应用程序代理应用程序的连接器组有两个或更多连接器，并且你在多个服务器（服务器场）上运行后端 web 应用程序，则需要良好的负载平衡策略。 一个好的策略可确保服务器平均获取客户端请求，并阻止服务器场中的服务器过度使用或未通过使用。
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>方案 1：后端应用程序不需要会话持久性
最简单的情况是后端 web 应用程序不需要会话粘性（会话持久性）。 来自用户的任何请求都可以由服务器场中的任何后端应用程序实例处理。 你可以使用第4层负载均衡器，并将其配置为无关联。 某些选项包括 Microsoft 网络负载平衡、Azure 负载均衡器或来自其他供应商的负载均衡器。 或者，可以配置轮循机制。
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>方案 2：后端应用程序需要会话暂留
在这种情况下，后端 web 应用程序需要在经过身份验证的会话期间进行会话粘性（会话持久性）。 所有来自用户的请求必须由在服务器场中的同一服务器上运行的后端应用程序实例处理。
此方案可能更复杂，因为客户端通常与应用程序代理服务建立多个连接。 不同连接的请求可能会在服务器场中的不同连接器和服务器上。 由于每个连接器使用其自己的 IP 地址进行此通信，因此负载均衡器无法基于连接器的 IP 地址来确保会话粘性。 源 IP 相关性不能使用。
下面是方案2的一些选项：

- 选项 1：基于负载均衡器设置的会话 cookie，建立会话持久性。 建议使用此选项，因为这样可以在后端服务器之间更均匀地分配负载。 它需要具有此功能的第7层负载均衡器，并且可以处理 HTTP 流量并终止 SSL 连接。 你可以使用 Azure 应用程序网关（会话相关性）或其他供应商提供的负载均衡器。

- 选项 2：将会话暂留在 X 转发的标头字段的基础上。 此选项需要具有此功能的第7层负载均衡器，并且可以处理 HTTP 流量并终止 SSL 连接。  

- 选项 3：将后端应用程序配置为不需要会话暂留。

请参阅你的软件供应商文档，了解后端应用程序的负载平衡要求。

## <a name="next-steps"></a>后续步骤

- [启用应用程序代理](application-proxy-add-on-premises-application.md)
- [启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
- [启用条件性访问](application-proxy-integrate-with-sharepoint-server.md)
- [解决使用应用程序代理时遇到的问题](application-proxy-troubleshoot.md)
- [了解 Azure AD 体系结构如何支持高可用性](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
