---
title: 排查连接问题-超大规模（Citus）-Azure Database for PostgreSQL
description: 了解如何对 Azure Database for PostgreSQL-超大规模（Citus）的连接问题进行故障排除
keywords: postgresql 连接, 连接字符串, 连接问题, 暂时性错误, 连接错误
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: abcc979bb36fe0acb663a511875a1e186c2c0739
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583966"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>排查 Azure Database for PostgreSQL 的连接问题-超大规模（Citus）

连接问题可能由多种因素引起，例如：

* 防火墙设置
* 连接超时
* 不正确的登录信息
* 已达到服务器组的连接限制
* 服务的基础结构出现问题
* 服务维护
* 协调器节点故障转移到新硬件

通常，超大规模的连接问题可按如下方式分类：

* 暂时性错误（短暂或间歇性）
* 持久或非暂时性错误（定期重复发生的错误）

## <a name="troubleshoot-transient-errors"></a>对暂时性错误进行故障排除

出现暂时性错误的原因有多种。 最常见的包括系统维护、硬件或软件错误以及协调器节点 vCore 升级。

为超大规模服务器组节点启用高可用性可以自动缓解这些类型的问题。 但是，您的应用程序仍应准备好暂时失去其连接。 其他事件也可能需要更长的时间来缓解，例如当大型事务导致长时间运行的恢复时。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解决暂时性连接问题的步骤

1. 检查 " [Microsoft Azure 服务" 仪表板](https://azure.microsoft.com/status)，以了解在应用程序报告错误的时间内发生的任何已知的中断。
2. 连接到云服务的应用程序（例如超大规模（Citus））应会出现暂时性错误，并正常做出反应。 例如，应用程序应实现重试逻辑来处理这些错误，而不是将它们作为应用程序错误呈现给用户。
3. 服务器组接近其资源限制，因此错误看起来就像暂时性连接问题。 增加节点 RAM 或添加辅助节点和重新平衡数据可能会有所帮助。
4. 如果连接问题继续存在，或者持续时间超过60秒，或者每天发生一次以上，请通过在[Azure 支持](https://azure.microsoft.com/support/options)站点上选择 "**获取支持**" 来提供 azure 支持请求。

## <a name="troubleshoot-persistent-errors"></a>排查一再出现的错误

如果应用程序持久无法连接到超大规模（Citus），最常见的原因是防火墙配置错误或用户错误。

* 协调器节点防火墙配置：确保超大规模服务器防火墙已配置为允许来自客户端（包括代理服务器和网关）的连接。
* 客户端防火墙配置：客户端的防火墙必须允许连接到数据库服务器。 某些防火墙要求仅允许名称使用应用程序，但允许服务器的 IP 地址和端口。
* 用户错误：仔细检查连接字符串。 您可能键入了错误的参数，如服务器名称。 可以在 Azure 门户中找到各种语言框架和 psql 的连接字符串。 请在超大规模（Citus）服务器组中转到 "**连接字符串**" 页。 另外请记住，超大规模（Citus）群集只具有一个数据库，其预定义名称为**Citus**。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解决永久性连接问题的步骤

1. 设置[防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)以允许客户端 IP 地址。 （仅出于临时测试目的）使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束 IP 地址，来设置一个防火墙规则。 该规则将为所有 IP 地址打开服务器。 如果规则解决了连接问题，请将其删除，并为适当限制的 IP 地址或地址范围创建防火墙规则。
2. 在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 5432。
3. 验证连接字符串和其他连接设置。
4. 在仪表板中检查服务运行状况。

## <a name="next-steps"></a>后续步骤

* 了解[Azure Database for PostgreSQL-超大规模（Citus）中防火墙规则](concepts-hyperscale-firewall-rules.md)的概念
* 请参阅如何[管理 Azure Database for PostgreSQL 的防火墙规则-超大规模（Citus）](howto-hyperscale-manage-firewall-using-portal.md)
