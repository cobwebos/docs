---
title: 将 Azure 数据存储从第 1 代迁移到第 2 代
description: 将 Azure 数据存储从第 1 代迁移到第 2 代。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 80c0afafca3b0bf497689cbd4a0870eedd066cfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677144"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>将 Azure 数据存储从第 1 代迁移到第 2 代

您可以将数据、工作负载和应用程序从数据存储存储第 1 代迁移到数据湖存储 Gen2。

Azure 数据存储第 2 代基于[Azure Blob 存储](storage-blobs-introduction.md)构建，并提供一组专用于大数据分析的功能。 [数据存储库 Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/)结合了[Azure 数据存储库 Gen1](https://docs.microsoft.com/azure/data-lake-store/index)的功能，如文件系统语义、目录和文件级安全性，以及[Azure Blob 存储](storage-blobs-introduction.md)的低成本、分层存储、高可用性/灾难恢复功能。

> [!NOTE]
> 为了便于阅读，本文使用术语*Gen1*引用 Azure 数据存储第 1 代，术语*Gen2*引用 Azure 数据存储库第 2 代。

## <a name="recommended-approach"></a>推荐的方法

要迁移到 Gen2，我们建议采用以下方法。

：heavy_check_mark：步骤1：评估准备情况

：heavy_check_mark：步骤 2：准备迁移

：heavy_check_mark：步骤 3：迁移数据和应用程序工作负载

：heavy_check_mark：第4步：从第1代到第2代的分后

> [!NOTE]
> Gen1 和 Gen2 是不同的服务，没有就地升级体验，需要有意迁移。 

### <a name="step-1-assess-readiness"></a>第 1 步：评估就绪情况

1. 了解[数据存储湖存储第 2 代产品](https://azure.microsoft.com/services/storage/data-lake-storage/)/它的好处，成本和一般架构。 

2. 将第 1 代的功能与第 2 代[的功能进行比较](#gen1-gen2-feature-comparison)。 

3. 查看[已知问题](data-lake-storage-known-issues.md)列表，以评估功能上的任何差距。

4. Gen2 支持 Blob 存储功能，如[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](storage-blob-storage-tiers.md)和[Blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)。 如果您有兴趣使用这些功能中的任何一个，请查看[当前的支持级别](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)。

5. 查看[Azure 生态系统支持的](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access)当前状态，以确保 Gen2 支持解决方案所依赖的任何服务。

### <a name="step-2-prepare-to-migrate"></a>第 2 步：准备迁移

1. 标识要迁移的数据集。

   借此机会清理不再使用的数据集。 除非您计划一次迁移所有数据，否则请借此机会确定可以分阶段迁移的逻辑数据组。
   
2. 确定迁移对您的业务的影响。

   例如，考虑在迁移进行期间，您是否承受任何停机时间。 这些注意事项可以帮助您确定合适的迁移模式，并选择最合适的工具。

3. 创建迁移计划。 

   我们建议这些[迁移模式](#migration-patterns)。 您可以选择这些模式之一，将它们组合在一起，或设计您自己的自定义模式。

### <a name="step-3-migrate-data-workloads-and-applications"></a>第 3 步：迁移数据、工作负载和应用程序

使用您喜欢的模式迁移数据、工作负载和应用程序。 我们建议您以增量方式验证方案。

1. [创建存储帐户](data-lake-storage-quickstart-create-account.md)并启用分层命名空间功能。 

2. 迁移数据。 

3. 在[工作负荷中配置服务](data-lake-storage-integrate-with-azure-services.md)以指向 Gen2 终结点。 
   
4. 更新应用程序以使用 Gen2 API。 请参阅[.NET](data-lake-storage-directory-file-acl-dotnet.md)、Java、Python、JavaScript 和[REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)的指南。 [Java](data-lake-storage-directory-file-acl-java.md) [Python](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md) 
   
5. 更新脚本以使用数据存储湖存储 Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md)和[Azure CLI 命令](data-lake-storage-directory-file-acl-cli.md)。
   
6. 搜索包含代码文件中字符串`adl://`的 URI 引用，或在 Databricks 笔记本、Apache Hive HQL 文件或用作工作负载一部分的任何其他文件中。 将这些引用替换为新存储帐户的[Gen2 格式化 URI。](data-lake-storage-introduction-abfs-uri.md) 例如：第 1 代`adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile`URI：`abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`可能变为 。 

7. 将帐户上的安全性配置为包括[基于角色的访问控制 （RBAC） 角色](../common/storage-auth-aad-rbac-portal.md)、[文件和文件夹级安全性](data-lake-storage-access-control.md)以及 Azure[存储防火墙和虚拟网络](../common/storage-network-security.md)。

### <a name="step-4-cutover-from-gen1-to-gen2"></a>第 4 步：从第 1 代到第 2 代的分距

在确信应用程序和工作负载在 Gen2 上保持稳定后，您可以开始使用 Gen2 来满足您的业务方案。 关闭在 Gen1 上运行的任何剩余管道，并停用您的第一代帐户。 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>第 1 代 vs 第 2 代功能

下表将第一代的功能与第 2 代的功能进行比较。

|区域 |Gen1   |Gen2 |
|---|---|---|
|数据组织|[分层命名空间](data-lake-storage-namespace.md)<br>文件和文件夹支持|[分层命名空间](data-lake-storage-namespace.md)<br>容器、文件和文件夹支持 |
|异地冗余| [组](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage)， [ZRS](../common/storage-redundancy.md#zone-redundant-storage)， [GRS](../common/storage-redundancy.md#geo-redundant-storage)， [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|身份验证|[AAD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)<br>[共享访问密钥](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|授权|管理 - [RBAC](../../role-based-access-control/overview.md)<br>数据 + [ACL](data-lake-storage-access-control.md)|管理与[RBAC](../../role-based-access-control/overview.md)<br>数据 - [ACL](data-lake-storage-access-control.md)， [RBAC](../../role-based-access-control/overview.md) |
|加密 = 静态数据|服务器端 — 使用[Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)或[客户管理的](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)密钥|服务器端 — 使用[Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)或[客户管理的](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)密钥|
|VNET 支持|[VNET 集成](../../data-lake-store/data-lake-store-network-security.md)|[服务终结点](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，[专用终结点](../common/storage-private-endpoints.md)|
|开发人员体验|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md)， [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md)， [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md)， [Python](../../data-lake-store/data-lake-store-data-operations-python.md)，[电源外壳](../../data-lake-store/data-lake-store-get-started-powershell.md)， [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|一般可用 - [REST](/rest/api/storageservices/data-lake-storage-gen2)， [.NET](data-lake-storage-directory-file-acl-dotnet.md)， [Java](data-lake-storage-directory-file-acl-java.md)， [Python](data-lake-storage-directory-file-acl-python.md)<br>公共预览 - [JavaScript](data-lake-storage-directory-file-acl-javascript.md)，[电源外壳](data-lake-storage-directory-file-acl-powershell.md)， [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|诊断日志|经典日志<br>[集成 Azure 监视器](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[经典日志](../common/storage-analytics-logging.md)- 一般可用<br>Azure 监视器集成 + 时间线 TBD|
|生态系统|[HDInsight （3.6）](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)， [Azure 数据砖 （3.1 及以上）](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html)， [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)， [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight （3.6， 4.0）](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)， [Azure 数据砖 （5.1 及以上）](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2)， [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)， [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>第 1 代到第 2 代模式

选择迁移模式，然后根据需要修改该模式。

|||
|---|---|
|**提升和换档**|最简单的模式。 如果数据管道能够承受停机时间，则是理想的选择。|
|**增量复制**|类似于*提升和换档*，但停机时间较少。 非常适合复制时间更长的大量数据。|
|**双管道**|非常适合承受任何停机时间的管道。|
|**双向同步**|类似于*双管道*，但采用更分阶段的方法，适用于更复杂的管道。|

让我们仔细看看每种模式。
 
### <a name="lift-and-shift-pattern"></a>提升和换档模式

这是最简单的模式。

1. 停止对 Gen1 的所有写入。

2. 将数据从第 1 代移动到第 2 代。 我们建议[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 随数据一起复制。

3. 将引入操作和工作负载指向第 2 代。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![提升和换档模式](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>使用提升和换档模式的注意事项

：heavy_check_mark：同时将所有工作负载从第 1 代划合到第 2 代。

：heavy_check_mark：在迁移和缩短期间预计停机。

：heavy_check_mark：非常适合提供停机时间且所有应用可以同时升级的管道。

### <a name="incremental-copy-pattern"></a>增量复制模式

1. 开始将数据从第 1 代移动到第 2 代。 我们建议[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 随数据一起复制。

2. 增量复制来自 Gen1 的新数据。

3. 复制所有数据后，停止对 Gen1 的所有写入，并将工作负载指向 Gen2。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![增量复制模式](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>使用增量复制模式的注意事项：

：heavy_check_mark：同时将所有工作负载从第 1 代划合到第 2 代。

：heavy_check_mark：仅在缩短期间预计停机。

：heavy_check_mark：非常适合所有应用同时升级的管道，但数据副本需要更多时间。

### <a name="dual-pipeline-pattern"></a>双管道模式

1. 将数据从第 1 代移动到第 2 代。 我们建议[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 随数据一起复制。

2. 将新数据引入第 1 代和第 2 代。

3. 将工作负载指向第 2 代。

4. 停止对第 1 代的所有写入，然后停用第 1 代。

> [!div class="mx-imgBorder"]
> ![双管道模式](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>使用双管道模式的注意事项：

：heavy_check_mark：第 1 代和第二代管道并排运行。

：heavy_check_mark：支持零停机时间。

：heavy_check_mark：在工作负载和应用程序无法承受任何停机时间，并且您可以引入两个存储帐户的情况下，是理想的选择。

### <a name="bi-directional-sync-pattern"></a>双向同步模式

1. 设置第 1 代和 Gen2 之间的双向复制。 我们建议[万迪斯科](https://docs.wandisco.com/bigdata/wdfusion/adls/)。 它为现有数据提供了修复功能。

3. 完成所有移动后，停止对 Gen1 的所有写入并关闭双向复制。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![双向图案](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>使用双向同步模式的注意事项：

：heavy_check_mark：非常适合涉及大量管道和依赖关系的复杂方案，其中分阶段方法可能更有意义。  

：heavy_check_mark：迁移工作量很高，但它为第一代和第二代提供了并行支持。

## <a name="next-steps"></a>后续步骤

- 了解为存储帐户设置安全性的各个部分。 请参阅[Azure 存储安全指南](../common/storage-security-guide.md)。
- 优化数据湖存储的性能。 有关[性能，请参阅优化 Azure 数据存储第 2 代](data-lake-storage-performance-tuning-guidance.md)
- 查看管理数据湖存储的最佳做法。 请参阅[使用 Azure 数据存储第 2 代的最佳做法](data-lake-storage-best-practices.md)

