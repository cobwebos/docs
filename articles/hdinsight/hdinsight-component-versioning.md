---
title: Apache Hadoop 组件和版本 - Azure HDInsight
description: 了解 Azure HDInsight 中的 Apache Hadoop 组件和版本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/09/2020
ms.openlocfilehash: e27b65346ba8d48e36d080d66754db9605259e2b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144408"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>HDInsight 提供了哪些 Apache Hadoop 组件和版本？

了解 Microsoft Azure HDInsight 和企业安全性套餐中[Apache Hadoop](https://hadoop.apache.org/)环境组件和版本。 另外，还将了解如何检查 HDInsight 中的 Hadoop 组件版本。

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>随不同 HDInsight 版本提供的 Apache Hadoop 组件

Azure HDInsight 支持多个可随时部署的 Hadoop 群集版本。 2017年4月4日，Azure HDInsight 使用的默认群集版本为3.6。

下表列出了与 HDInsight 群集版本关联的组件版本：

> [!NOTE]  
> HDInsight 服务的默认版本可能会更改，不会另行通知。 如果依赖某个版本，建议在使用 .NET SDK 以及 Azure PowerShell 和 Azure 经典 CLI 创建群集时指定 HDInsight 版本。

| 组件              | HDInsight 4.0 | HDInsight 3.6（默认）     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop 和 YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 （ESP 交互式查询上的2.1.0） |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1、2.4    | 2.3.0、2.2.0、2.1.0         |
| Apache Livy            | 0.5           | 0.4、0.4、0.3               |
| Apache Kafka           | 1.1.1、2.1    | 1.1、1.0 *（请参阅下面的“注意”） |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> 出于系统性能考量，对 Kafka 版本 0.10 的支持已在 2019 年 3 月过期。

## <a name="check-for-current-hadoop-component-version-information"></a>检查当前的 Hadoop 组件版本信息

与 HDInsight 群集版本关联的 Hadoop 环境组件版本可能会随 HDInsight 的更新而更改。 若要检查 Hadoop 组件并验证正在为群集使用哪些版本，请使用 Ambari REST API。 **GetComponentInformation** 命令检索有关服务组件的信息。 有关详细信息，请参阅 [Apache Ambari 文档](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

### <a name="release-notes"></a>发行说明

请参阅 [HDInsight 发行说明](hdinsight-release-notes.md)，了解 HDInsight 最新版本的更多发行说明。

## <a name="supported-hdinsight-versions"></a>支持的 HDInsight 版本

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>支持 HDInsight 版本的到期和停用

**支持到期**意味着 Microsoft 将不再为指定的 HDInsight 版本提供支持。 并且它将不再通过用于创建群集的 Azure 门户提供。 但是，仍可以使用 Azure CLI 或各种 Sdk 来创建这些版本。

HDInsight 版本的**停**用意味着现有群集将继续按原样运行。 但是，不能通过任何方式（包括 CLI 和 Sdk）创建此版本的新群集。 其他控制平面功能（如手动缩放和自动缩放）在版本停用后也可能不起作用。 支持不适用于停用的版本。

下表列出了 HDInsight 版本。 还提供了支持到期日期和停用日期（如果已知这些日期）。

### <a name="available-versions"></a>可用版本

下表列出了 Azure 门户中可用的 HDInsight 版本以及 PowerShell 和 .NET SDK 等其他部署方法。

| HDInsight 版本 | VM OS | 发布日期 | 支持到期日期 | 停用日期 | 高可用性 |  在 Azure 门户中的可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |是 |是 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日 | 2020 年 12 月 31 日 |2020 年 12 月 31 日 |是 |是 |

Spark 2.1，2.2 & Kafka 1.0 支持将在年6月 30 2020 日过期。

> [!NOTE]  
> 在对某个版本的支持到期后，不能通过 Microsoft Azure 门户获得该版本。 但是，可继续使用 Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) 命令中的 `Version` 参数和 .NET SDK 获取群集版本，直到版本停用的那天为止。

### <a name="retired-versions"></a>已停用的版本

下表列出了 Azure 门户中**没有**的 HDInsight 版本。

| HDInsight 版本 | HDP 版本 | VM OS | 发布日期 | 支持到期日期 | 停用日期 | 高可用性 |  在 Azure 门户上的可用性 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |2016 年 9 月 30 日 |2017 年 9 月 5 日 |2018 年 6 月 28 日 |是 |否 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016 年 3 月 29 日 |2016 年 12 月 29 日 |2018 年 1 月 9 日 |是 |否 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2018 年 7 月 31 日 |是 |否 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2017 年 7 月 31 日 |是 |否 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 或 Windows Server 2012 R2 |2015 年 2 月 18 日 |2016 年 3 月 1 日 |2017 年 4 月 1 日 |是 |否 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014 年 6 月 24 日 |2015 年 5 月 18 日 |2016 年 6 月 30 日 |是 |否 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014 年 2 月 11 日 |2014 年 9 月 17 日 |2015 年 6 月 30 日 |是 |否 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013 年 10 月 28 日 |2014 年 5 月 12 日 |2015 年 5 月 31 日 |是 |否 |
| HDInsight 1.6 |HDP 1.1 | |2013 年 10 月 28 日 |2014 年 4 月 26 日 |2015 年 5 月 31 日 |否 |否 |

> [!NOTE]  
> 对于 HDInsight 2.1 和更高版本，默认情况下会集部署具有两个头节点的高度可用群集。 它们不适用于 HDInsight 1.6 版本的群集。

## <a name="enterprise-security-package-for-hdinsight"></a>适用于 HDInsight 的 Enterprise Security 包

Enterprise Security 是一种可选包，可在 HDInsight 群集上作为创建群集工作流的一部分添加。 Enterprise Security 包支持：

- 与 Active Directory 集成进行身份验证。

    过去，你创建了包含本地管理员用户和本地 SSH 用户的 HDInsight 群集。 本地管理员用户可以访问所有文件、文件夹、表和列。  使用企业安全性套餐，可以通过将 HDInsight 与 Active Directory 集成来启用基于角色的访问控制。 其中包括本地 Active Directory Azure Active Directory 域服务。 或 Active Directory。 群集上的域管理员可以授予用户使用其自己的公司（域）用户名和密码的权限。

    有关更多信息，请参阅：

    - [已加入域的 HDInsight 群集的 Apache Hadoop 安全性简介](./domain-joined/hdinsight-security-overview.md)
    - [在 HDInsight 中计划 Azure 已加入域的 Apache Hadoop 群集](./domain-joined/apache-domain-joined-architecture.md)
    - [配置已加入域的沙盒环境](./domain-joined/apache-domain-joined-configure.md)
    - [使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- 数据授权

  - 与 Apache Ranger 集成以对 Hive、Spark SQL 和 Yarn 队列授权。
  - 可以对文件和文件夹设置访问控制。

    有关更多信息，请参阅：

  - [在已加入域的 HDInsight 中配置 Apache Hive 策略](./domain-joined/apache-domain-joined-run-hive.md)

- 查看审核日志以监视访问和配置的策略。

### <a name="supported-cluster-types"></a>支持的群集类型

目前只有以下群集类型支持 Enterprise Security 包：

- Hadoop（仅限 HDInsight 3.6）
- Spark
- Kafka
- HBase
- 交互式查询

### <a name="support-for-azure-data-lake-storage"></a>支持 Azure Data Lake Storage

Enterprise Security 包支持使用 Azure Data Lake Storage 作为主存储和附加存储。

### <a name="pricing-and-service-level-agreement-sla"></a>定价和服务级别协议（SLA）

有关 Enterprise Security 包的定价和 SLA 的信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 群集版本的服务级别协议

服务级别协议（SLA）定义为 "支持"_窗口_。 支持窗口是支持 HDInsight 版本的时间段`Microsoft Customer Service and Support`。 如果版本具有已通过的_支持过期日期_，则 HDInsight 群集不在支持范围内。 支持 HDInsight 版本 X 的过期（在更新的 X + 1 版本可用之后）为后面的版本：  

- 公式 1：发布 HDInsight 群集版本 X 的日期加 180 天。
- 公式 2：在 Azure 门户中提供 HDInsight 群集版本 X+1 的日期加 90 天。

_停用日期_是指在此后不能在 HDInsight 上创建群集版本的日期。 从2017年7月31日开始，不能在 HDInsight 群集停用日期之后调整它的大小。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>群集的默认节点配置和虚拟机大小

有关要为群集选择哪些虚拟机 SKU 的详细信息，请参阅 [Azure HDInsight 群集配置详细信息](hdinsight-supported-node-configuration.md)。

## <a name="next-steps"></a>后续步骤

- [为 HDInsight 上的 Apache Hadoop、Spark 和其他组件设置群集](hdinsight-hadoop-provision-linux-clusters.md)
- [使用 Windows 电脑在 HDInsight 上的 Apache Hadoop 中工作](hdinsight-hadoop-windows-tools.md)
- [与 Azure HDInsight 版本相关的 Hortonworks 发行说明](./hortonworks-release-notes.md)
