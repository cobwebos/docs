---
title: 将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2
description: 将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 245bcac81189ac8aa63f81fbe4ed30655a457bc8
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371832"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2

您可以将数据、工作负荷和应用程序从 Data Lake Storage Gen1 迁移到 Data Lake Storage Gen2 中。

Azure Data Lake Storage Gen2 是在[Azure Blob 存储](storage-blobs-introduction.md)基础之上构建的，提供了一组专用于大数据分析的功能。 [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/)结合了[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)中的功能，例如文件系统语义、目录和文件级别安全性，并通过[Azure Blob 存储](storage-blobs-introduction.md)中的低成本、分层存储、高可用性/灾难恢复功能进行缩放。

> [!NOTE]
> 为了便于阅读，本文使用了术语*Gen1*来指代 Azure Data Lake Storage Gen1，术语*Gen2*引用 Azure Data Lake Storage Gen2。

## <a name="recommended-approach"></a>推荐的方法

若要迁移到 Gen2，我们建议采用以下方法。

： heavy_check_mark：步骤1：评估准备情况

： heavy_check_mark：步骤2：准备迁移

： heavy_check_mark：步骤3：迁移数据和应用程序工作负荷

： heavy_check_mark：步骤4：从 Gen1 转换为 Gen2

> [!NOTE]
> Gen1 和 Gen2 是不同的服务，没有就地升级体验，需要进行有意的迁移工作。 

### <a name="step-1-assess-readiness"></a>步骤1：评估准备情况

1. 了解[Data Lake Storage Gen2 产品/服务](https://azure.microsoft.com/services/storage/data-lake-storage/);这是一种优势、成本和一般的体系结构。 

2. 将 Gen1 的功能与 Gen2 的[功能进行比较](#gen1-gen2-feature-comparison)。 

3. 查看[已知问题](data-lake-storage-known-issues.md)的列表，以评估功能的任何差距。

4. Gen2 支持 Blob 存储功能，如[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](storage-blob-storage-tiers.md)和[blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)。 如果你在使用其中任何功能时感兴趣，请查看[当前支持级别](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-feature-support)。

5. 查看[Azure 生态系统支持](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-ecosystem-support)的当前状态，以确保 Gen2 支持你的解决方案所依赖的任何服务。

### <a name="step-2-prepare-to-migrate"></a>步骤2：准备迁移

1. 确定要迁移的数据集。

   利用此机会清理不再使用的数据集。 除非你计划一次迁移所有数据，否则请花些时间来确定可以分阶段迁移的逻辑数据组。
   
2. 确定迁移对你的业务造成的影响。

   例如，请考虑在迁移发生时是否可以承受任何停机时间。 这些注意事项可帮助你确定适当的迁移模式，并选择最合适的工具。

3. 创建迁移计划。 

   建议采用这些[迁移模式](#migration-patterns)。 您可以选择其中一种模式，将它们组合在一起，或者设计自己的自定义模式。

### <a name="step-3-migrate-data-workloads-and-applications"></a>步骤3：迁移数据、工作负荷和应用程序

使用你喜欢的模式迁移数据、工作负荷和应用程序。 建议增量验证方案。

1. [创建存储帐户](data-lake-storage-quickstart-create-account.md)并启用分层命名空间功能。 

2. 迁移数据。 

3. 将[工作负荷中的服务](data-lake-storage-integrate-with-azure-services.md)配置为指向 Gen2 终结点。 
   
4. 更新应用程序以使用 Gen2 Api。 请参阅适用于[.net](data-lake-storage-directory-file-acl-dotnet.md)、 [Java](data-lake-storage-directory-file-acl-java.md)、 [Python](data-lake-storage-directory-file-acl-python.md)、 [JavaScript](data-lake-storage-directory-file-acl-javascript.md)和[REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)的指南。 
   
5. 更新脚本以使用[PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md)Data Lake Storage Gen2 和[Azure CLI 命令](data-lake-storage-directory-file-acl-cli.md)。
   
6. 在 Databricks 笔记本中搜索包含字符串 `adl://` 的 URI 引用，或搜索在工作负荷中使用的 Apache Hive HQL 文件或任何其他文件。 将这些引用替换为新存储帐户的[Gen2 格式的 URI](data-lake-storage-introduction-abfs-uri.md) 。 例如： Gen1 URI： `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` 可能会成为 `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`。 

7. 配置帐户的安全性，以包括[基于角色的访问控制（RBAC）角色](../common/storage-auth-aad-rbac-portal.md)、[文件和文件夹级别的安全性](data-lake-storage-access-control.md)、 [Azure 存储防火墙和虚拟网络](../common/storage-network-security.md)。

### <a name="step-4-cutover-from-gen1-to-gen2"></a>步骤4：从 Gen1 切换到 Gen2

确信您的应用程序和工作负荷在 Gen2 上稳定后，就可以开始使用 Gen2 来满足您的业务方案。 关闭 Gen1 上运行的任何其他管道，并解除 Gen1 帐户的授权。 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 vs Gen2 功能

此表将 Gen1 的功能与 Gen2 的功能进行比较。

|区域 |Gen1   |Gen2 |
|---|---|---|
|数据组织|[分层命名空间](data-lake-storage-namespace.md)<br>文件和文件夹支持|[分层命名空间](data-lake-storage-namespace.md)<br>容器、文件和文件夹支持 |
|异地冗余| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage)、 [ZRS](../common/storage-redundancy.md#zone-redundant-storage)、 [GRS](../common/storage-redundancy.md#geo-redundant-storage)、 [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentication|[AAD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)<br>[共享访问密钥](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|授权|管理- [RBAC](../../role-based-access-control/overview.md)<br>数据- [acl](data-lake-storage-access-control.md)|管理- [RBAC](../../role-based-access-control/overview.md)<br>数据- [acl](data-lake-storage-access-control.md)， [RBAC](../../role-based-access-control/overview.md) |
|加密–静态数据|服务器端-与[服务管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#microsoft-managed-keys)的或[客户托管](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault)的密钥|服务器端-与[服务管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#microsoft-managed-keys)的或[客户托管](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault)的密钥|
|VNET 支持|[VNET 集成](../../data-lake-store/data-lake-store-network-security.md)|[服务终结点](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，[专用终结点（公共预览版）](../common/storage-private-endpoints.md)|
|开发人员体验|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md)、 [.net](../../data-lake-store/data-lake-store-data-operations-net-sdk.md)、 [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md)、 [Python](../../data-lake-store/data-lake-store-data-operations-python.md)、 [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md) [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|[REST](https://review.docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)、 [.net](/data-lake-storage-directory-file-acl-dotnet.md)、 [Java](data-lake-storage-directory-file-acl-java.md)、 [Python](data-lake-storage-directory-file-acl-python.md)、 [JavaScript](data-lake-storage-directory-file-acl-javascript.md)、 [PowerShell](data-lake-storage-directory-file-acl-powershell.md) [Azure CLI](data-lake-storage-directory-file-acl-cli.md) （公共预览版）|
|诊断日志|经典日志<br>[Azure Monitor 集成](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[经典日志](../common/storage-analytics-logging.md)（公共预览版）<br>Azure monitor 集成–时间线 TBD|
|生态系统|[HDInsight （3.6）](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)， [Azure Databricks （3.1 及更高版本）](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html)， [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)， [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight （3.6，4.0）](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)， [Azure Databricks （5.1 及更高版本）](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2)， [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)， [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Gen1 到 Gen2 模式

选择迁移模式，并根据需要修改该模式。

|||
|---|---|
|**直接迁移**|最简单的模式。 如果数据管道可以承受停机，则这是理想之选。|
|**增量复制**|类似于*抬起和移位*，但停机时间更少。 适用于需要较长时间进行复制的大量数据。|
|**双管道**|适用于不能承受停机的管道。|
|**双向同步**|与*双管道*类似，但具有更分阶段的方法，适用于更复杂的管道。|

让我们进一步了解每个模式。
 
### <a name="lift-and-shift-pattern"></a>抬起和移位模式

这是最简单的模式。

1. 停止所有写入 Gen1。

2. 将数据从 Gen1 移动到 Gen2。 建议[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 Acl 与数据一起复制。

3. 将操作和工作负荷点引入 Gen2。

4. 停止 Gen1。

> [!div class="mx-imgBorder"]
> ![抬起和移位模式](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>使用提升和移位模式的注意事项

： heavy_check_mark：同时针对所有工作负荷从 Gen1 转换为 Gen2。

： heavy_check_mark：迁移期间应停机，在转换期间会发生故障。

： heavy_check_mark：适用于可承受停机时间的管道，并且所有应用程序可一次升级。

### <a name="incremental-copy-pattern"></a>增量复制模式

1. 开始将数据从 Gen1 移动到 Gen2。 建议[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 Acl 与数据一起复制。

2. 以增量方式复制 Gen1 中的新数据。

3. 复制所有数据后，停止所有写入到 Gen1，并将工作负荷指向 Gen2。

4. 停止 Gen1。

> [!div class="mx-imgBorder"]
> ![增量复制模式](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>使用增量复制模式的注意事项：

： heavy_check_mark：同时针对所有工作负荷从 Gen1 转换为 Gen2。

： heavy_check_mark：仅在转换期间应停机。

： heavy_check_mark：适用于一次升级所有应用的管道，但数据复制需要更多时间。

### <a name="dual-pipeline-pattern"></a>双管道模式

1. 将数据从 Gen1 移动到 Gen2。 建议[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 Acl 与数据一起复制。

2. 将新数据引入 Gen1 和 Gen2。

3. 将工作负荷指向 Gen2。

4. 停止所有写入到 Gen1，并解除 Gen1 的授权。

> [!div class="mx-imgBorder"]
> ![双管道模式](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>使用双重管道模式的注意事项：

： heavy_check_mark： Gen1 和 Gen2 管道并行运行。

： heavy_check_mark：支持零停机时间。

： heavy_check_mark：理想情况下，在工作负荷和应用程序无法承受任何停机的情况下，你可以引入这两个存储帐户。

### <a name="bi-directional-sync-pattern"></a>双向同步模式

1. 设置 Gen1 和 Gen2 之间的双向复制。 建议[WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)。 它为现有数据提供一项修复功能。

3. 完成所有移动后，停止所有写入 Gen1 并关闭双向复制。

4. 停止 Gen1。

> [!div class="mx-imgBorder"]
> ![双向模式](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>使用双向同步模式的注意事项：

： heavy_check_mark：适用于涉及大量管道和依赖项的复杂方案，其中分阶段方法可能更有意义。  

： heavy_check_mark：迁移工作很高，但它提供对 Gen1 和 Gen2 的并行支持。

## <a name="next-steps"></a>后续步骤

- 了解为存储帐户设置安全性的各个部分。 请参阅[Azure 存储安全指南](../common/storage-security-guide.md)。
- 优化 Data Lake Store 的性能。 请参阅[优化 Azure Data Lake Storage Gen2 性能](data-lake-storage-performance-tuning-guidance.md)
- 查看管理 Data Lake Store 的最佳实践。 请参阅[使用 Azure Data Lake Storage Gen2 的最佳实践](data-lake-storage-best-practices.md)

