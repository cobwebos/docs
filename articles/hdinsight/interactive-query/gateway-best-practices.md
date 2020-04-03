---
title: Azure HDInsight 中 Apache Hive 的网关深度潜水和最佳实践
description: 了解如何导航通过 Azure HDInsight 网关运行 Hive 查询的最佳做法
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586972"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Hive 的网关深度潜水和最佳实践

Azure HDInsight 网关（网关）是 HDInsight 群集的 HTTPS 前端。 网关负责：身份验证、主机解析、服务发现以及现代分布式系统所需的其他有用功能。 网关提供的功能会导致一些开销，本文档将介绍要导航的最佳做法。 还讨论了网关故障排除技术。

## <a name="the-hdinsight-gateway"></a>HDInsight 网关

HDInsight 网关是 HDInsight 群集中唯一可在互联网上公开访问的部分。 可以使用`clustername-int.azurehdinsight.net`内部网关终结点访问网关服务，而无需通过公共 Internet 访问。 内部网关终结点允许在不退出群集的虚拟网络的情况下建立到网关服务的连接。 网关处理发送到群集的所有请求，并将此类请求转发到正确的组件和群集主机。

下图粗略说明了网关如何在 HDInsight 中的所有不同主机解析可能性面前提供抽象。

![主机解析图](./media/gateway-best-practices/host-resolution-diagram.png "主机解析图")

## <a name="motivation"></a>动机

将网关置于 HDInsight 群集的前面的动机是为服务发现和用户身份验证提供接口。 网关提供的身份验证机制与启用 ESP 的群集特别相关。

对于服务发现，网关的优点是群集中的每个组件都可以在网关网站 （）`clustername.azurehdinsight.net/hive2`下作为不同的终结点进行访问，而不是大量`host:port`配对。

对于身份验证，网关允许用户使用凭据对进行`username:password`身份验证。 对于启用 ESP 的群集，此凭据将是用户的域用户名和密码。 通过网关对 HDInsight 群集进行身份验证不需要客户端获取 kerberos 票证。 由于网关接受`username:password`凭据并代表用户获取用户的 Kerberos 票证，因此可以从任何客户端主机（包括连接到与 （ESP） 群集不同的 AA-DDS 域的客户端）与网关建立安全连接。

## <a name="best-practices"></a>最佳做法

网关是负责请求转发和身份验证的单个服务（跨两个主机平衡负载）。 网关可能成为超过特定大小的 Hive 查询的吞吐量瓶颈。 当通过 ODBC 或 JDBC 在网关上执行非常大的**SELECT**查询时，可能会观察到查询性能下降。 "非常大的"是指跨行或列占数据超过 5 GB 的查询。 此查询可以包括行和或宽列计数的长列表。

网关围绕大规模的查询的性能下降是因为数据必须从基础数据存储 （ADLS Gen2） 传输到：HDInsight Hive 服务器、网关，最后通过 JDBC 或 ODBC 驱动程序传输到客户端主机。

下图说明了 SELECT 查询中涉及的步骤。

![结果图](./media/gateway-best-practices/result-retrieval-diagram.png "结果图")

Apache Hive 是兼容 HDFS 的文件系统之上的关系抽象。 此抽象意味着 Hive 中的**SELECT**语句对应于文件系统上的**READ**操作。 在向用户报告之前 **，READ**操作将转换为相应的架构。 此过程的延迟随着数据大小和到达最终用户所需的总跃点而增加。

在执行大型数据的**CREATE**或**INSERT**语句时，可能会出现类似的行为，因为这些命令将对应于基础文件系统中的**WRITE**操作。 请考虑将数据（如原始 ORC）写入文件系统/数据湖，而不是使用**INSERT**或**LOAD**加载数据库。

在启用企业安全包的群集中，足够复杂的 Apache Ranger 策略可能会导致查询编译时间变慢，从而导致网关超时。 如果在 ESP 群集中注意到网关超时，请考虑减少或组合游侠策略的数量。

## <a name="troubleshooting-techniques"></a>故障排除技术

作为上述行为的一部分，有多个缓解和理解绩效问题的场所。 在 HDInsight 网关上遇到查询性能下降时，请使用以下检查表：

* 在执行大型**SELECT**查询时，请使用**LIMIT**子句。 **LIMIT**子句将减少报告给客户端主机的总行数。 **LIMIT**子句仅影响结果生成，不会更改查询计划。 要将**LIMIT**子句应用于查询计划，请使用`hive.limit.optimize.enable`配置 。 **LIMIT**可以使用参数形式**LIMIT x、y**与偏移组合。

* 在运行**SELECT**查询而不是使用**SELECT\*** 时，请命名感兴趣的列。 选择较少的列将降低数据读取量。

* 尝试通过 Apache Beeline 运行感兴趣的查询。 如果通过 Apache Beeline 检索结果需要较长时间，则通过外部工具检索相同结果时会出现延迟。

* 测试基本的 Hive 查询，以确保可以建立与 HDInsight 网关的连接。 尝试从两个或多个外部工具运行基本查询，以确保没有单个工具遇到问题。

* 查看任何阿帕奇安巴里警报，以确保 HDInsight 服务是健康的。 Ambari 警报可以与 Azure 监视器集成以进行报告和分析。

* 如果使用外部 Hive Metastore，请检查 Hive Metastore 的 Azure SQL DB DTU 是否未达到其限制。 如果 DTU 接近其限制，则需要增加数据库大小。

* 确保任何第三方工具（如 PBI 或 Tableau）都使用分页来查看表或数据库。 有关应用方面的帮助，请咨询您的支持合作伙伴。 用于分形的主要工具是 JDBC`fetchSize`参数。 较小的提取大小可能会导致网关性能下降，但获取大小过大可能会导致网关超时。 提取大小调整必须基于工作负载完成。

* 如果数据管道涉及从 HDInsight 群集的基础存储中读取大量数据，请考虑使用直接与 Azure 存储（如 Azure 数据工厂）接口的工具

* 在运行交互式工作负载时考虑使用 Apache Hive LLAP，因为 LLAP 可能会为快速返回查询结果提供更流畅的体验

* 请考虑增加使用`hive.server2.thrift.max.worker.threads`的 Hive Metastore 服务的可用线程数。 当大量并发用户向群集提交查询时，此设置尤其重要

* 减少从任何外部工具到达网关的重试次数。 如果使用多个重试，请考虑遵循指数级回退重试策略

* 请考虑使用 配置`hive.exec.compress.output`和`hive.exec.compress.intermediate`启用压缩配置。

## <a name="next-steps"></a>后续步骤

* [在 HDInsight 上阿帕奇蜜蜂](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [HDInsight 网关超时故障排除步骤](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [高清洞察虚拟网络](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [高清洞察与快速路线](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)