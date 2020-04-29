---
title: Azure HDInsight 中 Apache Hive 的网关深入探讨和最佳实践
description: 了解如何通过 Azure HDInsight 网关导航运行 Hive 查询的最佳实践
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80586972"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Hive 的网关深入探讨和最佳实践

Azure HDInsight 网关（网关）是 HDInsight 群集的 HTTPS 前端。 网关负责：身份验证、主机解析、服务发现，以及新式分布式系统所需的其他有用功能。 网关提供的功能会导致一些系统开销，此文档将描述要导航的最佳实践。 还介绍了网关故障排除技术。

## <a name="the-hdinsight-gateway"></a>HDInsight 网关

HDInsight 网关是可通过 internet 公开访问的 HDInsight 群集的唯一部分。 可以访问网关服务，无需使用`clustername-int.azurehdinsight.net`内部网关终结点通过公共 internet。 内部网关终结点允许连接到网关服务而无需退出群集的虚拟网络。 网关处理发送到群集的所有请求，并将此类请求转发到正确的组件和群集主机。

下图提供了有关网关如何在 HDInsight 中的所有不同主机分辨率可能性之前提供抽象的粗略说明。

![主机解析示意图](./media/gateway-best-practices/host-resolution-diagram.png "主机解析示意图")

## <a name="motivation"></a>动机

将网关放在 HDInsight 群集前面的动机是提供服务发现和用户身份验证的接口。 网关提供的身份验证机制特别适用于启用了 ESP 的群集。

对于服务发现，网关的优点是，可以将群集中的每个组件作为网关网站（ `clustername.azurehdinsight.net/hive2`）上的不同终结点进行访问，而不是`host:port`将其作为多种配对。

对于身份验证，网关允许用户使用`username:password`凭据对进行身份验证。 对于启用了 ESP 的群集，此凭据将是用户的域用户名和密码。 通过网关对 HDInsight 群集进行身份验证不需要客户端获取 kerberos 票证。 由于网关接受`username:password`凭据并代表用户获取用户的 Kerberos 票证，因此可以从任何客户端主机（包括与（ESP）群集加入不同的 AA DDS 域的客户端）进行安全连接。

## <a name="best-practices"></a>最佳做法

网关是单个服务（跨两个主机进行负载均衡），负责请求转发和身份验证。 网关可能成为超过特定大小的 Hive 查询的吞吐量瓶颈。 当在网关上通过 ODBC 或 JDBC 执行非常大的**选择**查询时，可能会发现查询性能下降。 "非常大" 表示跨行或列构成超过 5 GB 的数据的查询。 此查询可能包含较长的行列表、或较宽的列计数。

由于必须将数据从基础数据存储区（ADLS Gen2）传输到客户端主机的 JDBC Hive 服务器和网关，最后通过 JDBC 或 ODBC 驱动程序将数据传输到客户端主机，因此网关的性能下降。

下图说明了 SELECT 查询中涉及的步骤。

![结果关系图](./media/gateway-best-practices/result-retrieval-diagram.png "结果关系图")

Apache Hive 是基于 HDFS 兼容的文件系统的关系抽象。 此抽象意味着 Hive 中的**SELECT**语句对应于文件系统上的**读取**操作。 在向用户报告之前，**读取**操作会转换为相应的架构。 此过程的延迟随着与最终用户所需的数据大小和总跃点的增加而增加。

当执行大型数据的**CREATE**或**INSERT**语句时，可能会出现类似的行为，因为这些命令将对应于基础文件系统中的**写入**操作。 考虑将数据（例如原始 ORC）写入 filesystem/datalake，而不是使用**INSERT**或**LOAD**加载它。

在启用企业安全包的群集中，足够复杂的 Apache Ranger 策略可能导致查询编译时间缓慢，这可能会导致网关超时。 如果网关超时在 ESP 群集中被发现，请考虑减少或组合 ranger 策略的数目。

## <a name="troubleshooting-techniques"></a>故障排除技术

有多个会场可用于缓解和理解作为上述行为的一部分而满足的性能问题。 通过 HDInsight 网关进行查询性能下降时，请使用以下清单：

* 执行大型**SELECT**查询时，请使用**LIMIT**子句。 **LIMIT**子句会将报告的总行数减少到客户端主机。 **LIMIT**子句仅影响生成结果，而不会更改查询计划。 若要将**LIMIT**子句应用于查询计划，请使用配置`hive.limit.optimize.enable`。 **限制**可以使用自变量**limit x，y**的偏移量进行组合。

* 运行 "**选择**查询" 而不是 "**选择\* **" 时，命名感兴趣的列。 选择的列越少，读取的数据量就越小。

* 尝试通过 Apache Beeline 运行感兴趣的查询。 如果通过 Apache Beeline 进行的结果检索时间过长，则在通过外部工具检索相同的结果时，会出现延迟。

* 测试基本 Hive 查询以确保可以建立与 HDInsight 网关的连接。 尝试从两个或更多外部工具运行基本查询，以确保不会有任何单个工具遇到问题。

* 查看任何 Apache Ambari 警报以确保 HDInsight 服务运行正常。 Ambari 警报可以与 Azure Monitor 集成，以便进行报告和分析。

* 如果使用外部 Hive 元存储，请检查 Hive 元存储的 Azure SQL DB DTU 是否未达到其限制。 如果 DTU 接近其限制，则需要增大数据库大小。

* 确保任何第三方工具（如 PBI 或 Tableau）使用分页来查看表或数据库。 请咨询你的支持合作伙伴以获取这些工具，以帮助进行分页。 用于分页的主要工具是 JDBC `fetchSize`参数。 较小的提取大小可能会导致网关性能下降，但提取大小太大可能会导致网关超时。 必须基于工作负荷进行提取大小优化。

* 如果数据管道涉及从 HDInsight 群集的基础存储中读取大量数据，请考虑使用直接与 Azure 存储（如 Azure 数据工厂）进行交互的工具

* 在运行交互式工作负荷时，请考虑使用 Apache Hive LLAP，因为 LLAP 可能会为快速返回查询结果提供更流畅的体验

* 请考虑使用`hive.server2.thrift.max.worker.threads`增加 Hive 元存储服务可用的线程数。 当大量并发用户正在向群集提交查询时，此设置尤其相关

* 减少用于从任何外部工具连接到网关的重试次数。 如果使用了多次重试，请考虑使用指数回退重试策略

* 请考虑使用配置`hive.exec.compress.output`和`hive.exec.compress.intermediate`启用压缩 Hive。

## <a name="next-steps"></a>后续步骤

* [Apache Beeline on HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [HDInsight 网关超时故障排除步骤](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [用于 HDInsight 的虚拟网络](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [具有快速路由的 HDInsight](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)