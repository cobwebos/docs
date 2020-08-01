---
title: 排查 Azure 应用服务中的间歇性出站连接错误
description: 排查 Azure 应用服务中的间歇性连接错误和相关性能问题
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 5e1f2108c5607917c77330f362952f960e57e03a
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447913"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>排查 Azure 应用服务中的间歇性出站连接错误

本文帮助你排查 [Azure 应用服务](https://docs.microsoft.com/azure/app-service/overview)中的间歇性连接错误和相关性能问题。 本主题将提供有关源地址网络转换 (SNAT) 端口耗尽的详细信息及其故障排除方法。 如果在本文中有任何需要帮助的地方，请联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 

## <a name="symptoms"></a>症状

托管在 Azure 应用服务中的应用程序和功能可能会出现以下一种或多种症状：

* 服务计划中的所有或部分实例的响应速度缓慢。
* 间歇性 5xx 错误或“错误的网关”错误 
* 超时错误消息
* 无法连接到外部终结点（例如 SQLDB、Service Fabric、其他应用服务等）

## <a name="cause"></a>原因

出现这种症状的主要原因是，应用程序实例已达到以下限制之一，因此无法与外部终结点打开新的连接：

* TCP 连接数：可以建立的出站连接数有限制。 此数字与所用辅助角色的大小相关联。
* SNAT 端口数：如 [Azure中的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)中所述，Azure 使用源网络地址转换 (SNAT) 和负载均衡器（不向客户公开）在公共 IP 地址空间中与 Azure 外部的终结点通信。 最初为 Azure 应用服务中的每个实例预分配了 128 个 SNAT 端口。  该限制会影响与同一个主机/端口组合打开的连接数。 如果应用与混合的地址/端口组合建立了连接，则不会用尽 SNAT 端口。 重复调用同一个地址/端口组合时，会用尽 SNAT 端口。 释放某个端口以后，即可根据需要重复使用该端口。 只有在等待 4 分钟后，Azure 网络负载均衡器才会从关闭的连接回收 SNAT 端口。

当应用程序或功能快速打开新的连接时，它们可能很快就会耗尽预分配的配额（128 个端口）。 然后，应用程序或功能会一直受到阻止，直到通过动态分配额外的 SNAT 端口或者通过重复使用回收的 SNAT 端口提供了新的 SNAT 端口为止。 由于无法创建新连接而被阻止的应用程序或功能将开始遇到本文的“症状”部分所述的一种或多种问题。 

## <a name="avoiding-the-problem"></a>避免问题

如果目标是支持服务终结点的 Azure 服务，则可以通过使用[区域 VNet 集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)和服务终结点或专用终结点来避免 SNAT 端口耗尽问题。 使用区域 VNet 集成并将服务终结点置于集成子网中时，指向这些服务的应用出站流量不会有出站 SNAT 端口限制。 同样，如果使用区域 VNet 集成和专用终结点，则不会向该目标发送任何出站 SNAT 端口问题。 

避免 SNAT 端口问题意味着避免在同一主机和端口上重复创建新连接。

Azure 文档的**出站连接**的[问题解决部分](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving)中讨论了用于缓解 SNAT 端口耗尽的常规策略。 在这些策略中，以下各项适用于托管在 Azure 应用服务上的应用程序和功能。

### <a name="modify-the-application-to-use-connection-pooling"></a>修改应用程序以使用连接池

* 对于 "池 HTTP 连接"，请查看[池 http 连接与 HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)。
* 有关 SQL Server 连接池的信息，请查看[SQL Server 连接池（ADO.NET）](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)。
* 若要实现与 entity framework 应用程序的池，请查看[DbContext pooling](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)。

下面是用于通过不同解决方案堆栈实现连接池的链接集合。

#### <a name="node"></a>节点

默认情况下，NodeJS 的连接不会保持活动状态。 下面是适用于连接池的常用数据库和包，其中包含如何实现它们的示例。

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP 保持活动状态

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js 13.9.0 文档](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

下面是用于 JDBC 连接池的常用库，其中包含如何实现它们的示例： JDBC 连接池。

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 连接池

* [Apache 连接管理](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [类 PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

尽管 PHP 不支持连接池，但你可以尝试使用持久的数据库连接到后端服务器。
 
* MySQL 服务器

   * 针对较新版本的[MySQLi 连接](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * 旧版本的 PHP [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* 其他数据源

   * [PHP 连接管理](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) （注意： SQLAlchemy 可用于除 MicrosoftSQL 服务器以外的其他数据库）
* [HTTP 保持活动状态](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)（使用会话[会话对象](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)时，keep-alive 会自动进行）。

对于其他环境，请查看用于在应用程序中实现连接池的提供程序或特定于驱动程序的文档。

### <a name="modify-the-application-to-reuse-connections"></a>修改应用程序以重复使用连接

*  有关管理 Azure 功能中的连接的其他指针和示例，请查看[Azure Functions 中的 "管理连接](https://docs.microsoft.com/azure/azure-functions/manage-connections)"。

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>修改应用程序以使用主动性较低的重试逻辑

* 有关其他指导和示例，请查看[重试模式](https://docs.microsoft.com/azure/architecture/patterns/retry)。

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>使用 keepalive 重置出站空闲超时

* 若要为 Node.js 应用实现 keepalive，请查看[节点应用程序是否发出过多出站呼叫](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)。

### <a name="additional-guidance-specific-to-app-service"></a>特定于应用服务的其他指导：

* [负载测试](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test)应以稳定的供电速度模拟实际数据。 在真实环境中测试应用和功能可以提前识别和解决 SNAT 端口耗尽问题。
* 确保后端服务可以快速返回响应。 若要排查 Azure SQL 数据库的性能问题，请参阅[排查智能见解的 AZURE Sql 数据库性能问题](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)。
* 将应用服务计划横向扩展到更多实例。 有关缩放的详细信息，请参阅[缩放 Azure 应用服务中的应用](https://docs.microsoft.com/azure/app-service/manage-scale-up)。 应用服务计划中的每个辅助角色实例都分配了多个 SNAT 端口。 如果你将使用情况分散到多个实例，则可能会将每个实例的 SNAT 端口使用情况获取为每个唯一远程终结点上建议的100出站连接限制。
* 请考虑移动到[应用服务环境（ASE）](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)，其中你分配了一个出站 IP 地址，连接和 SNAT 端口限制更多。

避免出站 TCP 限制更易于解决，因为限制是由辅助角色的大小设置的。 可以查看[沙盒跨 VM 数字限制-TCP 连接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)的限制

|限制名称|描述|小型（A1）|中（A2）|大（A3）|隔离层（ASE）|
|---|---|---|---|---|---|
|连接|跨整个 VM 的连接数|1920|3968|8064|16,000|

若要避免出站 TCP 限制，可以增加工作线程的大小，也可以水平横向扩展。

## <a name="troubleshooting"></a>故障排除

了解这两种类型的出站连接限制以及应用的作用，应使故障排除变得更容易。 如果你知道你的应用程序对同一存储帐户进行多个调用，则可能会怀疑 SNAT 限制。 如果你的应用程序通过 internet 创建对终结点的大量调用，则你可能会认为你达到了 VM 限制。

如果你不知道应用程序的行为是否能够快速确定原因，应用服务中有一些工具和方法可帮助确定这一点。

### <a name="find-snat-port-allocation-information"></a>查找 SNAT 端口分配信息

你可以使用[应用服务诊断](https://docs.microsoft.com/azure/app-service/overview-diagnostics)来查找 snat 端口分配信息，并观察应用服务站点的 snat 端口分配指标。 若要查找 SNAT 端口分配信息，请执行以下步骤：

1. 若要访问应用服务诊断，请导航到应用服务 web 应用或[Azure 门户](https://portal.azure.com/)中的应用服务环境。 在左侧导航窗格中，选择 "**诊断并解决问题**"。
2. 选择可用性和性能类别
3. 在类别下的可用磁贴列表中，选择 "SNAT 端口耗尽" 磁贴。 这种做法是将其保留在128以下。
如果需要，你仍可以打开支持票证，支持工程师将从后端获取指标。

请注意，由于 SNAT 端口使用未作为指标提供，因此不能基于 SNAT 端口使用自动缩放，也不能基于 SNAT 端口分配指标配置自动缩放。

### <a name="tcp-connections-and-snat-ports"></a>TCP 连接和 SNAT 端口

TCP 连接和 SNAT 端口不是直接相关的。 TCP 连接使用检测程序包含在任何应用服务站点的 "诊断和解决问题" 边栏选项卡中。 搜索短语 "TCP 连接" 以找到它。

* SNAT 端口仅用于外部网络流，而 TCP 连接总数包含本地环回连接。
* 如果不同的流在协议、IP 地址或端口上不同，则可以使用不同的流来共享 SNAT 端口。 TCP 连接指标计算每个 TCP 连接数。
* TCP 连接限制发生在辅助实例级别。 对于 SNAT 端口限制，Azure 网络出站负载平衡不使用 TCP 连接指标。
* [沙盒跨 VM 数字限制-Tcp 连接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)中描述了 tcp 连接限制

|限制名称|描述|小型（A1）|中（A2）|大（A3）|隔离层（ASE）|
|---|---|---|---|---|---|
|连接|跨整个 VM 的连接数|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>Web 作业和数据库连接
 
如果 SNAT 端口耗尽，在这种情况下，Web 作业无法连接到 SQL 数据库，则没有用于显示每个单独的 web 应用程序进程打开的连接数的指标。 若要查找有问题的 WebJob，请将多个 Web 作业移出到另一个应用服务计划，以查看该情况是否提高，或者是否在某个计划中仍有问题。 重复此过程，直到找到有问题的 Web 作业。

### <a name="using-snat-ports-sooner"></a>更快地使用 SNAT 端口

你不能更改任何 Azure 设置以更快地释放已使用的 SNAT 端口，因为根据以下条件发布所有 SNAT 端口，并且该行为是设计使然。
 
* 如果服务器或客户端发送 FINACK，则将在240秒后[释放 SNAT 端口](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release)。
* 如果检测到 RST，则会在15秒后释放 SNAT 端口。
* 如果已达到空闲超时，则释放端口。
 
## <a name="additional-information"></a>其他信息

* [应用服务的 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [排查 Azure 应用服务中应用性能缓慢的问题](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
