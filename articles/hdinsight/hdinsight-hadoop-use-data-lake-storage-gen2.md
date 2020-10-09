---
title: 配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集
description: 了解如何将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 4ef53b2249f8ce57255c13126c9310f1c889d64f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855049"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 是一种专用于在 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)基础上进行大数据分析的云存储服务。 Data Lake Storage Gen2 将 Azure Blob 存储和 Azure Data Lake Storage Gen1 的功能组合在一起。 生成的服务提供了 Azure Data Lake Storage Gen1 中的功能，其中包括：文件系统语义、目录级别和文件级安全性以及适应性。 以及 Azure Blob 存储的低成本、分层存储、高可用性和灾难恢复功能。

有关使用 Data Lake Storage Gen2 的群集创建选项的完整比较，请参阅 [比较用于 Azure HDInsight 群集的存储选项](hdinsight-hadoop-compare-storage-options.md)。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

Data Lake Storage Gen2 能够以默认存储和附加存储帐户的形式用作几乎所有 Azure HDInsight 群集类型的存储选项。 但是，HBase 只能有一个具有 Data Lake Storage Gen2 的帐户。

> [!Note]  
> 选择 "Data Lake Storage Gen2 作为 **主存储类型**后，将无法选择 Data Lake Storage Gen1 作为附加存储。

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>使用 Data Lake Storage Gen2 创建 HDInsight 群集

使用以下链接获取有关如何创建具有对 Data Lake Storage Gen2 的访问权限的 HDInsight 群集的详细说明。

* [使用门户](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [使用 Azure CLI](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* 目前不支持 PowerShell 创建具有 Azure Data Lake Storage Gen2 的 HDInsight 群集。

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 中 Data Lake Storage Gen2 的访问控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支持哪些类型的权限？

Data Lake Storage Gen2 使用一个支持基于角色的访问控制 (RBAC) 和类似于 POSIX 的访问控制列表 (ACL) 的访问控制模型。 Data Lake Storage Gen1 仅支持用于控制数据访问的访问控制列表。

RBAC 使用角色分配有效地将权限集应用到 Azure 资源的用户、组和服务主体。 通常，这些 Azure 资源会被限制为顶级资源 (例如，Azure Blob 存储帐户) 。 对于 Azure Blob 存储，以及 Data Lake Storage Gen2，此机制已扩展到文件系统资源。

有关使用 RBAC 的文件权限的详细信息，请参阅 azure [RBAC)  (azure 基于角色的访问控制 ](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)。

有关使用 ACL 分配文件权限的详细信息，请参阅[对文件和目录应用访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>如何在 Data Lake Storage Gen2 中控制对数据的访问？

HDInsight 群集在 Data Lake Storage Gen2 中访问文件的能力通过托管标识进行控制。 托管标识是在 Azure Active Directory (Azure AD) 中注册的标识，其凭据由 Azure 管理。 利用托管标识，无需在 Azure AD 中注册服务主体。 或维护凭据，如证书。

Azure 服务有两种类型的托管标识：系统分配的托管标识和用户分配的托管标识。 HDInsight 使用用户分配的托管标识来访问 Data Lake Storage Gen2。 `user-assigned managed identity`作为独立的 Azure 资源创建。 在创建过程中，Azure 会在由所用订阅信任的 Azure AD 租户中创建一个标识。 在创建标识后，可以将标识分配到一个或多个 Azure 服务实例。

用户分配标识的生命周期与它所分配到的 Azure 服务实例的生命周期是分开管理的。 有关托管标识的详细信息，请参阅[什么是 Azure 资源托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>如何设置 Azure AD 用户的权限，以使用 Hive 或其他服务在 Data Lake Storage Gen2 中查询数据？

若要为用户设置权限以查询数据，请将 Azure AD 安全组用作 ACL 中分配的主体。 不要直接向单个用户或服务主体分配文件访问权限。 使用 AD 安全组来控制权限流时，可以添加和删除用户或服务主体，而无需将 ACL 重新应用到整个目录结构。 只需要从相应的 Azure AD 安全组添加或删除用户。 ACL 不可继承，因此，重新应用 ACL 需要更新针对每个文件和子目录应用的 ACL。

## <a name="access-files-from-the-cluster"></a>从群集访问文件

可以通过多种方法从 HDInsight 群集访问 Data Lake Storage Gen2 中的文件。

* **使用完全限定的名称**。 使用此方法时，需提供要访问的文件的完整路径。

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **使用缩短的路径格式**。 使用此方法时，需将群集根的路径替换为：

    ```
    abfs:///<file.path>/
    ```

* **使用相对路径**。 使用此方法时，仅需提供要访问的文件的相对路径。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>数据访问示例

示例基于到群集的头节点的 [ssh 连接](./hdinsight-hadoop-linux-use-ssh-unix.md)。 示例使用所有三个 URI 方案。 将 `CONTAINERNAME` 和 `STORAGEACCOUNT` 替换为相关值

#### <a name="a-few-hdfs-commands"></a>几个 hdfs 命令

1. 在本地存储上创建一个文件。

    ```bash
    touch testFile.txt
    ```

1. 在群集存储上创建目录。

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 将数据从本地存储复制到群集存储。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 列出群集存储上的目录内容。

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>创建 Hive 表

但为了便于说明，显示了三个文件位置。 实际执行时，仅使用 `LOCATION` 条目之一。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 与 Data Lake Storage Gen2 预览版集成 - ACL 和安全更新](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [教程：在 Azure HDInsight 中使用交互式查询提取、转换和加载数据](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)