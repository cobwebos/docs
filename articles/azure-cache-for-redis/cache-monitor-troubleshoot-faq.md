---
title: 用于 Redis 监视和故障排除常见问题的 Azure 缓存
description: 了解一些常见问题的答案，这些问题可帮助你监视 Azure Cache for Redis 并对其进行故障排除
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 2149d069ce9cb0b636e461d19963f413b162bc9d
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010828"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>用于 Redis 监视和故障排除常见问题的 Azure 缓存
本文提供了有关如何监视 Azure Cache for Redis 并对其进行故障排除的常见问题的解答。

## <a name="common-questions-and-answers"></a>常见问题和解答
本部分包含以下 Faq：

* [如何监视缓存的运行状况和性能？](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [为何会出现超时？](#why-am-i-seeing-timeouts)
* [客户端为何与缓存断开连接？](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>如何监视缓存的运行状况和性能？
可以在 [Azure 门户](https://portal.azure.com)中监视 Microsof Azure Redis 缓存实例。 可以查看度量值、将度量值图表固定到启动面板、自定义监视图表的日期和时间范围、在图表中添加和删除度量值，以及设置符合特定条件时发出的警报。 有关详细信息，请参阅[监视 Azure Redis 缓存](cache-how-to-monitor.md)。

Azure Redis 缓存**资源菜单**中还包含了用于对缓存进行监视和故障排除的几个工具。

* “诊断并解决问题”提供常见问题的相关信息，以及解决问题的策略。
* “资源运行状况”会监视资源，并告知资源是否按预期运行。 有关 Azure 资源运行状况服务的详细信息，请参阅 [Azure 资源运行状况概述](../resource-health/resource-health-overview.md)。
* “新建支持请求”提供用于建立缓存支持请求的选项。

借助这些工具，可以监视 Azure Redis 缓存实例的运行状况，以及管理缓存应用程序。 有关详细信息，请参阅[如何配置 Azure Redis 缓存](cache-configure.md)的“支持和故障排除设置”部分。

### <a name="why-am-i-seeing-timeouts"></a>为何会出现超时？
超时发生在用来与 Redis 通信的客户端中。 将某个命令发送到 Redis 服务器后，该命令会排队，Redis 服务器最终会提取该命令并执行它。 但是，客户端在此过程中可能会超时，在这种情况下，会在调用端引发异常。 有关排查超时问题的详细信息，请参阅[客户端故障排除](cache-troubleshoot-client.md)和 [StackExchange.Redis 超时异常](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)。

### <a name="why-was-my-client-disconnected-from-the-cache"></a>客户端为何与缓存断开连接？
下面是缓存断开连接的一些常见原因。

* 客户端的原因
  * 已重新部署客户端应用程序。
  * 客户端应用程序执行了缩放操作。
    * 对于云服务或 Web 应用，原因可能在于自动缩放。
  * 客户端上的网络层已更改。
  * 客户端中或客户端与服务器之间的网络节点中发生暂时性错误。
  * 已达到带宽阈值限制。
  * 占用大量 CPU 的操作花费了太长时间才完成。
* 服务器端的原因
  * 在标准缓存产品上，用于 Redis 的 Azure Cache 服务启动了从主节点到副本节点的故障转移。
  * Azure 正在修补已部署缓存的实例
    * 原因可能是 Redis 服务器更新或常规 VM 维护。


## <a name="next-steps"></a>后续步骤

有关 Azure Cache for Redis 实例监视和故障排除的详细信息，请参阅[如何监视 Azure Cache for Redis](cache-how-to-monitor.md) 和各种故障排除指南。

了解[适用于 Redis 的其他 Azure 缓存的常见问题](cache-faq.md)。
