---
title: 使用 Azure 经典 CLI 创建 Apache Hadoop 群集 - Azure HDInsight
description: 了解如何使用跨平台 Azure 经典 CLI 创建 HDInsight 群集。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: c9b1f3570ae3f9b945c87c2ce316c3f72e32b4ad
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792044"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>使用 Azure 经典 CLI 创建 HDInsight 群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

本文介绍了使用 Azure 经典 CLI 创建 HDInsight 3.5 群集的相关步骤。

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* **Azure 经典 CLI**。 本文档中的步骤最近已使用 Azure 经典 CLI 版本 0.10.14 进行测试。

## <a name="log-in-to-your-azure-subscription"></a>登录到 Azure 订阅

按照 [从 Azure 命令行接口连接到 Azure 订阅](/cli/azure/authenticate-azure-cli) 中所述的步骤，使用“登录”方法连接到订阅。

## <a name="create-a-cluster"></a>创建群集

应从命令行（例如 PowerShell 或 Bash）执行以下步骤。

1. 使用以下命令对 Azure 订阅进行身份验证：

        azure login

    系统会提示提供用户名和密码。 如果有多个 Azure 订阅，可以使用 `azure account set <subscriptionname>` 来设置经典 CLI 命令使用的订阅。

2. 使用以下命令切换到 Azure 资源管理器模式：

        azure config mode arm

3. 创建资源组。 此资源组包含 HDInsight 群集和关联的存储帐户。

        azure group create groupname location

    * 将 `groupname` 替换为组的唯一名称。

    * 将 `location` 替换为要在其中创建该组的地理区域。

       有关有效位置的列表，请使用 `azure location list` 命令，然后使用 `Name` 列中的位置之一。

4. 创建存储帐户。 此存储帐户将用作 HDInsight 群集的默认存储。

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * 将 `groupname` 替换为上一步中创建的组的名称。

    * 将 `location` 替换为上一步中使用的同一位置。

    * 将 `storagename` 替换为存储帐户的唯一名称。

        > [!NOTE]  
        > 有关此命令中使用参数的详细信息，请使用 `azure storage account create -h` 查看此命令的帮助。

5. 检索用于访问存储帐户的密钥。

        azure storage account keys list -g groupname storagename

    * 将 `groupname` 替换为资源组名称。
    * 将 `storagename` 替换为存储帐户的名称。

      在返回的数据中，保存 `key1` 的 `key` 值。

6. 创建 HDInsight 群集。

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * 将 `groupname` 替换为资源组名称。

    * 将 `Hadoop` 替换为希望创建的群集类型。 例如，`Hadoop`、`HBase`、`Kafka`、`Spark` 或 `Storm`。

      > [!IMPORTANT]  
      > HDInsight 群集具有各种不同的类型，与该群集进行优化的工作负荷或技术相对应。 没有任何方法支持创建组合多种类型的群集，如一个群集同时具有 Storm 和 HBase 类型。

    * 将 `location` 替换为上一步中使用的同一位置。

    * 将 `storagename` 替换为存储帐户名称。

    * 将 `storagekey` 替换为上一步中获取的密钥。

    * 对于 `--defaultStorageContainer` 参数，请使用为群集使用的同一个名称。

    * 将 `admin` 和 `httppassword` 替换为通过 HTTPS 访问群集时要使用的用户名和密码。

    * 将 `sshuser` 和 `sshuserpassword` 替换为通过 SSH 访问群集时要使用的用户名和密码

      > [!IMPORTANT]  
      > 此示例创建一个具有两个辅助角色节点的群集。 也可以在创建群集后，通过执行缩放操作更改辅助节点数。 如果计划使用 32 个以上的辅助节点，则必须选择至少具有 8 个核心和 14 GB RAM 的头节点大小。 可以在创建群集的过程中使用 `--headNodeSize` 参数设置头节点大小。
      >
      > 有关节点大小和相关费用的详细信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。
      
      可能需要几分钟时间才能完成群集创建过程。 通常大约为 15 分钟。

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>后续步骤

使用经典 CLI 成功创建 HDInsight 群集后，请参考以下主题来了解如何使用群集：

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 群集

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 群集

* [HDInsight 中的 Apache HBase 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [为 Apache HBase on HDInsight 开发 Java 应用程序](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 群集

* [为 Apache Storm on HDInsight 开发 Java 拓扑](storm/apache-storm-develop-java-topology.md)
* [在 Apache Storm on HDInsight 中使用 Python 组件](storm/apache-storm-develop-python-topology.md)
* [使用 Apache Storm on HDInsight 部署和监视拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)
