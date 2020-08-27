---
title: 排查 Azure 应用服务中的间歇性出站连接错误
description: 排查 Azure 应用服务中的间歇性连接错误和相关性能问题
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 467f7b3525883e16e57a06ff97cf4fd386279d22
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958229"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>排查 Azure 应用服务中的间歇性出站连接错误

本文帮助你排查 [Azure 应用服务](./overview.md)中的间歇性连接错误和相关性能问题。 本主题将提供有关源地址网络转换 (SNAT) 端口耗尽的详细信息及其故障排除方法。 如果在本文中有任何需要帮助的地方，请联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 

## <a name="symptoms"></a>症状

托管在 Azure 应用服务中的应用程序和功能可能会出现以下一种或多种症状：

* 服务计划中的所有或部分实例的响应速度缓慢。
* 间歇性 5xx 错误或“错误的网关”错误 
* 超时错误消息
* 无法连接到外部终结点（例如 SQLDB、Service Fabric、其他应用服务等）

## <a name="cause"></a>原因

出现这种症状的主要原因是，应用程序实例已达到以下限制之一，因此无法与外部终结点打开新的连接：

* TCP 连接数：可以建立的出站连接数有限制。 此数字与所用辅助角色的大小相关联。
* SNAT 端口数：如 [Azure中的出站连接](../load-balancer/load-balancer-outbound-connections.md)中所述，Azure 使用源网络地址转换 (SNAT) 和负载均衡器（不向客户公开）在公共 IP 地址空间中与 Azure 外部的终结点通信。 最初为 Azure 应用服务中的每个实例预分配了 128 个 SNAT 端口。  该限制会影响与同一个主机/端口组合打开的连接数。 如果应用与混合的地址/端口组合建立了连接，则不会用尽 SNAT 端口。 重复调用同一个地址/端口组合时，会用尽 SNAT 端口。 释放某个端口以后，即可根据需要重复使用该端口。 只有在等待 4 分钟后，Azure 网络负载均衡器才会从关闭的连接回收 SNAT 端口。

当应用程序或功能快速打开新的连接时，它们可能很快就会耗尽预分配的配额（128 个端口）。 然后，应用程序或功能会一直受到阻止，直到通过动态分配额外的 SNAT 端口或者通过重复使用回收的 SNAT 端口提供了新的 SNAT 端口为止。 由于无法创建新连接而被阻止的应用程序或功能将开始遇到本文的“症状”部分所述的一种或多种问题。 

## <a name="avoiding-the-problem"></a>避免问题

如果你的目标是一个支持服务终结点的 Azure 服务，则可通过使用[区域 VNet 集成](./web-sites-integrate-with-vnet.md)和服务终结点或专用终结点来避免 SNAT 端口耗尽问题。 使用区域 VNet 集成并将服务终结点置于集成子网中时，发往这些服务的应用出站流量不会有出站 SNAT 端口限制。 同样，如果使用区域 VNet 集成和专用终结点，则不会有将流量发往该目标的出站 SNAT 端口的问题。 

避免 SNAT 端口问题意味着需要避免对同一主机和端口反复创建新连接。

“Azure 的出站连接”文档的[解决问题部分](../load-balancer/load-balancer-outbound-connections.md)介绍了缓解 SNAT 端口耗尽问题的一般策略。 这些策略中的以下策略适用于托管在 Azure 应用服务中的应用和功能。

### <a name="modify-the-application-to-use-connection-pooling"></a>修改应用程序以使用连接池

* 对于池 HTTP 连接，请查看[使用 HttpClientFactory 的池 HTTP 连接](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)。
* 有关 SQL Server 连接池的信息，请查看 [SQL Server 连接池 (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)。
* 若要对实体框架应用程序实现池，请查看 [DbContext 池](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)。

下面是有关通过不同的解决方案堆栈实现连接池的链接集合。

#### <a name="node"></a>节点

默认情况下，NodeJS 的连接不会保持活动状态。 下面是适用于连接池的常用数据库和包，其中包含有关如何实现它们的示例。

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP 保持活动状态

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 文档](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

下面是用于 JDBC 连接池的常用库，其中包含有关如何实现它们的示例：JDBC 连接池。

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 连接池

* [Apache 连接管理](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [类 PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

尽管 PHP 不支持连接池，但你可以尝试使用到后端服务器的持久数据库连接。
 
* MySQL 服务器

   * 适用于较新版本的 [MySQLi 连接](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * 适用于较旧 PHP 版本的 [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* 其他数据源

   * [PHP 连接管理](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)（注意：除 Microsoft SQL Server 以外，SQLAlchemy 还可用于其他数据库）
* [HTTP Keep-Alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)（使用会话 [session-objects](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive) 时，Keep-Alive 是自动的）。

对于其他环境，请查看有关在应用程序中实现连接池的特定于提供商或驱动程序的文档。

### <a name="modify-the-application-to-reuse-connections"></a>修改应用程序以重复使用连接

*  有关在 Azure Functions 中管理连接的其他指导和示例，请查看[在 Azure Functions 中管理连接](../azure-functions/manage-connections.md)。

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>修改应用程序以使用主动性较低的重试逻辑

* 有关其他指导和示例，请查看 [重试模式](/azure/architecture/patterns/retry)。

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>使用 keepalive 重置出站空闲超时

* 若要实现 Node.js 应用的 Keep-Alive，请查看[我的 Node 应用程序正在发出过多的出站调用](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls)。

### <a name="additional-guidance-specific-to-app-service"></a>特定于应用服务的其他指导：

* [负载测试](/azure/devops/test/load-test/app-service-web-app-performance-test)应以稳定的供电速度模拟实际数据。 在真实环境中测试应用和功能可以提前识别和解决 SNAT 端口耗尽问题。
* 确保后端服务可以快速返回响应。 若要排查 Azure SQL 数据库的性能问题，请查看[使用智能见解排查 Azure SQL 数据库性能问题](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)。
* 将应用服务计划横向扩展为更多实例。 有关缩放的详细信息，请参阅[缩放 Azure 应用服务中的应用](./manage-scale-up.md)。 应用服务计划中的每个辅助角色实例分配有多个 SNAT 端口。 如果将用量分散在多个实例之间，可能会使每个实例的 SNAT 端口用量不超过每个独特远程终结点的建议出站连接数限制（100 个）。
* 考虑转移到[应用服务环境 (ASE)](./environment/using-an-ase.md)，其中分配了单个出站 IP 地址，且连接数和 SNAT 端口数限制要高得多。 在 ASE 中，每个实例的 SNAT 端口数基于 [Azure 负载平衡器预先分配表](../load-balancer/load-balancer-outbound-connections.md#snatporttable) （例如，含1-50 个工作线程的 ase）每个实例有1024个预先分配的端口，而具有51-100 辅助角色实例的 ase 的每个实例都有512个预分配端口。

避免超过出站 TCP 限制要更容易一些，因为这些限制是按辅助角色的大小设置的。 可以在[沙盒跨 VM 数字限制 - TCP 连接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)中查看限制

|限制名称|描述|小型 (A1)|中型 (A2)|大型 (A3)|隔离层 (ASE)|
|---|---|---|---|---|---|
|连接|整个 VM 的连接数|1920|3968|8064|16,000|

若要避免超过出站 TCP 限制，可以增大辅助角色的大小，或者横向扩展。

## <a name="troubleshooting"></a>疑难解答

了解两种类型的出站连接限制以及应用的作用后，应该就可以更轻松地进行故障排除。 如果你知道自己的应用对同一存储帐户发出许多调用，可以怀疑达到了 SNAT 限制。 如果应用完全通过 Internet 向终结点发出大量的调用，可以怀疑达到了 VM 限制。

如果你对应用程序的行为认识不充分，以致无法快速判断原因，可以借助应用服务中的某些工具和技术做出判断。

### <a name="find-snat-port-allocation-information"></a>查找 SNAT 端口分配信息

你可以使用 [应用服务诊断](./overview-diagnostics.md) 来查找 snat 端口分配信息，并观察应用服务站点的 snat 端口分配指标。 若要查找 SNAT 端口分配信息，请执行以下步骤：

1. 若要访问应用服务诊断，请在 [Azure 门户](https://portal.azure.com/)中导航到你的应用服务 Web 应用或应用服务环境。 在左侧导航栏中，选择“诊断并解决问题”。
2. 选择“可用性和性能”类别
3. 在该类别下的可用磁贴列表中，选择“SNAT 端口耗尽”磁贴。 做法是将其保持在 128 以下。
如果确实需要，仍可以开具支持票证，支持工程师会从后端为你提取指标。

请注意，由于 SNAT 端口用量不是以指标形式提供的，因此无法基于 SNAT 端口用量进行自动缩放，也无法基于 SNAT 端口分配指标配置自动缩放。

### <a name="tcp-connections-and-snat-ports"></a>TCP 连接和 SNAT 端口

TCP 连接和 SNAT 端口并不直接相关。 任何应用服务站点的“诊断并解决问题”边栏选项卡中都包含了 TCP 连接用量检测程序。 搜索短语“TCP 连接”即可找到它。

* SNAT 端口仅用于外部网络流，而 TCP 连接总数包括本地环回连接。
* 如果协议、IP 地址或端口中的流不同，则一个 SNAT 端口可由不同的流共享。 TCP 连接指标统计每个 TCP 连接。
* TCP 连接限制在辅助角色实例级别发生。 Azure 网络出站负载均衡不使用 TCP 连接指标来限制 SNAT 端口。
* [沙盒跨 VM 数字限制 - TCP 连接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)中介绍了 TCP 连接限制

|限制名称|描述|小型 (A1)|中型 (A2)|大型 (A3)|隔离层 (ASE)|
|---|---|---|---|---|---|
|连接|整个 VM 的连接数|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>Web 作业和数据库连接
 
如果 SNAT 端口耗尽，导致 WebJobs 无法连接到 SQL 数据库，则不会有任何指标显示每个 Web 应用程序进程打开的连接数。 若要查找有问题的 Web 作业，请将多个 Web 作业移到另一个应用服务计划，以确定情况是否有所改善，或者某个计划中仍有问题。 重复该过程，直到找出有问题的 Web 作业。

### <a name="using-snat-ports-sooner"></a>更快地使用 SNAT 端口

无法更改任何 Azure 设置来更快地释放已用的 SNAT 端口，因为所有 SNAT 端口是根据下面的条件释放的，且该行为是有意设计的。
 
* 如果服务器或客户端发送 FINACK，则会在 240 秒后[释放 SNAT 端口](../load-balancer/load-balancer-outbound-connections.md)。
* 如果出现 RST，则会在 15 秒后释放 SNAT 端口。
* 如果已达到空闲超时，则会释放端口。
 
## <a name="additional-information"></a>其他信息

* [包含应用服务的 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [排查 Azure 应用服务中应用性能缓慢的问题](./troubleshoot-performance-degradation.md)