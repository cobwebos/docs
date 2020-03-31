---
title: 故障排除连接 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 了解如何解决与 Azure 数据库的连接问题，以便发布后 SQL - 超大规模 （Citus）
keywords: postgresql 连接, 连接字符串, 连接问题, 暂时性错误, 连接错误
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977499"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>故障连接问题到 Azure 数据库后SQL - 超大规模 （Citus）

连接问题可能由以下几项原因引起：

* 防火墙设置
* 连接超时
* 登录信息不正确
* 服务器组达到的连接限制
* 服务的基础结构出现问题
* 维修维护
* 协调器节点故障转移到新硬件

通常，与超大规模的连接问题可以分类如下：

* 暂时性错误（短暂或间歇性）
* 持久或非暂时性错误（定期重复发生的错误）

## <a name="troubleshoot-transient-errors"></a>对暂时性错误进行故障排除

瞬态错误的发生原因有很多。 最常见的包括系统维护、硬件或软件错误以及协调节点 vCore 升级。

为超大规模服务器组节点启用高可用性可以自动缓解这些类型的问题。 但是，您的应用程序仍应准备暂时失去其连接。 此外，其他事件可能需要更长时间才能缓解，例如大型事务导致长时间恢复时。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解决暂时性连接问题的步骤

1. 检查[Microsoft Azure 服务仪表板](https://azure.microsoft.com/status)，了解在应用程序报告错误期间发生的任何已知中断。
2. 连接到云服务（如超大规模 （Citus） 的应用程序应预期瞬态错误并做出正常响应。 例如，应用程序应实现重试逻辑来处理这些错误，而不是将它们作为应用程序错误呈现给用户。
3. 当服务器组接近其资源限制时，错误可能看起来像是暂时连接问题。 增加节点 RAM 或添加辅助节点和重新平衡数据可能会有所帮助。
4. 如果连接问题持续或持续超过 60 秒，或每天发生多次，请通过在[Azure 支持](https://azure.microsoft.com/support/options)站点上选择 **"获取支持**"来提交 Azure 支持请求。

## <a name="troubleshoot-persistent-errors"></a>排查一再出现的错误

如果应用程序持续无法连接到 Hyperscale （Citus），最常见的原因是防火墙配置错误或用户错误。

* 协调节点防火墙配置：确保超大规模服务器防火墙配置为允许来自客户端的连接，包括代理服务器和网关。
* 客户端防火墙配置：客户端上的防火墙必须允许连接到数据库服务器。 某些防火墙不仅要求允许按名称的应用程序，而且允许服务器的 IP 地址和端口。
* 用户错误：仔细检查连接字符串。 您可能键入了服务器名称等参数。 您可以在 Azure 门户中找到各种语言框架和 psql 的连接字符串。 转到超大规模 （Citus） 服务器组中的连接**字符串**页面。 还要记住，超大规模（Citus）集群只有一个数据库，其预定义的名称是**citus。**

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解决永久性连接问题的步骤

1. 设置[防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)以允许客户端 IP 地址。 （仅出于临时测试目的）使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束 IP 地址，来设置一个防火墙规则。 该规则将服务器打开到所有 IP 地址。 如果规则解决了连接问题，请将其删除，并为适当受限的 IP 地址或地址范围创建防火墙规则。
2. 在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 5432。
3. 验证连接字符串和其他连接设置。
4. 在仪表板中检查服务运行状况。

## <a name="next-steps"></a>后续步骤

* 在 Azure[数据库中了解防火墙规则的概念，用于后格雷SQL - 超大规模 （Citus）](concepts-hyperscale-firewall-rules.md)
* 了解如何管理[Azure 数据库的防火墙规则，适用于后格雷SQL - 超大规模 （Citus）](howto-hyperscale-manage-firewall-using-portal.md)
