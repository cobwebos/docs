---
title: 解决 Azure 应用服务中的间歇性出站连接错误
description: 解决 Azure 应用服务中的间歇性连接错误和相关性能问题
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367546"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>解决 Azure 应用服务中的间歇性出站连接错误

本文可帮助您解决[Azure 应用服务](https://docs.microsoft.com/azure/app-service/overview)中的间歇性连接错误和相关性能问题。 本主题将提供有关源地址网络转换 （SNAT） 端口耗尽的详细信息和故障排除方法。 如果本文中的任何一点都需要更多帮助，请与[MSDN Azure 和堆栈溢出论坛中的](https://azure.microsoft.com/support/forums/)Azure 专家联系。 或者，提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。

## <a name="symptoms"></a>症状

Azure 应用服务上托管的应用程序和函数可能会表现出以下一个或多个症状：

* 服务计划中所有或某些实例的响应时间变慢。
* 间歇性 5xx 或**错误网关**错误
* 超时错误消息
* 无法连接到外部终结点（如 SQLDB、服务结构、其他应用服务等）

## <a name="cause"></a>原因

这些症状的一个主要原因是应用程序实例无法打开到外部终结点的新连接，因为它已达到以下限制之一：

* TCP 连接：可以进行的出站连接数有限制。 这与所使用的工作人员的大小相关联。
* SNAT 端口：如[Azure 中的出站连接中](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)所述，Azure 使用源网络地址转换 （SNAT） 和负载均衡器（不向客户公开）在公共 IP 地址空间中与 Azure 外部的终结点进行通信。 Azure 应用服务中的每个实例最初都会获得预分配的**128**个 SNAT 端口数。 该限制会影响打开连接到同一主机和端口组合的连接。 如果应用创建到地址和端口组合的连接，则不会占用 SNAT 端口。 重复调用同一地址和端口组合时，SNAT 端口将用用。 释放某个端口以后，即可根据需要重复使用该端口。 Azure 网络负载均衡器仅在等待 4 分钟后从已关闭的连接中回收 SNAT 端口。

当应用程序或函数快速打开新连接时，它们会快速耗尽其预分配的 128 个端口配额。 然后，通过动态分配其他 SNAT 端口或重新回收的 SNAT 端口，阻止它们，直到新的 SNAT 端口可用。 由于无法创建新连接而被阻止的应用程序或函数将开始遇到本文"**症状**"部分中描述的一个或多个问题。

## <a name="avoiding-the-problem"></a>避免问题

避免 SNAT 端口问题意味着避免重复创建与同一主机和端口的新连接。

Azure 文档**的出站连接**[的问题解决部分](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving)讨论了缓解 SNAT 端口耗尽的一般策略。 在这些策略中，以下策略适用于 Azure 应用服务上托管的应用和功能。

### <a name="modify-the-application-to-use-connection-pooling"></a>修改应用程序以使用连接池

* 要池 HTTP 连接，请查看[使用 HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)的池 HTTP 连接。
* 有关 SQL Server 连接池的信息，请查看[SQL 服务器连接池 （ADO.NET）](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)。
* 要实现与实体框架应用程序池，请查看[DbContext 池](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)。

下面是用于通过不同解决方案堆栈实现连接池的链接集合。

#### <a name="node"></a>节点

默认情况下，NodeJS 的连接不会保持活动状态。 下面是用于连接池的热门数据库和包，其中包含如何实现它们的示例。

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP 保持活动状态

* [代理保存](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 文档](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

下面是用于 JDBC 连接池的热门库，其中包含如何实现它们的示例：JDBC 连接池。

* [汤姆卡特 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [希卡里CP](https://github.com/brettwooldridge/HikariCP)
* [阿帕奇 DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 连接池

* [阿帕奇连接管理](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [类池Http客户端连接管理器](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

尽管 PHP 不支持连接池，但您可以尝试使用与后端服务器的持久数据库连接。
 
* MySQL 服务器

   * 较新版本的[MySQLi 连接](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)旧版本的 PHP

* 其他数据源

   * [PHP连接管理](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL 服务器](https://github.com/tediousjs/node-mssql#connection-pools)（注意：SQLAlchemy 可用于除 MicrosoftSQL Server 之外的其他数据库）
* [HTTP 保持活动](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)状态（使用[会话对象](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)时，保持活动是自动的）。

对于其他环境，请查看提供程序或特定于驱动程序的文档，以便在应用程序中实现连接池。

### <a name="modify-the-application-to-reuse-connections"></a>修改应用程序以重复使用连接

*  有关在 Azure 函数中管理连接的其他指针和示例，请查看[Azure 函数中的管理连接](https://docs.microsoft.com/azure/azure-functions/manage-connections)。

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>修改应用程序以使用主动性较低的重试逻辑

* 有关其他指导和示例，请查看[重试模式](https://docs.microsoft.com/azure/architecture/patterns/retry)。

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>使用 keepalive 重置出站空闲超时

* 要实现 Node.js 应用的保持计划，请查看[我的节点应用程序正在进行过多的出站调用](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)。

### <a name="additional-guidance-specific-to-app-service"></a>特定于应用服务的其他指南：

* [负载测试](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test)应以稳定的馈送速度模拟真实世界数据。 在真实压力下测试应用和功能可以提前识别和解决 SNAT 端口耗尽问题。
* 确保后端服务能够快速返回响应。 要解决 Azure SQL 数据库的性能问题，请查看[使用智能见解解决 Azure SQL 数据库性能问题](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)。
* 将应用服务计划扩展到更多实例。 有关缩放的详细信息，请参阅[缩放 Azure 应用服务中的应用](https://docs.microsoft.com/azure/app-service/manage-scale-up)。 应用服务计划中的每个辅助角色实例都分配了多个 SNAT 端口。 如果将使用情况分散到更多实例中，则每个实例的 SNAT 端口使用量可能会低于建议限制的 100 个出站连接，每个唯一的远程终结点。
* 请考虑迁移到[应用服务环境 （ASE），](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)其中分配给您单个出站 IP 地址，并且连接和 SNAT 端口的限制要高得多。

避免出站 TCP 限制更容易解决，因为限制由工作人员的大小设置。 您可以在[沙盒交叉 VM 数字限制 - TCP 连接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)中看到限制

|限制名称|描述|小 （A1）|中等 （A2）|大 （A3）|隔离层 （ASE）|
|---|---|---|---|---|---|
|连接|跨整个 VM 的连接数|1920|3968|8064|16,000|

为了避免出站 TCP 限制，可以增加辅助对象的大小，也可以横向横向横向横向扩展。

## <a name="troubleshooting"></a>疑难解答

了解两种类型的出站连接限制以及你的应用的作用，应该更容易排除故障。 如果您知道你的应用对同一存储帐户进行多次调用，您可能会怀疑 SNAT 限制。 如果你的应用在互联网上创建了大量对终结点的调用，你会怀疑您达到了 VM 限制。

如果您没有足够的应用程序行为来快速确定原因，应用服务中可以使用一些工具和技术来帮助确定。

### <a name="find-snat-port-allocation-information"></a>查找 SNAT 端口分配信息

您可以使用[应用服务诊断](https://docs.microsoft.com/azure/app-service/overview-diagnostics)查找 SNAT 端口分配信息，并遵守应用服务站点的 SNAT 端口分配指标。 要查找 SNAT 端口分配信息，请按照以下步骤操作：

1. 要访问应用服务诊断，请导航到[Azure 门户](https://portal.azure.com/)中的应用服务 Web 应用或应用服务环境。 在左侧导航中，选择 **"诊断并解决问题**"。
2. 选择可用性和性能类别
3. 在类别下的可用磁贴列表中选择 SNAT 端口耗尽磁贴。 其做法是将其保持在 128 以下。
如果您确实需要它，您仍然可以打开支持票证，支持工程师将从后端为您获取指标。

请注意，由于 SNAT 端口使用情况不能作为指标提供，因此无法基于 SNAT 端口使用情况自动缩放，也不能根据 SNAT 端口分配指标配置自动缩放。

### <a name="tcp-connections-and-snat-ports"></a>TCP 连接和 SNAT 端口

TCP 连接和 SNAT 端口没有直接关系。 任何应用服务网站的"诊断和解决问题"边栏选项卡中都包含 TCP 连接使用检测器。 搜索短语"TCP 连接"以查找它。

* SNAT 端口仅用于外部网络流，而总 TCP 连接包括本地回环连接。
* 如果流在协议、IP 地址或端口中不同，则可以由不同的流共享 SNAT 端口。 TCP 连接指标对每个 TCP 连接进行计数。
* TCP 连接限制发生在辅助角色实例级别。 Azure 网络出站负载平衡不使用 TCP 连接指标进行 SNAT 端口限制。
* TCP 连接限制在[沙盒交叉 VM 数字限制 - TCP 连接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)中描述

|限制名称|描述|小 （A1）|中等 （A2）|大 （A3）|隔离层 （ASE）|
|---|---|---|---|---|---|
|连接|跨整个 VM 的连接数|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>Web 作业和数据库连接
 
如果 SNAT 端口已耗尽，Web 作业无法连接到 Azure SQL 数据库，则没有指标显示每个单独的 Web 应用程序进程打开的连接数。 要查找有问题的 WebJob，将多个 Web 作业移出另一个应用服务计划，以查看情况是否有所改善，或者其中一个计划中是否仍然存在问题。 重复此过程，直到发现有问题的 WebJob。

### <a name="using-snat-ports-sooner"></a>更快地使用 SNAT 端口

您不能更改任何 Azure 设置以更快地释放已使用的 SNAT 端口，因为所有 SNAT 端口都将根据以下条件发布，并且行为是设计。
 
* 如果服务器或客户端发送[FINACK，SNAT 端口将在](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release)240 秒后释放。
* 如果看到 RST，SNAT 端口将在 15 秒后释放。
* 如果已达到空闲超时，则释放端口。
 
## <a name="additional-information"></a>其他信息

* [具有应用服务的 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [排查 Azure 应用服务中应用性能缓慢的问题](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
