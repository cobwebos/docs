---
title: 比较用于与 Azure HDInsight 群集配合使用的存储选项
description: 概述存储类型及其如何使用 Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 320b8f948d08e46c43085e174dfbe838f44bac79
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479164"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比较用于与 Azure HDInsight 群集配合使用的存储选项

你可以选择几个不同的 Azure 存储服务时创建 HDInsight 群集：

* Azure 存储
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

本文概述了这些存储类型和其独特功能。

下表总结了使用不同版本的 HDInsight 支持的 Azure 存储服务：

| 存储服务 | 帐户类型 | Namespace 类型 | 支持的服务 | 支持的性能层 | 支持的访问层 | HDInsight 版本 | 群集类型 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 常规用途 V2 | 分层 （文件系统） | Blob | 标准 | 热、 冷、 存档 | 3.6+ | 全部 |
|Azure 存储| 常规用途 V2 | Object | Blob | 标准 | 热、 冷、 存档 | 3.6+ | 全部 |
|Azure 存储| 常规用途 V1 | Object | Blob | 标准 | 不适用 | 全部 | 全部 |
|Azure 存储| Blob 存储 * * | Object | 块 blob | 标准 | 热、 冷、 存档 | 全部 | 全部 |
|Azure Data Lake Storage Gen1| 不适用 | 分层 （文件系统） | 不适用 | 不适用 | 不适用 | 仅 3.6 | 除 HBase |

* * 对于 HDInsight 群集，只有辅助存储帐户可以是类型 BlobStorage。

有关 Azure 存储帐户类型的详细信息，请参阅[Azure 存储帐户概述](../storage/common/storage-account-overview.md)

有关 Azure 存储访问层的详细信息，请参阅[Azure Blob 存储：高级 （预览版）、 热、 冷和存档存储层](../storage/blobs/storage-blob-storage-tiers.md)

可以创建使用主要和可选的辅助存储服务的不同组合的群集。 下表汇总了目前支持在 HDInsight 中的群集存储配置：

| HDInsight 版本 | 主存储 | 辅助存储 | 支持 |
|---|---|---|---|
| 3.6 & 4.0 | 常规用途 V1、 常规用途 V2 | 常规用途 V1、 常规用途 V2、 BlobStorage (块 Blob) | 是 |
| 3.6 & 4.0 | 常规用途 V1、 常规用途 V2 | Data Lake Storage Gen2 | 否 |
| 3.6 & 4.0 | 常规用途 V1、 常规用途 V2 | Data Lake Storage Gen1 | 是 |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | 是 |
| 3.6 & 4.0 | Data Lake Storage Gen2* | 常规用途 V1、 常规用途 V2、 BlobStorage (块 Blob) | 是 |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 否 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 是 |
| 3.6 | Data Lake Storage Gen1 | 常规用途 V1、 常规用途 V2、 BlobStorage (块 Blob) | 是 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 否 |
| 4.0 | Data Lake Storage Gen1 | 任意 | 否 |

* =，只要它们是相同的托管的标识用于群集访问权限的所有安装程序，这可能是一个或多个数据湖存储第 2 代帐户。

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>在 Azure HDInsight 中将 Azure Data Lake Storage Gen2 用于 Apache Hadoop

Azure Data Lake Storage Gen2 采用了 Azure Data Lake Storage Gen1 中的核心功能，并将它们集成到了 Azure Blob 存储中。 这些功能包括与 Hadoop 兼容的文件系统、Azure Active Directory (Azure AD) 和基于 POSIX 的访问控制列表 (ACL)。 此组合使你能够利用 Azure Data Lake Storage Gen1 的性能，同时还能够使用 Blob 存储的分层和数据生命周期管理。

有关 Azure Data Lake Storage Gen2 的详细信息，请参阅 [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)。

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的核心功能

* **与 Hadoop 兼容的访问：** 在 Azure Data Lake Storage Gen2 中，可以像使用 Hadoop 分布式文件系统 (HDFS) 一样管理和访问数据。 Azure Blob 文件系统 (ABFS) 驱动程序可以在所有 Apache Hadoop 环境中使用（包括 Azure HDInsight 和 Azure Databricks）。 使用 ABFS 可以访问存储在 Data Lake Storage Gen2 中的数据。

* **POSIX 权限的超集：** Data Lake Gen2 的安全模型支持 ACL 和 POSIX 权限，以及特定于 Data Lake Storage Gen2 的一些额外粒度。 可以通过管理工具或 Apache Hive 和 Apache Spark 等框架来配置设置。

* **成本效益：** Data Lake Storage Gen2 提供低成本的存储容量和事务。 Azure Blob 存储生命周期等功能可以随数据在其生命周期中的移动来调整计费率，从而帮助降低成本。

* **与 Blob 存储工具、框架和应用兼容：** Data Lake Storage Gen2 可以继续使用适用于 Blob 存储的各种工具、框架和应用程序。

* **优化的驱动程序：** ABFS 驱动程序专门针对大数据分析进行了优化。 相应的 REST API 通过分布式文件系统 (DFS) 终结点 dfs.core.windows.net 进行显示。

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 的新增功能

#### <a name="managed-identities-for-secure-file-access"></a>用于安全文件访问的托管标识

Azure HDInsight 使用托管标识来保护对 Azure Data Lake Storage Gen2 中文件的群集访问。 托管标识是 Azure Active Directory 的一项功能，可以为 Azure 服务提供一组自动托管的凭据。 这些凭据可用于对任何支持 Active Directory 身份验证的服务进行身份验证。 不需要将凭据存储在代码或配置文件中即可使用托管标识。

有关详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

#### <a name="azure-blob-file-system-driver"></a>Azure Blob 文件系统驱动程序

Apache Hadoop 应用程序会以本机方式从本地磁盘存储读取和写入数据。 ABFS 等 Hadoop 文件系统驱动程序通过模仿常规 Hadoop 文件系统操作，使 Hadoop 应用程序能够使用云存储。 驱动程序将从应用程序收到的这些命令转换为实际云存储平台可以理解的操作。

以前，Hadoop 文件系统驱动程序会先将所有文件系统操作转换为针对客户端的 Azure 存储 REST API 调用，然后再调用 REST API。 但是，这种客户端转换会导致针对单个文件系统操作（例如文件重命名）执行多个 REST API 调用。 ABFS 已将部分 Hadoop 文件系统逻辑从客户端移到了服务器端。 Azure Data Lake Storage Gen2 API 现在将与 Blob API 并行运行。 此迁移提高了性能，因为现在常用的 Hadoop 文件系统操作可以通过一个 REST API 调用来执行。

有关详细信息，请参阅 [Azure Blob 文件系统驱动程序 (ABFS)：用于 Hadoop 的专用 Azure 存储驱动程序](../storage/blobs/data-lake-storage-abfs-driver.md)。

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 的 URI 方案 

Azure Data Lake Storage Gen2 使用新的 URI 方案从 HDInsight 访问 Azure 存储中的文件：

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

此 URI 方案提供了 SSL 加密访问（前缀为 `abfss://`）和未加密访问（前缀为 `abfs://`）。 请尽量使用 `abfss`，即使在访问位于 Azure 中同一区域内的数据时也是如此。

`<FILE_SYSTEM_NAME>` 标识文件系统 Data Lake Storage Gen2 的路径。

`<ACCOUNT_NAME>` 标识 Azure 存储帐户名称。 完全限定域名 (FQDN) 是必需的。

`<PATH>` 是文件或目录 HDFS 路径名。

如果未指定 `<FILE_SYSTEM_NAME>` 和`<ACCOUNT_NAME>` 的值，则会使用默认文件系统。 对于默认文件系统中的文件，可以使用相对路径或绝对路径。 例如，可以使用以下任一路径引用 HDInsight 群集附带的 `hadoop-mapreduce-examples.jar` 文件：

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> 在 HDInsight 版本 2.1 和 1.6 群集中，文件名为 `hadoop-examples.jar`。 在 HDInsight 外部使用文件时，你会发现大多数实用程序无法识别 ABFS 格式，应该改用基本的路径格式，如 `example/jars/hadoop-mapreduce-examples.jar`。

有关详细信息，请参阅[使用 Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)。

## <a name="azure-storage"></a>Azure 存储

Azure 存储是一种稳健、通用的存储解决方案，它与 HDInsight 无缝集成。 HDInsight 可将 Azure 存储中的 Blob 容器用作群集的默认文件系统。 HDInsight 中的整套组件可以通过 HDFS 界面直接操作以 Blob 形式存储的结构化或非结构化数据。

我们建议使用单独的存储容器的默认群集存储和业务数据，来隔离的 HDInsight 日志和从你自己的业务数据的临时文件。 我们还建议删除默认 blob 容器，其中包含应用程序和系统日志之后每次使用，以降低存储成本。 请确保在删除该容器之前检索日志。

如果选择在“选定网络”上通过“防火墙和虚拟网络”限制来保护存储帐户的安全，   请务必启用例外“允许受信任的 Microsoft 服务...”，  这样 HDInsight 就能访问存储帐户。

### <a name="hdinsight-storage-architecture"></a>HDInsight 存储体系结构

下图提供了 Azure 存储的 HDInsight 存储体系结构的抽象视图：

![显示 Hadoop 群集使用 HDFS API 来访问 Blob 存储中的结构化和非结构化数据，并在其中存储这些数据的示意图](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight 存储体系结构")

HDInsight 提供对在本地附加到计算节点的分布式文件系统的访问。 可使用完全限定 URI 访问该文件系统，例如：

    hdfs://<namenodehost>/<path>

通过 HDInsight 还可以访问 Azure 存储中的数据。 语法如下：

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

将 Azure 存储帐户与 HDInsight 群集配合使用时，请注意以下原则：

* **已连接到群集的存储帐户中的容器：** 由于在创建过程中帐户名称和密钥将与群集相关联，因此，对这些容器中的 Blob 具有完全访问权限。

* **未连接到群集的存储帐户中的公用容器或公用 Blob：**  你对容器中的 blob 具有只读权限。
  
  > [!NOTE]  
  > 利用公共容器，可以获得该容器中可用的所有 Blob 的列表以及容器元数据。 利用公共 Blob，仅在知道正确 URL 时才可访问 Blob。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../storage/blobs/storage-manage-access-to-resources.md)。

* **未连接到群集的存储帐户中的专用容器：**  你无法访问这些容器中的 Blob，除非在提交 WebHCat 作业时定义了存储帐户。 

创建过程中定义的存储帐户及其密钥存储在群集节点上的 %HADOOP/_HOME%/conf/core-site.xml 中。 HDInsight 默认使用 core-site.xml 文件中定义的存储帐户。 可以使用 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 修改此设置。

多个 WebHCat 作业，包括 Apache Hive、MapReduce、Apache Hadoop 流和 Apache Pig，都可以带有存储帐户和元数据的说明。 （它目前对带有存储帐户的 Pig 有效，但对元数据无效。）有关详细信息，请参阅[将 HDInsight 群集与备用存储帐户和元存储配合使用](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 可用于结构化和非结构化数据。 Blob 容器将数据存储为键值对，没有目录层次结构。 不过，键名称可以包含斜杠字符 (/)，使其看起来像存储在目录结构中的文件。 例如，Blob 的键可以是 `input/log1.txt`。 不存在实际的 `input` 目录，但由于键名称中包含斜杠字符，键看起来像一个文件路径。

### <a id="benefits"></a>Azure 存储的优点
未共置在一起的计算群集和存储资源存在隐含的性能成本。 通过在 Azure 区域中的存储帐户资源附近创建计算群集可以减少这些成本。 在此区域中，计算节点可以通过高速网络高效访问 Azure 存储中的数据。

在 Azure 存储而非 HDFS 中存储数据可带来多项优势：

* **数据重用和共享：** HDFS 中的数据位于计算群集内。 仅有权访问计算群集的应用程序才能通过 HDFS API 使用数据。 相比之下，Azure 存储中的数据可通过 HDFS API 或 Blob 存储 REST API 进行访问。 因此，可以使用更多的应用程序（包括其他 HDInsight 群集）和工具来生成和使用此类数据。

* **数据存档：** 在 Azure 存储中存储数据后，可以安全地删除用于计算的 HDInsight 群集而不会丢失用户数据。

* **数据存储成本：** 与在 Azure 存储中存储数据相比，在 DFS 中长期存储数据的成本更高，因为计算群集的成本高于 Azure 存储的成本。 此外，由于数据无需在每次生成计算群集时重新加载，也节省了数据加载成本。

* **弹性横向扩展：** 尽管 HDFS 提供了横向扩展文件系统，但具体的扩展由你为群集创建的节点数量决定。 与依靠自动获得的 Azure 存储的弹性缩放功能相比，更改规模可能会更复杂。

* **异地复制：** 可对 Azure 存储进行异地复制。 尽管异地复制可提供地理恢复和数据冗余功能，但针对异地复制位置的故障转移将大大影响性能，并且可能会产生额外成本。 因此，请谨慎选择异地复制，并仅在数据的价值值得支付额外成本时才选择它。

某些 MapReduce 作业和包可能会产生中间结果，并不想在 Azure 存储中存储这些结果。 在此情况下，仍可以选择将数据存储在本地 HDFS 中。 HDInsight 在 Hive 作业和其他过程中会为其中某些中间结果使用 DFS。

> [!NOTE]  
> 大多数 HDFS 命令（例如 `ls`、`copyFromLocal` 和 `mkdir`）在 Azure 存储中可按预期方式工作。 只有特定于本机 HDFS 实现（称作 DFS）的命令在 Azure 存储上会显示不同的行为，例如 `fschk` 和 `dfsadmin`。

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 概述

Azure Data Lake Storage Gen1 是一个企业范围的超大规模存储库，适用于大数据分析工作负载。 使用 Azure Data Lake 可以在一个位置捕获任何大小、类型和引入速度的数据进行操作和探索分析。

使用与 WebHDFS 兼容的 REST API，可以从 Hadoop（HDInsight 群集提供）访问 Data Lake Storage Gen1。 Data Lake Storage Gen1 专为分析存储数据而设计，并已针对数据分析方案的性能做了优化。 它现成地包含了现实企业用例不可或缺的功能。 这些功能包括安全性、可管理性、可伸缩性、可靠性和可用性。

有关 Azure Data Lake Storage Gen1 的详细信息，请参阅 [Azure Data Lake Storage Gen1 概述](../data-lake-store/data-lake-store-overview.md)。

下面介绍了 Data Lake Storage Gen1 的重要功能。

### <a name="compatibility-with-hadoop"></a>与 Hadoop 兼容

Data Lake Storage Gen1 是一个 Apache Hadoop 文件系统，该系统与 HDFS 兼容并与 Hadoop 生态系统相互协作。  采用 WebHDFS API 的现有 HDInsight 应用程序或服务可以轻松与 Data Lake Storage Gen1 集成。 Data Lake Storage Gen1 还为应用程序公开了 WebHDFS 兼容的 REST 接口。

使用 Hadoop 分析框架（例如 MapReduce 或 Hive），可以轻松分析 Data Lake Storage Gen1 中存储的数据。 可将 Azure HDInsight 群集预配和配置为直接访问 Data Lake Storage Gen1 中存储的数据。

### <a name="unlimited-storage-petabyte-files"></a>无限存储空间，PB 量级的文件

Data Lake Storage Gen1 提供无限存储空间，适合用于存储各种分析数据。 帐户大小、文件大小或 Data Lake 中可存储的数据量均无任何限制。 Data Lake Storage Gen1 支持 KB 到 PB 量级的单个文件大小，非常适合用于存储任何类型的数据。 通过创建多个副本来长期存储数据，数据在 Data Lake 中的存储时间长短没有限制。

### <a name="performance-tuning-for-big-data-analytics"></a>针对大数据分析优化了性能

Data Lake Storage Gen1 旨在运行需要利用超大吞吐量查询和分析海量数据的大规模分析系统。 Data Lake 将文件的各个部分散在多个独立的存储服务器中。 分数数据时，此设置可以改善并行读取文件时的读取吞吐量。

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>企业就绪：高度可用且安全

Data Lake Storage Gen1 提供符合行业标准的可用性和可靠性。 数据资产可通过创建冗余副本来长期存储，防范意外的故障。 企业可以在其解决方案中使用 Data Lake Storage Gen1 作为现有数据平台的重要组成部分。

Data Lake Storage Gen1 还为存储的数据提供企业级安全性。 有关详细信息，请参阅 [保护 Azure Data Lake Storage Gen1 中的数据](#DataLakeStoreSecurity)。

### <a name="flexible-data-structures"></a>弹性数据结构

Data Lake Storage Gen1 可按本机格式（原样）存储任何数据，不需要事先经过转换。 加载数据之前，Data Lake Storage Gen1 不需要定义架构。 独立的分析框架在分析时会解释数据并定义架构。 Data Lake Storage Gen1 能够存储任意大小和格式的文件，因此可以处理结构化、半结构化和非结构化数据。

Data Lake Storage Gen1 的数据容器本质上是文件夹和文件。 可以使用 SDK、Azure 门户和 Azure PowerShell 来操作存储的数据。 只要使用这些接口和相应容器将数据放入存储，就能存储任何类型的数据。 Data Lake Storage Gen1 不会根据其存储的数据类型对数据执行任何特殊处理。

## <a name="DataLakeStoreSecurity"></a>Data Lake Storage Gen1 中的数据安全性
Data Lake Storage Gen1 使用 Azure Active Directory 进行身份验证，使用访问控制列表 (ACL) 管理对数据的访问。

| **功能** | **说明** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 与 Azure Active Directory (Azure AD) 集成，可对 Data Lake Storage Gen1 中存储的所有数据进行标识与访问管理。 因为这种集成，Data Lake Storage Gen1 可受益于所有 Azure AD 功能。 这些功能包括多重身份验证、条件访问、基于角色的访问控制、应用程序使用情况监视、安全监视和警报，等等。 Data Lake Storage Gen1 支持在 REST 接口中使用 OAuth 2.0 协议进行身份验证。 参阅[使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 身份验证](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)。|
| 访问控制 |Data Lake Storage Gen1 通过支持 WebHDFS 协议公开的 POSIX 样式权限来提供访问控制。 可对根文件夹、子文件夹和单个文件启用 ACL。 有关 ACL 在 Data Lake Storage Gen1 上下文中的工作原理的详细信息，请参阅 [Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 还针对帐户中存储的数据提供加密。 创建 Data Lake Storage Gen1 帐户时可以指定加密设置。 可以选择加密或不加密数据。 有关详细信息，请参阅 [Data Lake Storage Gen1 中的加密](../data-lake-store/data-lake-store-encryption.md)。 有关如何提供加密相关配置的说明，请参阅[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md)。 |

若要详细了解如何保护 Data Lake Storage Gen1 中的数据，请参阅[保护 Azure Data Lake Storage Gen1 中存储的数据](../data-lake-store/data-lake-store-secure-data.md)。

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>与 Data Lake Storage Gen1 兼容的应用程序
Data Lake Storage Gen1 与 Hadoop 生态系统中的大多数开源组件兼容。 此外，还与其他 Azure 服务完美集成。  请访问以下链接，详细了解 Data Lake Storage Gen1 如何与开源组件及其他 Azure 服务配合使用。

* 有关可与 Data Lake Storage Gen1 互操作的开源应用程序列表，请参阅[可与 Azure Data Lake Storage Gen1 配合工作的开源大数据应用程序](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)。
* 参阅[将 Azure Data Lake Storage Gen1 与其他 Azure 服务集成](../data-lake-store/data-lake-store-integrate-with-other-services.md)，了解如何将 Data Lake Storage Gen1 与其他 Azure 服务配合使用，以实现更多的方案。
* 参阅[使用 Azure Data Lake Storage Gen1 来满足大数据要求](../data-lake-store/data-lake-store-data-scenarios.md)，了解如何在引入数据、处理数据、下载数据和可视化数据等方案中使用 Data Lake Storage Gen1。

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 文件系统 (adl://)
在 Hadoop 环境（在 HDInsight 群集上提供）中，可以通过新文件系统 AzureDataLakeFilesystem (adl://) 访问 Data Lake Storage Gen1。 可以通过目前无法在 WebHDFS 中实现的方式来优化使用 adl:// 的应用程序和服务的性能。 因此，使用 Data Lake Storage Gen1 时，可以灵活使用建议的 adl:// 获得最佳性能，或继续直接使用 WebHDFS API 维护现有代码。 Azure HDInsight 充分使用 AzureDataLakeFilesystem 来提供 Data Lake Storage Gen1 的最佳性能。

可以使用以下 URL 访问 Data Lake Storage Gen1 中的数据：

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

有关如何访问 Data Lake Storage Gen1 中的数据的详细信息，请参阅[可针对存储的数据执行的操作](../data-lake-store/data-lake-store-get-started-portal.md#properties)。



## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 存储简介](../storage/common/storage-introduction.md)
