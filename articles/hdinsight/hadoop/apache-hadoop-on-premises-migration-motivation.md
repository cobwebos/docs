---
title: 将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 动机和权益
description: 了解将本地 Hadoop 群集迁移到 Azure HDInsight 的动机和权益。
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: a03a778b2a057235b31d02e90e5ce87e9559b38a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058558"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 动机和权益

本文是有关将本地 Apache Hadoop 生态系统部署迁移到 Azure HDInsight 最佳实践系列文章中的第一篇。 本系列文章适用于负责设计、部署和迁移 Azure HDInsight 中 Apache Hadoop 解决方案的人员。 可能受益于这些文章的角色包括云架构师、Hadoop 管理员和 DevOps 工程师。 软件开发人员、数据工程师和数据科学家也可以得益于关于不同类型的群集如何在云中工作的介绍。

## <a name="why-to-migrate-to-azure-hdinsight"></a>迁移到 Azure HDInsight 的原因

Azure HDInsight 是 Hadoop 组件的云分发。 可以通过 Azure HDInsight 轻松、快速且经济有效地处理大量数据。 HDInsight 包括最常用的开源框架，例如：

- Apache Hadoop
- Apache Spark
- 带有 LLAP 的 Apache Hive
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Azure HDInsight 优于本地 Hadoop

- **低成本** - 通过[按需创建群集](../hdinsight-hadoop-create-linux-clusters-adf.md)且仅为使用的资源付费，可降低成本。 分离式计算和存储通过保持数据量独立于群集大小来提供灵活性。

- **自动创建群集** - 自动创建群集需要最少的设置和配置。 自动化可用于按需群集。

- **托管硬件和配置** - 无需担心 HDInsight 群集的物理硬件或基础结构。 只需指定群集的配置，Azure 就会对其进行设置。

- **易于缩放** - 通过 HDInsight 可纵向 [扩展](../hdinsight-administer-use-portal-linux.md)或缩减工作负载。 Azure 负责重新分配数据和重新均衡工作负载，而不会中断数据处理作业。

- **全球可用性** - 提供 HDInsight 的[区域](https://azure.microsoft.com/regions/services/)要多于提供任何其他大数据分析产品/服务的区域。 Azure HDInsight 还在中国和德国的 Azure 政府版中提供，满足关键主权领域的企业需求。

- **安全性和符合性** - HDInsight 允许通过  [Azure 虚拟网络](../hdinsight-extend-hadoop-virtual-network.md)、 [加密](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)以及与  [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md) 集成来保护企业数据资产。 HDInsight 还满足最常用的行业和政府 [符合性标准](https://azure.microsoft.com/overview/trusted-cloud)。

- **简化版本管理** - Azure HDInsight 管理 Hadoop 生态系统组件的版本并使其保持最新。 软件更新在内部部署过程中通常比较复杂。

- **针对特定工作负载优化的较小群集与组件之间的依赖关系较少** - 典型的本地 Hadoop 设置使用具有多种用途的单个群集。 使用 Azure HDInsight，可创建特定于工作负载的群集。 为特定工作负载创建群集消除了维护单个群集日益复杂的复杂性。

- **生产力** - 可在首选开发环境中使用 Hadoop 和 Spark 的各种工具。

- **自定义工具或第三方应用程序的可扩展性** - HDInsight 群集可使用已安装的组件进行扩展，也可以通过 Azure 市场中的[一键式](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) 部署与其他大数据解决方案进行集成。

- **轻松管理、 管理和监视**-与 Azure HDInsight 集成 [Azure Monitor 日志](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 以提供单一界面可以监视所有群集。

- **与其他 Azure 服务集成** - HDInsight 可轻松地与其他常用 Azure 服务进行集成，例如：

    - Azure 数据工厂 (ADF)
    - Azure Blob 存储
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL 数据库
    - Azure Analysis Services

- **自我修复过程和组件** - HDInsight 使用自己的监视基础结构不断检查基础结构和开源组件。 它还可自动修复关键故障，例如开源组件和节点不可用。 任何 OSS 组件发生故障时都会在 Ambari 中触发警报。

有关详细信息，请参阅文章[什么是 Azure HDInsight 和 Apache Hadoop 技术堆栈](../hadoop/apache-hadoop-introduction.md)。

## <a name="migration-planning-process"></a>迁移规划过程

建议使用以下步骤来规划本地 Hadoop 群集到 Azure HDInsight 的迁移：

1. 了解当前的本地部署和拓扑。
2. 了解当前的项目范围、时间线和团队专业知识。
3. 了解 Azure 需求。
4. 构建基于最佳做法的详细计划。

## <a name="gathering-details-to-prepare-for-a-migration"></a>收集详细信息以为迁移做准备

本节提供模板问卷，以帮助收集有关以下内容的重要信息：

- 本地部署
- 项目详细信息
- Azure 要求

### <a name="on-premises-deployment-questionnaire"></a>本地部署问卷

| **问题** | **示例** | **答案** |
|---|---|---|
|**主题**：**环境**|||
|群集分发版本|HDP 2.6.5、CDH 5.7|
|大数据生态系统组件|HDFS、Yarn、Hive、LLAP、Impala、Kudu、HBase、Spark、MapReduce、Kafka、Zookeeper、Solr、Sqoop、Oozie、Ranger、Atlas、Falcon、Zeppelin、R|
|群集类型|Hadoop、Spark、Confluent Kafka、Storm、Solr|
|群集数|4|
|主节点数量|2|
|辅助角色节点数|100|
|边缘节点数| 5|
|总磁盘空间|100 TB|
|主节点配置|m/y、cpu、磁盘等。|
|数据节点配置|m/y、cpu、磁盘等。|
|边缘节点配置|m/y、cpu、磁盘等。|
|是否 HDFS 加密？|是|
|高可用性|HDFS 高可用性、元存储高可用性|
|灾难恢复/备份|是否备份群集？|  
|依赖于群集的系统|SQL Server、Teradata、Power BI、MongoDB|
|第三方集成|Tableau、GridGain、Qubole、Informatica、Splunk|
|**主题**：安全性 |||
|外围安全性|防火墙|
|群集身份验证和授权|Active Directory、Ambari、Cloudera Manager，不进行身份验证|
|HDFS 访问控制|  手动，SSH 用户|
|Hive 身份验证和授权|Sentry、LDAP、带有 Kerberos 的 AD、Ranger|
|审核|Ambari、Cloudera Navigator、Ranger|
|监视|Graphite、collectd、statsd、Telegraf、InfluxDB|
|警报|Kapacitor、Prometheus、Datadog|
|数据保留持续时间| 3 年，5 年|
|群集管理员|单个管理员，多个管理员|

### <a name="project-details-questionnaire"></a>项目详细信息问卷

|**问题**|**示例**|**答案**|
|---|---|---|
|**主题**：工作负载和频率 |||
|MapReduce 作业|10 个作业 -- 每天两次||
|Hive 作业|100 个作业 -- 每小时||
|Spark 批处理作业|50 个作业 -- 每 15 分钟||
|Spark 流式处理作业|5 个作业 -- 每 3 分钟||
|结构化流作业|5 个作业 -- 每分钟||
|机器学习模型训练作业|2 个作业 -- 每周一次||
|编程语言|Python、Scala、Java||
|脚本|Shell、Python||
|**主题**：**数据**|||
|数据源|平面文件、Json、Kafka、RDBMS||
|数据业务流程|Oozie 工作流、气流||
|内存中查找|Apache Ignite、Redis||
|数据目标|HDFS、RDBMS、Kafka、MPP ||
|**主题**：元数据 |||
|Hive 数据库类型|Mysql、Postgres||
|不。 Hive 元存储|2||
|不。 Hive 表|100||
|不。 Ranger 策略|20||
|不。 Oozie 工作流|100||
|**主题**：**缩放**|||
|数据量包括复制|100 TB||
|每日引入量|50 GB||
|数据增长率|每年 10%||
|群集节点增长率|每年 5%
|**主题**：群集利用率 |||
|已使用的平均 CPU 百分比|60%||
|已使用的平均内存百分比|75%||
|已使用的磁盘空间|75%||
|已使用的平均网络百分比|25%
|**主题**：人员 |||
|不。 管理员|2||
|不。 开发人员|10||
|不。 最终用户|100||
|技能|Hadoop、Spark||
|不。 可用于迁移工作的资源|2||
|**主题**：**限制**|||
|当前限制|延迟较高||
|当前挑战|并发问题||

### <a name="azure-requirements-questionnaire"></a>Azure 需求问卷

|**主题**：基础结构  |||
|---|---|---|
|**问题**|**示例**|**答案**|
| 首选区域|美国东部||
|首选 VNet？|是||
|需要 HA/DR？|是||
|与其他云服务进行集成？|ADF、CosmosDB||
|**主题**： **数据移动**  |||
|初始加载首选项|DistCp、Data box、ADF、WANDisco||
|数据传输增量|DistCp、AzCopy||
|正在进行的增量数据传输|DistCp、Sqoop||
|**主题**： 监视和警报  |||
|使用 Azure 监控和警报与集成第三方监视|使用 Azure 监视和警报||
|**主题**： 安全性首选项  |||
|专用和受保护的数据管道？|是||
|已加入域的群集 (ESP)？|     是||
|本地 AD 同步到云？|     是||
|不。 AD 用户同步？|          100||
|确定将密码同步到云？|    是||
|仅云用户？|                 是||
|需要 MFA？|                       否|| 
|数据授权需求？|  是||
|基于角色的访问控制？|        是||
|需要审核？|                  是||
|静态数据加密？|          是||
|在传输中进行数据加密？|       是||
|**主题**： 重建体系结构首选项  |||
|单个群集与特定群集类型|特定群集类型||
|共存存储与远程存储？|远程存储||
|在远程存储数据群集大小更小？|群集大小更小||
|使用多个较小的群集而不是单个大型群集？|使用多个较小的群集||
|使用远程元存储？|是||
|在不同的群集之间共享元存储？|是||
|解构工作负载？|使用 Spark 作业替换 Hive 作业||
|使用 ADF 实现数据业务流程？|否||

## <a name="next-steps"></a>后续步骤

阅读本系列教程的下一篇文章：

- [本地到 Azure HDInsight Hadoop 迁移的体系结构最佳做法](apache-hadoop-on-premises-migration-best-practices-architecture.md)
