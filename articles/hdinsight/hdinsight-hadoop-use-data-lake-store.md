---
title: 在 Azure HDInsight 中配合使用 Data Lake Store 和 Hadoop | Microsoft Docs
description: 了解如何从 Azure Data Lake Store 查询数据并存储分析结果。
keywords: Blob 存储, hdfs, 结构化数据, 非结构化数据, Data Lake Store
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 0d0fb9bad8c6120100ae3ee766aea7620dd6105f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201751"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>配合使用 Data Lake Store 和 HDInsight 群集

若要分析 HDInsight 群集中的数据，可将数据存储在 [Azure 存储](../storage/common/storage-introduction.md)和/或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 中。 这两个存储选项都允许安全地删除用于计算的 HDInsight 群集，而不会丢失用户数据。

本文介绍如何配合使用 Data Lake Store和 HDInsight 群集。 若要了解如何配合使用 Azure 存储和 HDInsight 群集，请参阅[配合使用 Azure 存储和 Azure HDInsight 群集](hdinsight-hadoop-use-blob-storage.md)。 若要深入了解如何创建 HDInsight 群集，请参阅[在 HDInsight 中创建 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

> [!NOTE]
> 因为始终需要通过安全通道访问 Data Lake Store，因此没有 `adls` 文件系统方案名称。 始终使用 `adl`。
> 
> 

## <a name="availabilities-for-hdinsight-clusters"></a>HDInsight 群集的可用性

Hadoop 支持默认文件系统的概念。 默认文件系统意指默认方案和授权。 它还可用于解析相对路径。 在 HDInsight 群集创建过程中，可指定 Azure 存储中的 Blob 容器作为默认文件系统，或者可借助 HDInsight 3.5 和更新版本，选择 Azure 存储或 Azure Data Lake Store 作为默认文件系统，但有一些例外。 

HDInsight 群集可通过以下两种方式使用 Data Lake Store：

* 作为默认存储
* 作为附加存储，将 Azure 存储 Blob 作为默认存储。

目前，只有部分 HDInsight 群集类型/版本支持使用 Data Lake Store 作为默认存储和附加存储帐户：

| HDInsight 群集类型 | 将 Data Lake Store 作为默认存储 | 将 Data Lake Store 作为附加存储| 说明 |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight 版本 3.6 | 是 | 是 | |
| HDInsight 版本 3.5 | 是 | 是 | HBase 除外|
| HDInsight 版本 3.4 | 否 | 是 | |
| HDInsight 版本 3.3 | 否 | 否 | |
| HDInsight 版本 3.2 | 否 | 是 | |
| Storm | | |可使用 Data Lake Store 从 Storm 拓扑写入数据。 对于可由 Storm 拓扑读取的引用数据，也可以使用 Data Lake Store。|

使用 Data Lake Store 作为附加存储帐户不会影响从群集读取或写入数据到 Azure 存储的性能或能力。


## <a name="use-data-lake-store-as-default-storage"></a>将 Data Lake Store 用作默认存储

将 Data Lake Store 作为默认存储对 HDInsight 进行部署时，与群集相关的文件将存储在 Data Lake Store 的以下位置中：

    adl://mydatalakestore/<cluster_root_path>/

其中，`<cluster_root_path>` 是在 Data Lake Store 中创建的文件夹的名称。 通过指定每个群集的根路径，可将同一 Data Lake Store 帐户用于多个群集。 因此可以进行设置，其中：

* Cluster1 可以使用路径 `adl://mydatalakestore/cluster1storage`
* Cluster2 可以使用路径 `adl://mydatalakestore/cluster2storage`

请注意：这两个群集使用的是同一个 Data Lake Store 帐户 **mydatalakestore**。 每个群集都有权访问 Data Lake Store 中其自身的根文件系统。 Azure 门户部署体验将特别提示为根路径使用 **/clusters/\<clustername>** 等文件夹名称。

为了能够使用 Data Lake Store 作为默认存储，必须授予对以下路径的服务主体访问权限：

- Data Lake Store 帐户根。  例如：adl://mydatalakestore/。
- 所有群集文件夹的文件夹。  例如：adl://mydatalakestore/clusters。
- 群集文件夹。  例如：adl://mydatalakestore/clusters/cluster1storage。

若要深入了解如何创建服务主体和授予访问权限，请参阅[配置 Data Lake Store 访问](#configure-data-lake-store-access)。


## <a name="use-data-lake-store-as-additional-storage"></a>将 Data Lake Store 用作附加存储

也可以将 Data Lake Store 用作群集的其他存储。 在这种情况下，群集默认存储可以是 Azure 存储 Blob 或 Data Lake Store 帐户。 如果针对 Data Lake Store（作为附加存储）中存储的数据运行 HDInsight 作业，则必须使用该文件的完全限定的路径。 例如：

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

请注意，当前在 URL 中没有 **cluster_root_path**。 这是因为在此情况下，Data Lake Store 不是默认存储，因此只需提供文件的路径即可。

为了能够使用 Data Lake Store 作为附加存储，只需授予对存储文件的路径位置的服务主体访问权限。  例如：

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

若要深入了解如何创建服务主体和授予访问权限，请参阅[配置 Data Lake Store 访问](#configure-data-lake-store-access)。


## <a name="use-more-than-one-data-lake-store-accounts"></a>使用多个 Data Lake Store 帐户

通过向 HDInsight 群集授予对一个或多个 Data Lake Store 帐户中数据的访问权限，可实现添加 Data Lake Store 帐户作为附加帐户以及添加多个 Data Lake Store 帐户。 请参阅[配置 Data Lake Store 访问](#configure-data-lake-store-access)。

## <a name="configure-data-lake-store-access"></a>配置 Data Lake Store 访问

若要从 HDInsight 群集配置 Data Lake Store 访问，必须具有 Azure Active Directory (Azure AD) 服务主体。 只有 Azure AD 管理员才能创建服务主体。 必须使用证书创建服务主体。 有关详细信息，请参阅[配置 Data Lake Store 访问](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#configure-data-lake-store-access)和[使用自签名证书创建服务主体](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate)。

> [!NOTE]
> 若要将 Azure Data Lake Store 用作 HDInsight 群集的额外存储，则强烈建议你在创建群集时按本文说明进行操作。 向现有的 HDInsight 群集添加 Azure Data Lake Store 作为额外的存储是很复杂的过程，容易出现错误。
>

## <a name="access-files-from-the-cluster"></a>从群集访问文件

可以通过多种方法从 HDInsight 群集访问 Data Lake Store 中的文件。

* **使用完全限定的名称**。 使用此方法时，需要提供要访问的文件的完整路径。

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **使用缩短的路径格式**。 使用此方法时，需将群集根的路径替换为 adl:///。 因此，在以上示例中，可以将 `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` 替换为 `adl:///`。

        adl:///<file path>

* **使用相对路径**。 使用此方法时，仅需提供要访问的文件的相对路径。 例如，如果文件的完整路径为：

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    可以改为使用此相对路径来访问同一 sample.log 文件。

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>创建具有 Data Lake Store 访问权限的 HDInsight 群集

请使用以下链接，详细了解如何创建具有 Data Lake Store 访问权限的 HDInsight 群集。

* [使用门户](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [使用 PowerShell（Data Lake Store 作为默认存储）](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [使用 PowerShell（Data Lake Store 作为其他存储）](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [使用 Azure 模板](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## <a name="next-steps"></a>后续步骤
本文介绍如何将 HDFS 兼容的 Azure Data Lake Store 与 HDInsight 配合使用。 这样可以生成可缩放的长期存档数据采集解决方案，并使用 HDInsight 来解锁所存储结构化和非结构化数据内的信息。

有关详细信息，请参阅：

* [Azure HDInsight 入门][hdinsight-get-started]
* [Azure Data Lake Store 入门](../data-lake-store/data-lake-store-get-started-portal.md)
* [通过 Azure 门户创建使用 Data Lake Store 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [通过 Azure PowerShell 创建使用 Data Lake Store 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [将数据上传到 HDInsight][hdinsight-upload-data]
* [将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]
* [将 Pig 与 HDInsight 配合使用][hdinsight-use-pig]
* [使用 Azure 存储共享访问签名来限制使用 HDInsight 访问数据][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
