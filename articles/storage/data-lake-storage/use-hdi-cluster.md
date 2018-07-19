---
title: 在 Azure HDInsight 群集上使用 Azure Data Lake Storage Gen2 预览版
description: 了解如何从 Azure Data Lake Storage Gen2 预览版查询数据，以及存储分析结果。
keywords: hdfs,结构化数据,非结构化数据,data lake store,Hadoop 输入,Hadoop 输出, hadoop 存储, hdfs 输入,hdfs 输出,hdfs 存储,wasb azure
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: jamesbak
manager: jahogg
ms.component: data-lake-storage-gen2
ms.service: storage
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: e4e72d3c5b6b9c96de6a8faafccfcc39fe3c37b8
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136683"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>在 Azure HDInsight 群集上使用 Azure Data Lake Storage Gen2 预览版

若要分析 HDInsight 群集中的数据，可将数据存储在 Azure 存储、Azure Data Lake Storage Gen1 和Azure Data Lake Storage Gen2 预览版的任意组合中。 使用所有存储选项都能安全地删除用于计算的 HDInsight 群集，而不会丢失用户数据。

Hadoop 支持默认文件系统的概念。 默认文件系统意指默认方案和授权。 它还可用于解析相对路径。 在 HDInsight 群集创建过程中，可以指定 Azure 存储中的 Blob 容器或者 Azure Data Lake Storage 作为默认文件系统。 或者在 HDInsight 3.5 中，可以选择 Azure 存储或者 Azure Data Lake Storage 作为默认文件系统，但存在几种例外情况。

本文介绍如何在 HDInsight 群集上使用 Azure Data Lake Storage Gen2。 有关如何创建 HDInsight 群集的详细信息，请参阅[将 Azure Data Lake Storage 与 Hadoop、Spark、Kafka 等配合使用来设置 HDInsight 群集](quickstart-create-connect-hdi-cluster.md)。

Azure 存储是一种稳健、通用的存储解决方案，它与 HDInsight 无缝集成。 HDInsight 可将 Azure Data Lake Storage 用作群集的默认文件系统。 通过 Hadoop 分布式的文件系统 (HDFS) 界面，可以针对 Azure Data Lake Storage 中的文件直接运行 HDInsight 中的整套组件。

建议不要使用默认文件系统来存储业务数据。 良好的做法是每次使用之后删除默认文件系统以降低存储成本。 请注意，默认容器包含应用程序日志和系统日志。 请确保在删除该容器之前检索日志。

不支持多个群集共享一个文件系统。

## <a name="hdinsight-storage-architecture"></a>HDInsight 存储体系结构

下图提供了使用 Azure 存储时的 HDInsight 存储体系结构的抽象视图：

![Hadoop 群集使用 HDFS API 来访问 Blob 存储中的结构化和非结构化数据，并在其中存储这些数据。](./media/use-hdi-cluster/HDI.ABFS.Arch.png "HDInsight 存储体系结构")

HDInsight 提供对在本地附加到计算节点的分布式文件系统的访问。 可使用完全限定 URI 访问该文件系统，例如：

    hdfs://<NAME_NODE_HOST>/<PATH>

另外，HDInsight 支持访问在 Azure Data Lake Storage 中存储的数据。 语法为：

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

以下是将 Azure 存储帐户与 HDInsight 群集配合使用时的一些注意事项。

* **连接到群集的存储帐户中的文件**包含创建群集过程中与群集关联的帐户名称和密钥。 此配置可让你拥有对文件系统中的文件的完全访问权限。

* **未连接到群集的存储帐户中的公共文件**公开对文件系统中的文件的只读权限。
  
  > [!NOTE]
  > 使用公共文件系统可以获取文件系统中提供的所有文件的列表和访问元数据。 如果使用公共文件系统，仅当你知道确切的 URL 时才可访问文件。 有关详细信息，请参阅[限制对容器和 Blob 的访问](http://msdn.microsoft.com/library/windowsazure/dd179354.aspx)（容器和 Blob 的规则同样适用于文件和文件系统）。
 
* **未连接到群集的存储帐户中的专用文件系统**不允许访问文件系统中的文件，除非在提交 WebHCat 作业时定义了存储帐户。 本文稍后将解释此项限制的原因。

创建过程中定义的存储帐户及其密钥存储在群集节点上的 %HADOOP_HOME%/conf/core-site.xml 中。 HDInsight 的默认行为是使用 core-site.xml 文件中定义的存储帐户。 可以使用 [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md) 修改此设置

多个 WebHCat 作业，包括 Hive、MapReduce、Hadoop 流和 Pig，都可以带有存储帐户和元数据的说明。 （此方法目前对带有存储帐户的 Pig 有效，但对元数据无效。）有关详细信息，请参阅[将 HDInsight 群集与备用存储帐户和元存储配合使用](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

## <a id="benefits"></a>Azure 存储的优点

通过在 Azure 区域的存储帐户资源附近创建计算群集，使计算节点能够通过高速网络高效地访问 Azure 存储中的数据，从而减少了非并置计算群集和存储资源所产生的隐含性能成本。

在 Azure 存储而非 HDFS 中存储数据有几个好处：

* **数据重用和共享：** HDFS 中的数据位于计算群集内。 仅有权访问计算群集的应用程序才能通过 HDFS API 使用数据。 Azure 存储中的数据可通过 HDFS API 或 [Blob 存储 REST API][blob-storage-restAPI] 访问。 因此，可使用大量应用程序（包括其他 HDInsight 群集）和工具来生成和使用此类数据。

* **数据存档：** 通过在 Azure 存储中存储数据，可以安全地删除用于计算的 HDInsight 群集而不会丢失用户数据。

* **数据存储成本：** 与在 Azure 存储中存储数据相比，在本机 HDFS 中长期存储数据的成本更高，因为计算群集的成本高于 Azure 存储的成本。 此外，由于数据无需在每次生成计算群集时重新加载，也节省了数据加载成本。

* **弹性向外扩展：** 尽管 HDFS 提供了向外扩展文件系统，但缩放由你为群集创建的节点数量决定。 与依靠自动获得的 Azure 存储的弹性缩放功能相比，更改缩放的过程可能会更复杂。

* **异地复制：** 可对 Azure 存储数据进行异地复制。 尽管这种能力可提供地理恢复和数据冗余功能，但支持故障转移到异地复制位置将大大影响性能，并且可能会产生额外成本。 因此，请谨慎选择异地复制，并仅在数据的价值值得支付额外成本时才选择。

* **数据生命周期管理：** 任何文件系统中的所有数据都经历自己的生命周期。 数据通常开始非常有价值且经常被访问，过渡到不那么有价值且需要较少访问，并最终需要存档或删除。 Azure 存储提供数据分层和生命周期管理策略，可根据数据的生命周期阶段相应地将数据分层。

某些 MapReduce 作业和包可能会产生中间结果，并不想在 Azure 存储中存储这些结果。 在此情况下，仍可以选择将数据存储在本地 HDFS 中。 实际上，HDInsight 在 Hive 作业和其他进程中使用本机 HDFS 实现（称为 DFS）来处理其中某些中间结果。

> [!NOTE]
> 大多数 HDFS 命令（例如 `ls`、`copyFromLocal` 和 `mkdir`）仍按预期工作。 只有特定于 DFS 的命令（例如 `fschk` 和 `dfsadmin`）在 Azure 存储中会显示不同的行为。

## <a name="create-an-data-lake-storage-file-system"></a>创建 Data Lake Storage 文件系统

若要使用文件系统，请先创建 [Azure 存储帐户][azure-storage-create]。 在此过程中，可指定在其中创建存储帐户的 Azure 区域。 群集和存储帐户必须位于同一区域。 Hive 元存储 SQL Server 数据库和 Oozie 元存储 SQL Server 数据库也必须位于同一区域。

无论所创建的每个 Blob 位于何处，它都属于 Azure Data Lake Storage 帐户中的某个文件系统。 

默认的 Data Lake Storage 文件系统存储群集特定的信息，例如作业历史记录和日志。 请不要在多个 HDInsight 群集之间共享默认的 Data Lake Storage 文件系统。 这可能会损坏作业历史记录。 建议对每个群集使用不同的文件系统，并将共享数据放入在所有相关群集的部署中指定的链接存储帐户，而不是放入默认存储帐户。 有关配置链接存储帐户的详细信息，请参阅[创建 HDInsight 群集][hdinsight-creation]。 但是，在删除原始的 HDInsight 群集后，可以重用默认存储文件系统。 对于 HBase 群集，可以通过使用已删除的 HBase 群集使用的默认 Blob 容器创建新的 HBase 群集，从而保留 HBase 表架构和数据。

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>使用 Azure 门户

从门户创建 HDInsight 群集时，可以使用相应的选项（如以下屏幕截图所示）提供存储帐户详细信息。 还可以指定是否要将其他存储帐户与该群集相关联，如果是，请为其他存储选择任何可用存储选项。

![HDInsight hadoop 创建数据源](./media/use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> 不支持在 HDInsight 群集之外的其他位置使用别的存储帐户。

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

如果[已安装并配置 Azure PowerShell][powershell-install]，可在 Azure PowerShell 提示符下使用以下代码创建存储帐户和容器：

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "WEST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
      -Name StorageAccountName `
      -Location $Location `
      -SkuName Standard_LRS `
      -Kind StorageV2 
      -HierarchialNamespace $True

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

> [!NOTE]
> 创建容器类似于在 Azure Data Lake Storage 中创建文件系统。

### <a name="use-azure-cli"></a>使用 Azure CLI

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

如果 [已安装并配置 Azure CLI](../../cli-install-nodejs.md)，则以下命令可以用于存储帐户和容器。

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> 在 Data Lake Storage Gen2 公共预览版推出期间，仅支持 `--sku Standard_LRS`。

系统会提示指定创建存储帐户的地理区域。 在打算创建 HDInsight 群集的同一区域中创建存储帐户。

创建存储帐户后，使用以下命令检索存储帐户密钥：

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

若要创建容器，请使用以下命令：

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> 创建容器类似于在 Azure Data Lake Storage 中创建文件系统。

## <a name="address-files-in-azure-storage"></a>确定 Azure 存储中文件的地址

用于从 HDInsight 访问 Azure 存储中的文件的 URI 方案为：

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>

URI 方案提供了使用 *abfs:* 前缀的未加密访问和使用 *abfss* 的 SSL 加密访问。 建议尽量使用 *abfss* ，即使在访问位于同一 Azure 区域内的数据时也是如此。

* &lt;FILE_SYSTEM_NAME&gt; 标识文件系统 Azure Data Lake Storage 的路径。
* &lt;ACCOUNT_NAME&gt; 标识 Azure 存储帐户名。 完全限定域名 (FQDN) 是必需的。

    如果 &lt;FILE_SYSTEM_NAME&gt; 和 &lt;ACCOUNT_NAME&gt; 的值都未指定，则使用默认文件系统。 对于默认文件系统中的文件，可以使用相对路径或绝对路径。 例如，可以使用以下任一路径引用随 HDInsight 群集提供的 *hadoop-mapreduce-examples.jar* 文件：
    
        abfs://myfilesystempath@myaccount.dfs.core.widows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> 在 HDInsight 版本 2.1 和 1.6 群集中，文件名是 *hadoop-examples.jar*。

* &lt;PATH&gt; 是文件或目录 HDFS 路径名。

> [!NOTE]
> 在 HDInsight 外部使用文件时，大多数实用程序无法识别 ABFS 格式，应该改用基本的路径格式，如 `example/jars/hadoop-mapreduce-examples.jar`。
 
## <a name="use-additional-storage-accounts"></a>使用其他存储帐户

创建 HDInsight 群集时，可以指定要与其关联的 Azure 存储帐户。 除了此存储帐户外，在创建过程中或群集创建完成后，还可以从同一 Azure 订阅或不同 Azure 订阅添加其他存储帐户。 有关添加其他存储帐户的说明，请参阅[创建 HDInsight 群集](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。

> [!WARNING]
> 不支持在 HDInsight 群集之外的其他位置使用别的存储帐户。

## <a name="next-steps"></a>后续步骤

本文介绍如何将与 HDFS 兼容的 Azure 存储和 HDInsight 配合使用。 此方法可以生成可缩放的长期存档数据采集解决方案，并使用 HDInsight 来解锁所存储结构化和非结构化数据内的信息。

有关详细信息，请参阅：

* [适用于 Azure Data Lake Storage Gen2 的 ABFS Hadoop 文件系统驱动程序](abfs-driver.md)
* [Azure Data Lake Storage 简介](introduction.md)
* [配合 Hadoop、Spark、Kafka 等使用 Azure Data Lake Storage 创建 HDInsight 群集](quickstart-create-connect-hdi-cluster.md)
* [使用 distcp 将数据引入 Azure Data Lake Storage](use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/use-hdi-cluster/HDI.PowerShell.BlobCommands.png
