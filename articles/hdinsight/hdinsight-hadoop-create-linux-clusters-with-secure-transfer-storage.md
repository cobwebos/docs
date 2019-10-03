---
title: 在 Azure HDInsight 中使用安全传输存储帐户创建 Hadoop 群集
description: 了解如何使用启用安全传输的 Azure 存储帐户创建 HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: d182ca9f74f86922a4d8c94c9b3557f6111b7d53
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098763"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>在 Azure HDInsight 中使用安全传输存储帐户创建 Apache Hadoop 群集

[需要安全传输](../storage/common/storage-require-secure-transfer.md)功能强制提交到帐户的所有请求都通过安全连接来进行，从而增强 Azure 存储帐户的安全性。 仅 HDInsight 群集 3.6 或更高版本支持此功能和 wasbs 方案。

## <a name="prerequisites"></a>先决条件

在开始阅读本文前，必须具备：

* **Azure 订阅**：若要创建一个月的免费试用帐户，请访问 [azure.microsoft.com/free](https://azure.microsoft.com/free)。
* 启用安全传输的 Azure 存储帐户。 有关说明，请参阅[创建存储帐户](../storage/common/storage-quickstart-create-account.md)和[需要安全传输](../storage/common/storage-require-secure-transfer.md)。
* 存储帐户中的 Blob 容器。

## <a name="create-cluster"></a>创建群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

本部分介绍如何使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-template-deploy.md)在 HDInsight 中创建 Hadoop 群集。 该模板位于 [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/) 中。 学习本文不需要有资源管理器模板方面的经验。 有关其他群集创建方法以及了解本文中使用的属性，请参阅[创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。

1. 单击以下映像以登录到 Azure，并在 Azure 门户中打开 Resource Manager 模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 按说明遵循以下规范创建群集： 

    - 指定 HDInsight 版本 3.6。 3\.6 或更高版本是必需的。
    - 指定启用安全传输的存储帐户。
    - 对存储帐户使用短名称。
    - 必须事先创建存储帐户和 blob 容器。

      有关说明，请参阅[创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。

如果使用脚本操作来提供自己的配置文件，则必须在以下设置中使用 wasbs：

- fs.defaultFS (core-site)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>添加其他存储帐户

可以通过多个选项添加其他启用安全传输的存储帐户：

- 修改上一部分的 Azure 资源管理器模板。
- 使用 [Azure 门户](https://portal.azure.com)创建一个群集，并指定关联的存储帐户。
- 使用脚本操作，将其他启用安全传输的存储帐户添加到现有的 HDInsight 群集。 有关详细信息，请参阅[将其他存储帐户添加到 HDInsight](hdinsight-hadoop-add-storage.md)。

## <a name="next-steps"></a>后续步骤
本文介绍了如何创建 HDInsight 群集，以及如何启用到存储帐户的安全传输。

有关如何使用 HDInsight 分析数据的详细信息，请参阅以下文章：

* 若要详细了解如何将 [Apache Hive](https://hive.apache.org/) 与 HDInsight 配合使用（包括如何从 Visual Studio 中执行 Hive 查询），请参阅[将 Apache Hive 与 HDInsight 配合使用][hdinsight-use-hive]。
* 若要了解 [Apache Pig](https://pig.apache.org/)（一种用于转换数据的语言），请参阅[将 Apache Pig 与 HDInsight 配合使用][hdinsight-use-pig]。
* 若要了解 [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)（在 Hadoop 中处理数据的程序编写方式），请参阅[将 Apache Hadoop MapReduce 与 HDInsight 配合使用][hdinsight-use-mapreduce]。
* 若要了解如何使用用于 Visual Studio 的 HDInsight 工具来分析 HDInsight 数据，请参阅[开始使用 HDInsight 的 Visual Studio Apache Hadoop 工具](hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

若要详细了解如何通过 HDInsight 来存储数据，或者如何将数据导入 HDInsight，请参阅以下文章：

* 有关 HDInsight 如何使用 Azure 存储的信息，请参阅[将 Azure 存储与 HDInsight 配合使用](hdinsight-hadoop-use-blob-storage.md)。
* 若要了解如何将数据上传到 HDInsight，请参阅[将数据上传到 HDInsight][hdinsight-upload-data]。

若要详细了解如何创建或管理 HDInsight 群集，请参阅以下文章：

* 要了解如何管理基于 Linux 的 HDInsight 群集，请参阅[使用 Apache Ambari 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。
* 有关可用于创建 HDInsight 群集的选项的详细信息，请参阅 [Creating HDInsight on Linux using custom options](hdinsight-hadoop-provision-linux-clusters.md)（使用自定义选项在 Linux 上创建 HDInsight）。
* 如果已熟悉 Linux 和 Apache Hadoop，但想要了解有关 HDInsight 上的 Hadoop 的具体信息，请参阅[在 Linux 上使用 HDInsight](hdinsight-hadoop-linux-information.md)。 此文提供了如下所述信息：

  * 群集上托管的服务（例如 [Apache Ambari](https://ambari.apache.org/) 和 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)）的 URL
  * [Apache Hadoop](https://hadoop.apache.org/) 文件和示例在本地文件系统上的位置
  * 使用 Azure 存储 (WASB) 而非 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) 作为默认数据存储

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
