---
title: 使用命令行创建 Hadoop 群集 - Azure HDInsight
description: 了解如何使用跨平台 Azure CLI 1.0 创建 HDInsight 群集。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: 7185e492a2cc42835ce1fa7043a963c7d5d0afb4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594791"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>使用 Azure CLI 创建 HDInsight 群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

本文介绍了使用 Azure CLI 1.0 创建 HDInsight 3.5 群集的相关步骤。

> [!IMPORTANT]
> 本主题介绍如何使用 Azure CLI 1.0 创建 HDInsight 群集。 此版本的 CLI 已弃用，并且对创建 HDInsight 群集的支持尚未添加到 Azure CLI 2.0。
>
> 也可以使用 Azure PowerShell 来创建和管理 HDInsight 群集。 有关详细信息，请参阅[使用 Azure PowerShell 创建 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)文档。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* **Azure CLI**。 本文档中的步骤最近已使用 Azure CLI 版本 0.10.14 进行测试。

    > [!IMPORTANT]
    > Azure CLI 1.0 已弃用，并且对创建 HDInsight 群集的支持尚未添加到 Azure CLI 2.0。

## <a name="log-in-to-your-azure-subscription"></a>登录到 Azure 订阅

按照 [从 Azure 命令行接口 (Azure CLI) 连接到 Azure 订阅](/cli/azure/authenticate-azure-cli) 中所述的步骤，使用 **登录** 方法连接到订阅。

## <a name="create-a-cluster"></a>创建群集

应从命令行（例如 PowerShell 或 Bash）执行以下步骤。

1. 使用以下命令对 Azure 订阅进行身份验证：

        azure login

    系统会提示提供用户名和密码。 如果有多个 Azure 订阅，可以使用 `azure account set <subscriptionname>` 来设置 Azure CLI 命令使用的订阅。

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

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](hdinsight-administer-use-portal-linux.md#create-clusters)。

## <a name="next-steps"></a>后续步骤

使用 Azure CLI 成功创建 HDInsight 群集后，请参考以下主题来了解如何使用群集：

### <a name="hadoop-clusters"></a>Hadoop 群集

* [将 Hive 与 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase 群集

* [HBase on HDInsight 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [为 HBase on HDInsight 开发 Java 应用程序](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 群集

* [为 Storm on HDInsight 开发 Java 拓扑](storm/apache-storm-develop-java-topology.md)
* [在 Storm on HDInsight 中使用 Python 组件](storm/apache-storm-develop-python-topology.md)
* [使用 Storm on HDInsight 部署和监视拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)
