---
title: 在 Azure HDInsight 中将 Azure Data Lake Storage (ADLS) Gen2 用于 Apache Hadoop
description: 概述了 Azure Data Lake Storage Gen2 及其与 Azure HDInsight 协同工作的方式。
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: b7cde3a8990a51e95a8ce9ad85bca524d5669e0c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721115"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>在 Azure HDInsight 中将 Azure Data Lake Storage Gen2 用于 Apache Hadoop

Azure Data Lake Storage (ADLS) Gen2 采用了 Hadoop 兼容文件系统、Azure Active Directory 和基于 POSIX 的 ACL 等 Azure Data Lake Storage Gen1 中的核心功能，并将它们集成到了 Azure Blob 存储中。 此组合使你能够利用 Azure Data Lake Storage Gen1 的性能，同时还能够使用 Blob 存储的分层和数据生命周期管理。

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的核心功能

- Hadoop 兼容访问：使用 Azure Data Lake Storage Gen2，可以像使用 Hadoop 分布式文件系统 (HDFS) 一样管理和访问数据。 ABFS 驱动程序可以在所有的 Apache Hadoop 环境中使用（包括 Azure HDInsight 和 Azure Databricks）以访问存储在 Data Lake Storage Gen2 中的数据。

- POSIX 权限的超集：Data Lake Gen2 的安全模型支持 ACL 和 POSIX 权限，以及特定于 Data Lake Storage Gen2 的一些额外粒度。 可以通过管理工具或 Apache Hive 和 Apache Spark 等框架来配置设置。

- 经济高效：Data Lake Storage Gen2 提供低成本的存储容量和事务。 Azure Blob 存储生命周期等功能可以随数据在其生命周期中的移动来调整计费率，从而帮助降低成本。

- 使用 Blob 存储工具、框架和应用：Data Lake Storage Gen2 可以继续使用目前存在的适用于 Blob 存储的各种工具、框架和应用程序。

- 已优化的驱动程序：ABFS 驱动程序专门针对大数据分析进行了优化。 相应的 REST API 通过 dfs 终结点 dfs.core.windows.net 进行显示。

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2 的新增功能

### <a name="managed-identities-for-secure-file-access"></a>用于安全文件访问的托管标识

Azure HDInsight 使用托管标识来保护对 Azure Data Lake Storage Gen2 中文件的群集访问。 托管标识是 Azure Active Directory 的一项功能，可以为 Azure 服务提供一组自动托管的凭据。 这些凭据可用于对任何支持 AD 身份验证的服务进行身份验证。 不需要将凭据存储在代码或配置文件中即可使用托管标识。

有关详细信息，请参阅[什么是 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="azure-blob-filesystem-abfs-driver"></a>Azure Blob 文件系统 (ABFS) 驱动程序

Apache Hadoop 应用程序会以本机方式从本地磁盘存储读取和写入数据。 ABFS 等 Hadoop 文件系统驱动程序通过模仿针对应用程序的常规 Hadoop 文件系统操作，使 Hadoop 应用程序能够适用于云存储。 驱动程序随即将这些操作转换为实际云存储平台可以理解的操作。

以前，Hadoop 文件系统驱动程序会先将所有文件系统操作转换为针对客户端的 Azure 存储 REST API 调用，然后再调用 REST API。 但是，这种客户端转换会导致针对单个文件系统操作（例如文件重命名）执行多个 REST API 调用。 ABFS 已将部分 Hadoop 文件系统逻辑从客户端移到了服务器端，并且 ADLS Gen2 API 现在将与 Blob API 并行运行。 此迁移提高了性能，因为现在常用的 Hadoop 文件系统操作可以通过一个 REST API 调用来执行。

有关详细信息，请参阅 [Azure Blob 文件系统驱动程序 (ABFS)：用于 Apache Hadoop 的专用 Azure 存储驱动程序](../storage/data-lake-storage/abfs-driver.md)。

### <a name="adls-gen-2-uri-scheme"></a>ADLS Gen 2 URI 方案

ADLS Gen2 使用新的 URI 方案从 HDInsight 访问 Azure 存储中的文件：

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

此 URI 方案提供了 SSL 加密访问（前缀为 `abfss://`）和未加密访问（前缀为 `abfs://`）。 建议尽量使用 `abfss`，即使在访问位于 Azure 中同一区域内的数据时也是如此。

`<FILE_SYSTEM_NAME>` 标识文件系统 Data Lake Storage Gen2 的路径。

`<ACCOUNT_NAME>` 标识 Azure 存储帐户名称。 完全限定域名 (FQDN) 是必需的。

`<PATH>` 是文件或目录 HDFS 路径名。

如果未指定 `<FILE_SYSTEM_NAME>` 和`<ACCOUNT_NAME>` 的值，则会使用默认文件系统。 对于默认文件系统中的文件，可以使用相对路径或绝对路径。 例如，可以使用以下任一路径引用 HDInsight 群集附带的 `hadoop-mapreduce-examples.jar` 文件：

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> 在 HDInsight 版本 2.1 和 1.6 群集中，文件名为 `hadoop-examples.jar`。 在 HDInsight 外部使用文件时，大多数实用程序无法识别 ABFS 格式，应该改用基本的路径格式，如 `example/jars/hadoop-mapreduce-examples.jar`。

有关详细信息，请参阅[使用 Azure Data Lake Storage Gen2 URI](../storage/data-lake-storage/introduction-abfs-uri.md)。

## <a name="next-steps"></a>后续步骤
- [Azure Data Lake Storage Gen2 简介](../storage/data-lake-storage/introduction.md)
- [将 Azure Data Lake Storage Gen2 预览版用于 Azure HDInsight 群集](../storage/data-lake-storage/use-hdi-cluster.md)