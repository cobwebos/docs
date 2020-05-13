---
title: Apache Hadoop 组件和版本 - Azure HDInsight
description: 了解 Azure HDInsight 中的 Apache Hadoop 组件和版本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/08/2020
ms.openlocfilehash: 9ce6df8491bf4ce7cbb0e12a08816e198f665beb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124646"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>适用于 Azure HDInsight 的 Apache 组件和版本

本文介绍 Azure HDInsight 中的[Apache Hadoop](https://hadoop.apache.org/)环境组件和版本以及企业安全性套餐。 你还将了解如何检查 HDInsight 中的 Hadoop 组件版本。

## <a name="apache-components-available-with-different-hdinsight-versions"></a>不同 HDInsight 版本中提供的 Apache 组件

Azure HDInsight 支持多个可随时部署的 Hadoop 群集版本。 2017年4月4日，Azure HDInsight 使用的默认群集版本为3.6。

下表中列出了与 HDInsight 群集版本关联的组件版本。

> [!NOTE]
> HDInsight 服务的默认版本可能会更改，不会另行通知。 如果有版本依赖项，请在使用带有 Azure PowerShell 的 .NET SDK 和 Azure 经典 CLI 创建群集时指定 HDInsight 版本。

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
| Apache Kafka           | 1.1.1、2.1    | 1.1、1.0 * （请参阅注释）。   |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> 由于系统性能方面的考虑，对 Kafka 版本0.10 的支持在3月2019过期。

## <a name="check-for-current-apache-component-version-information"></a>检查当前 Apache 组件版本信息

与 HDInsight 群集版本关联的 Hadoop 环境组件版本可能会随 HDInsight 的更新而更改。 若要检查 Hadoop 组件并验证正在为群集使用哪些版本，请使用 Ambari REST API。 **GetComponentInformation** 命令检索有关服务组件的信息。 有关详细信息，请参阅[Apache Ambari 文档](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

### <a name="release-notes"></a>发行说明

有关 HDInsight 最新版本的更多发行说明，请参阅[hdinsight 发行说明](hdinsight-release-notes.md)。

## <a name="supported-hdinsight-versions"></a>支持的 HDInsight 版本

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>支持 HDInsight 版本的到期和停用

**支持到期**意味着 Microsoft 不再提供对特定 HDInsight 版本的支持。 在群集创建过程中，它 Azure 门户将不再可用。 仍可以使用 Azure CLI 或各种 Sdk 来创建这些版本。

**停**用表示 HDInsight 版本的现有群集继续按原样运行。 不能通过任何方式（包括 CLI 和 Sdk）创建此版本的新群集。 其他控制平面功能（如手动缩放和自动缩放）在版本停用后也可能不起作用。 支持不适用于停用的版本。

下表列出了 HDInsight 版本。 如果已知，则会提供支持到期日期和停用日期。

### <a name="available-versions"></a>可用版本

此表列出了 Azure 门户中的 HDInsight 版本，以及其他部署方法（如 PowerShell 和 .NET SDK）。

| HDInsight 版本 | VM OS | 发布日期 | 支持到期日期 | 停用日期 | 高可用性 |  在 Azure 门户中的可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |是 |是 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日 | 2020 年 12 月 31 日 |2020 年 12 月 31 日 |是 |是 |

Spark 2.1、2.2 和 Kafka 1.0 支持于6月 30 2020 日过期。

> [!NOTE]
> 在对某个版本的支持到期后，可能无法通过 Azure 门户获取该版本。 在版本停用日期之前，可以使用 Windows PowerShell [AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)命令和 .net SDK 中的**version**参数继续使用群集版本。

### <a name="retired-versions"></a>已停用的版本

此表列出了 Azure 门户中没有的 HDInsight 版本。

| HDInsight 版本 | HDP 版本 | VM OS | 发布日期 | 支持到期日期 | 停用日期 | 高可用性 |  在 Azure 门户中的可用性 |
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
> 对于 HDInsight 2.1 和更高版本，默认情况下会集部署具有两个头节点的高度可用群集。 它们不适用于 HDInsight 1.6 版群集。

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 群集版本的服务级别协议

服务级别协议定义为_支持窗口_。 支持窗口是 HDInsight 版本受 Microsoft 客户服务和支持部门支持的时间段。 如果版本已超过_支持到期日期_，则 HDInsight 群集不在支持范围内。 支持 HDInsight 版本 X 的过期（在更新的 X + 1 版本可用之后）晚于：

- **公式1：** 将180天添加到 HDInsight 群集版本 X 的发布日期。
- **公式2：** 在 Azure 门户中提供 HDInsight 群集版本 X + 1 时，将90天添加到该日期。

_停用日期_是指在此后不能在 HDInsight 上创建群集版本的日期。 从2017年7月31日开始，你无法在其停用日期之后调整 HDInsight 群集的大小。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>群集的默认节点配置和虚拟机大小

有关要为群集选择哪些虚拟机 SKU 的详细信息，请参阅 [Azure HDInsight 群集配置详细信息](hdinsight-supported-node-configuration.md)。

## <a name="next-steps"></a>后续步骤

- [为 HDInsight 上的 Apache Hadoop、Spark 和其他组件设置群集](hdinsight-hadoop-provision-linux-clusters.md)
- [使用 Windows 电脑在 HDInsight 上的 Apache Hadoop 中工作](hdinsight-hadoop-windows-tools.md)
- [与 Azure HDInsight 版本相关的 Hortonworks 发行说明](./hortonworks-release-notes.md)
- [企业安全性套餐](./enterprise-security-package.md)