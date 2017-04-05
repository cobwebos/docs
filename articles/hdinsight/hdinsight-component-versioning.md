---
title: "Hadoop 组件和版本 - Azure HDInsight | Microsoft 文档"
description: "了解 HDInsight 中的 Hadoop 组件和版本，以及 HortonWorks 数据平台的此云分发中可用的服务级别。"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: bprakash
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: c753eac8caed139250a0db85b639fca57c1e9ea6
ms.lasthandoff: 03/28/2017


---
# <a name="what-are-the-different-hadoop-components-and-versions-available-with-hdinsight"></a>可与 HDInsight 使用的不同 Hadoop 组件和版本有哪些？

了解对 Azure HDInsight 提供的服务级别，以及所包含的 Hadoop 生态系统组件和版本。 每个 HDInsight 版本都是某个版本的 Hortonworks 数据平台 (HDP) 的云分发。

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight 标准版和 HDInsight 高级版

Azure HDInsight 提供了两个类别的大数据云产品/服务：**标准**和**高级**。 下表部分列出了**仅可作为高级版的一部分**使用的功能。 没有在此处的表格中显式调用的功能将用作为标准版的一部分可用。

> [!NOTE]
> HDInsight 高级版产品/服务当前处于预览状态，仅可用于 Linux 群集。
>
>

| HDInsight 高级版功能 | 说明 |
| --- | --- |
| 已加入域的 HDInsight 群集 |将 HDInsight 群集加入 Azure Active Directory (AAD) 域以实现企业级安全性。 现在可配置企业员工的列表，这些员工可通过 Azure Active Directory 进行身份验证登录到 HDInsight 群集。 企业管理员还可以使用 [Apache Ranger](http://hortonworks.com/apache/ranger/) 配置基于角色的访问控制来实现 Hive 安全性，从而根据最大需求权限来限制数据访问权限。 最后，管理员可以审核员工访问的数据以及对访问控制策略所做的任何更改，从而以较大的力度监管其企业资源。 有关详细信息，请参阅 [Configure domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md)（配置已加入域的 HDInsight 群集）。 |

### <a name="cluster-types-supported-for-hdinsight-premium"></a>HDInsight Premium 支持的群集类型
下表列出了 HDInsight 群集类型和高级版支持矩阵。

| 群集类型 | 标准 | 高级 |
| --- | --- | --- |
| Hadoop |是 |是（仅限于 HDInsight 3.5） |
| Spark |是 |否 |
| HBase |是 |否 |
| Storm |是 |否 |
| R Server  |是 |否 |
| 交互式 Hive（预览版） |是 |否 |
| Kafka（预览版）|是|否| 

随着 HDInsight 高级版中包含的群集类型更多，此表也会随之更新。

### <a name="features-not-supported-for-hdinsight-premium"></a>HDInsight Premium 不支持的功能

HDInsight Premium 群集当前不支持以下功能。

* **不支持将 Azure Data Lake Store 作为主存储**。 仍然可以将 Azure Data Lake Store 用作 HDInsight Premium 群集的附加存储。

### <a name="pricing-and-sla"></a>定价和 SLA
有关 HDInsight 高级版的定价和 SLA 的信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>可与不同 HDInsight 版本使用的 Hadoop 组件
Azure HDInsight 支持多个可随时部署的 Hadoop 群集版本。 每个版本选项创建 Hortonworks 数据平台 (HDP) 分发的特定版本和该分发内包含的一组组件。 下表中逐项列出了与 HDInsight 群集版本关联的组件版本。 请注意，Azure HDInsight 使用的默认群集版本当前是 3.5（到 2017 年 2 月 17 日为止）并基于 HDP 2.5。

> [!NOTE]
> 服务默认版本随时可更改，恕不另行通知。 如果依赖某个版本，我们建议在创建群集时使用 .NET SDK/Azure PowerShell 和 Azure CLI 指定版本。
>
>


| 组件 | HDInsight version 3.5（默认） | HDInsight 版本 3.4 | HDInsight 版本 3.3 | HDInsight 版本 3.2 | HDInsight 版本 3.1 | HDInsight 版本 3.0 |
| --- | --- | --- | --- | --- | --- | --- |
| Hortonworks 数据平台 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop 和 YARN |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive 和 HCatalog |1.2.1.2.5 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache HBase |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |1.6.2 + 2.0（仅限 Linux） |1.6.0（仅限 Linux） |1.5.2（仅限 Linux/实验性生成） |1.3.1（仅限 Windows） |-|-|
| Apache Kafka | 0.10.0 | 0.9.0 |-|-|-|-|
| Mono |4.2.1 |3.2.8 |-|-|-|-|

**获取当前组件版本信息**

与 HDInsight 群集版本关联的组件版本可能在将来的 HDInsight 更新中更改。 确定可用组件并验证正在使用哪些群集版本的一种方法是使用 Ambari REST API。 **GetComponentInformation** 命令可用于检索有关服务组件的信息。 有关详细信息，请参阅 [Ambari 文档][ambari-docs]。 获取此信息的另一个方法是使用远程桌面登录到群集并直接检查“C:\apps\dist\" 目录的内容。

**发行说明**

请参阅 [HDInsight 发行说明](hdinsight-release-notes.md)，了解 HDInsight 最新版本的更多发行说明。

## <a name="supported-hdinsight-versions"></a>支持的 HDInsight 版本
下表列出当前可用的 HDInsight 版本以及它们使用的相应 Hortonworks 数据平台版本和发布日期。 如果知道，还提供其支持到期日期和弃用日期。 请注意以下事项：

* 默认情况下，会针对 HDInsight 2.1 和更高版本的群集部署具有两个头节点的高度可用群集。 它们不适用于 HDInsight 1.6 群集。
* 某版本的支持到期后，可能不再通过 Azure 门户提供它。 下表列出 Azure 经典门户上提供的版本。 可继续使用 Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) 命令中的 `Version` 参数和 .NET SDK 获取群集版本，直到其弃用日期为止。

| HDInsight 版本 | HDP 版本 | VM OS | 高可用性 | 发布日期 | 在 Azure 门户上提供 | 支持到期日期 | 弃用日期 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.6 预览版 |HDP 2.6 |Ubuntu 16 |是 |02/28/2017 |是 | | |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |是 |2016/9/30 |是 | | |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |是 |03/29/2016 |是 |2016/12/29 |2018/1/9 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS 或 Windows Server 2012R2 |是 |12/02/2015 |是 |06/27/2016 |07/31/2017 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 或 Windows Server 2012R2 |是 |2/18/2015 |是 |3/1/2016 |04/01/2017 |
| HDI 3.1 |HDP 2.1 |Windows Server 2012R2 |是 |6/24/2014 |否 |05/18/2015 |06/30/2016 |
| HDI 3.0 |HDP 2.0 |Windows Server 2012R2 |是 |02/11/2014 |否 |09/17/2014 |06/30/2015 |
| HDI 2.1 |HDP 1.3 |Windows Server 2012R2 |是 |10/28/2013 |否 |05/12/2014 |05/31/2015 |
| HDI 1.6 |HDP 1.1 | |否 |10/28/2013 |否 |04/26/2014 |05/31/2015 |

##<a name="hdi-36-preview-with-apache-spark-21"></a>包含 Apache Spark 2.1 的 HDI 3.6 预览版
HDI 3.6 预览版于 2017 年 2 月 28 日发布。 这已在用于 Apache Spark 2.1 的预览版中提供。 有关更多详细信息，请参阅[包含 Apache Spark 2.1 的 HDI 3.6 预览版](https://azure.microsoft.com/en-us/blog/announcing-preview-of-azure-hdinsight-3-6-with-apache-spark-2-1/)

##<a name="hdi-version-32-and-33-nearing-deprecation-date"></a>HDI 版本 3.2 和 3.3 接近弃用日期
对 HDI 3.2 群集的支持于 03/01/2016 到期，并将于 04/01/2017 弃用。 对 HDI 3.3 群集的支持于 06/27/2016 到期，并将于 07/31/2017 弃用。 如果你有 HDI 3.2 或 3.3 HDI 群集，则请立刻将群集升级到 HDI 3.5（最新版本）。

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 群集版本的服务级别协议
SLA 用“支持窗口”来定义。 “支持窗口”是指 HDInsight 群集版本受 Microsoft 客户服务和支持部门支持的时间段。 如果 HDInsight 群集版本具有早于当前日期的**支持过期日期**，则表示它处于支持窗口外。 有关支持的 HDInsight 群集版本的列表，请参见上表。 给定 HDInsight 版本 X（一旦提供更新的 X+1 版本）的支持到期日期为按以下公式计算所得时间的较晚者：  

* 公式 1：发布 HDInsight 群集版本 X 的日期加 180 天。
* 公式 2：HDInsight 群集版本 X+1（X 之后的后续版本）在门户中可用的日期加 90 天。

**弃用日期**是指在此后不能在 HDInsight 上创建此群集版本的日期。 从 2017 年 7 月 31 日开始，不能在某群集的弃用日期后调整其大小。

> [!NOTE]
> 基于 Windows 的 HDInsight 群集（包括版本 2.1、3.0、3.1、3.2 和 3.3）在 Azure 来宾 OS 系列 4 上运行，该系列使用 64 位版本的 Windows Server 2012 R2 并支持 .NET Framework 4.0、4.5、4.5.1 和 4.5.2。
>
>

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>与 HDInsight 版本相关的 Hortonworks 发行说明
* HDInsight 群集版本 3.4 使用基于 [Hortonworks 数据平台 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) 的 Hadoop 分发版。 这是使用门户时创建的**默认** Hadoop 群集。
* HDInsight 群集版本 3.3 使用基于 [Hortonworks 数据平台 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) 的 Hadoop 分发版。

  * [此处](https://storm.apache.org/2015/11/05/storm0100-released.html)提供了 Apache Storm 发行说明。
  * [此处](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)提供了 Apache Hive 发行说明。
* HDInsight 群集版本 3.2 使用基于 [Hortonworks 数据平台 2.2][hdp-2-2] 的 Hadoop 分发版。  

  * 特定 Apache 组件的发行说明 - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) 和 [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。
* HDInsight 群集版本 3.1 使用基于 [Hortonworks 数据平台 2.1.7][hdp-2-1-7] 的 Hadoop 分发版。创建于 11/7/2014 之前的 HDInsight 3.1 群集基于 [Hortonworks 数据平台 2.1.1][hdp-2-1-1]。
* HDInsight 群集版本 3.0 使用基于 [Hortonworks 数据平台 2.0][hdp-2-0-8] 的 Hadoop 分发版。
* HDInsight 群集版本 2.1 使用基于 [Hortonworks 数据平台 1.3][hdp-1-3-0] 的 Hadoop 分发版。
* HDInsight 群集版本 1.6 使用基于 [Hortonworks 数据平台 1.1][hdp-1-1-0] 的 Hadoop 分发版。

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

