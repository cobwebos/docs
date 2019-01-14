---
title: 将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2 预览版
description: 升级解决方案以使用 Azure Data Lake Storage Gen2 预览版
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/19/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9cdad2f2e56d7c6dfdfb4a3d46a8094c8aead5ac
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631560"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2-preview"></a>将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2 预览版

如果在大数据分析解决方案中使用 Azure Data Lake Storage Gen1，本指南可以帮助升级这些解决方案以使用 Azure Data Lake Storage Gen2 预览版。 可以使用此文档来评估解决方案对 Data Lake Storage Gen1 的依赖关系。 本指南还介绍了如何计划和执行升级。

我们将帮助你完成以下任务：

:heavy_check_mark:评估升级就绪情况

:heavy_check_mark:规划升级

:heavy_check_mark:执行升级

## <a name="assess-your-upgrade-readiness"></a>评估升级就绪情况

我们的目标是，Data Lake Storage Gen1 中存在的所有功能在 Data Lake Storage Gen2 都可用。 这些功能在 SDK 和 CLI 等中的公开方式在 Data Lake Storage Gen1 和 Data Lake Storage Gen2 之间可能有所不同。 与 Data Lake Storage Gen1 配合使用的应用程序和服务需要能够以类似的工作原理同 Data Lake Storage Gen2 配合使用。 最后，某些功能不能立即在 Data Lake Storage Gen2 中可用。 这些功能可用时，我们将在本文档中进行公布。

接下来的这些部分将帮助决定如何用最好的方式升级到 Data Lake Storage Gen2 以及何时这样做最适合。

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 解决方案组件

最有可能的是，在分析解决方案或管道中使用 Data Lake Storage Gen1 时，可以使用许多其他技术来实现整体端到端功能。 [本文](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)介绍了数据流的各种组件，包括引入、处理和使用数据。

此外，还有用于预配、管理和监视这些组件的跨领域组件。 每个组件都使用最适合它们的接口配合 Data Lake Storage Gen1 运行。 在规划将解决方案升级到 Data Lake Storage Gen2 时，需要注意要使用的接口。 由于每个接口都有特殊的要求，因此需要同时升级管理接口和数据接口。

![Data Lake Storage 解决方案组件](./media/data-lake-storage-upgrade/solution-components.png)

上面的图 1 显示了在大多数分析解决方案中会看见的功能组件。

图 2 显示了如何使用特定技术实现这些组件的示例。

图 1 中的存储功能由 Data Lake Storage Gen1（图 2）提供。 请注意数据流中的各种组件使用 REST API 或 Java SDK 与 Data Lake Storage Gen1 交互的方式。 另请注意跨领域功能组件和 Data Lake Storage Gen1 交互的方式。 预配组件使用 Azure Resource 模板，而监视组件使用 Log Analytics 利用来自 Data Lake Storage Gen1 的操作数据。

要将解决方案升级从使用 Data Lake Storage Gen1 升级到Data Lake Storage Gen2，需要复制数据和元数据，重新挂钩数据流，然后，所有组件都需要能够配合 Data Lake Storage Gen2 使用。

下面提供的信息有助于做出更好的决策：

:heavy_check_mark:平台功能

:heavy_check_mark:编程接口

:heavy_check_mark:Azure 生态系统

:heavy_check_mark:合作伙伴生态系统

:heavy_check_mark:操作信息

在每个部分中，都能够为升级确定“必需品”。 确保功能可用后，或者确保合理的解决方法就位后，请继续本指南的[规划升级](#planning-for-an-upgrade)部分。

### <a name="platform-capabilities"></a>平台功能

本部分介绍了 Data Lake Storage Gen2 中现已提供的 Data Lake Storage Gen1 平台功能。

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 - 目标 | Data Lake Storage Gen2 - 可用性状态  |
|-----------------------|-----------------|----------------------|----------------------------------|
| 数据组织| 支持将数据作为文件夹和文件进行存储 | 支持将数据作为对象/Blob、文件夹和文件进行存储 - [链接](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | 支持将数据作为文件夹和文件进行存储 - 现已提供，支持将数据作为对象/Blob 进行存储 - 尚未提供 |
| 命名空间| 分层实体 | 分层实体 |  现已提供  |
| API  | 基于 HTTPS 的 REST API | 基于 HTTP/HTTPS 的 REST API| 现已提供 |
| 服务器端 API| [WebHDFS-compatible REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx)（兼容 WebHDFS 的 REST API） | Azure Blob 服务 REST API [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API - 现已提供，Azure Blob 服务 REST API - 尚未提供       |
| Hadoop 文件系统客户端 | 是 ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | 是 ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | 现已提供  |  | [共享密匙](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)、[Azure Active Directory 标识](https://docs.microsoft.com/azure/active-directory/active-directory-authentication-scenarios)、[共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) | 现已提供  |
| 数据操作 - 授权  | 基于 Azure Active Directory 标识的文件和文件夹级别 POSIX 访问控制列表 (ACL)  | 基于 Azure Active Directory 标识的文件和文件夹级别 POSIX 访问控制列表 (ACL) [共享密钥](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)用于帐户级别授权基于角色的访问控制 ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) 访问容器 | 现已提供 |
| 数据操作 - 日志  | 是 | 使用支持工单 Azure 监视集成对特定持续时间的日志进行一次性请求 | 使用支持工单对特定持续时间的日志进行一次性请求 - 现已提供，Azure Monitor 集成 - 尚未提供 |
| 静态数据加密 | Azure Key Vault 中具有服务托管密钥和客户托管密钥的透明服务器端 | Azure Key Vault 中具有服务托管密钥和客户托管密匙的透明服务器端 | 服务托管密钥 - 现已提供，客户托管密钥 -现已提供  |
| 虚拟网络 (VNet) 支持  | [使用虚拟网络集成（预览版）](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [使用 Azure 存储的服务终结点](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | 现已提供 | 管理操作（例如“帐户创建”） | Azure 向帐户管理提供的[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) | Azure 向帐户管理提供的[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) | 现已提供| Developer SDK | .NET、Java、Python、Node.js  | .NET、Java、Python、Node.js、C++、Ruby、PHP、Go、Android、iOS| 尚未提供 | 并行分析工作负荷的优化性能。 高吞吐量和 IOPS。 | 并行分析工作负荷的优化性能。 高吞吐量和 IOPS。 | 现已提供 |
| 大小限制 | 无帐户大小、文件大小或文件数量限制 | 帐户大小或文件数量没有限制。 文件大小限制为 5 TB。 | 现已提供|
| 异地冗余| 本地冗余 (LRS) | 本地冗余 (LRS)、区域冗余 (ZRS)、全局冗余 (GRS)、读取访问全局冗余 (RA-GRS)，请点击[此处](https://docs.microsoft.com/azure/storage/common/storage-redundancy)以获取详细信息| 现已提供 |
| 区域可用性 | 参见[此处](https://azure.microsoft.com/regions/) | 所有 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | 现已提供                                                                                                                           |
| 价格                                       | 参阅[定价](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | 参阅[定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| 可用性 SLA                            | [请参阅 SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [请参阅 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | 尚未提供                                                                                                                           |
| 数据管理                             | 文件到期时间                                                                                                                                        | 生命周期策略                                                                                                                                                                                                                                                                                                                                                                                                          | 尚未提供                                                                                                                       |

### <a name="programming-interfaces"></a>编程接口

此表介绍可用于自定义应用程序的 API 集。 为更清楚地进行说明，我们将这些 API 集分为两类：管理 API 和文件系统 API。

管理 API 有助于管理帐户，而文件系统 API 有助于对文件和文件夹进行操作。

|  API 集                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Data Lake Storage Gen2 可用性 - 共享密钥验证 | Data Lake Storage Gen2 可用性 - OAuth 验证                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK - 管理                  | [链接](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *不支持*                                                      | 现已提供 - [链接](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK - 文件系统                  | [链接](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | 尚未提供                                                | 尚未提供                                                                                                                                             |
| Java SDK - 管理                  | [链接](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *不支持*                                                      | 现已提供 - [链接](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK -文件系统                  | [链接](https://docs.microsoft.com/java/api/overview/azure/datalakestore/client)                                                                                                                                                                                                                                         | 尚未提供                                                | 尚未提供                                                                                                                                             |
| Node.js - 管理                   | [链接](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | 不支持                                                      | 现已提供 - [链接](http://azure.github.io/azure-storage-node/)                                                                                            |
| Node.js - 文件系统                   | [链接](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | 尚未提供                                                | 尚未提供                                                                                                                                             |
| Python - 管理                    | [链接](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *不支持*                                                      | 现已提供 - [链接](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python - 文件系统                    | [链接](http://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | 尚未提供                                                | 尚未提供                                                                                                                                             |
| REST API - 管理                  | [链接](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *不支持*                                                      | 现已提供 -                                                                                                                                               |
| REST API - 文件系统                  | [链接](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | 现已提供                                                    | 现已提供 - [链接](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell - 管理和文件系统 | [链接](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | 管理 -不支持文件系统 - 尚未提供        | 管理 - 现已提供 - [链接](https://docs.microsoft.com/powershell/module/az.storage) 文件系统 - 尚未提供 |
| CLI - 作业管理                       | [链接](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *不支持*                                                      | 现已提供 - [链接](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI - 文件系统                       | [链接](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | 尚未提供                                                | 尚未提供                                                                                                                                             |
| Azure 资源管理器模板 - 管理             | [模板 1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [模板 2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [模板 3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *不支持*                                                      | 现已提供 - [链接](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure 生态系统

使用 Data Lake Storage Gen1 时，可以在端到端管道中使用各种 Microsoft 服务和产品。 这些服务和产品可直接或间接与 Data Lake Storage Gen1 配合使用。 此表显示了经过修改可以和 Data Lake Storage Gen1 配合使用的服务，还显示了目前哪些服务可以和 Data Lake Storage Gen2 兼容。

| **区域**             | **Data Lake Storage Gen1 的可用性**                                                                                                                                    | **Data Lake Storage Gen2 可用性 - 共享密钥验证**                                                                                                           | **Data Lake Storage Gen2 可用性 - OAuth 验证**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| 分析框架  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | 现已提供                                                                                                                                                              | 现已提供                                                                                                                                 |
|                      | [HDInsight ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - 现已提供 HDInsight 4.0 - 尚未提供      | HDInsight 3.6 ESP - 尚未提供 HDInsight 4.0 ESP - 尚未提供                                                                 |
|                      | Databricks Runtime 3.1 及更高版本                                                                                                                                               | [Databricks Runtime 5.1 及更高版本](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) - 现已提供 | [Databricks Runtime 5.1 及更高版本](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) - 现已提供                                                                                              |
|                      | [SQL 数据仓库 ](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *不支持*                                                                                                                                                              | 现已提供[链接](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| 数据集成     | [数据工厂 ](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [版本 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) - 现已提供；版本 1 - 不支持                               | [版本 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) - 现已提供；版本 1 - 不支持  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *不支持*                                                                                                                                                              | *不支持*                                                                                                                                 |
|                      | [SQL Server Integration Services ](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
|                      | [数据目录](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
|                      | [逻辑应用 ](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
| IoT                  | [事件中心 - 捕获 ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
|                      | [流分析 ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
| 消耗          | [PowerBI Desktop  ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
|                      | [Excel ](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
|                      | [Analysis Services ](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
| 工作效率         | [Azure 门户](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *不支持*                                                                                                                                                              | 帐户管理 – 现已提供 数据操作 – 尚未提供                                                                   |
|                      | [针对 Visual Studio 的 Azure Data Lake 工具 ](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |
|                      | [Azure 存储资源管理器 ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | 现已提供                                                                                                                                                              | 现已提供                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | 尚未提供                                                                                                                                                          | 尚未提供                                                                                                                             |

### <a name="partner-ecosystem"></a>合作伙伴生态系统

此表显示了经过修改可以和 Data Lake Storage Gen1 配合使用的第三方服务列表。 它还显示了当前哪些服务可以和 Data Lake Storage Gen2 兼容。

| 区域            | 合作伙伴  | 产品/服务  | Data Lake Storage Gen1 的可用性                                                                                                                                               | Data Lake Storage Gen2 可用性 - 共享密钥验证                                                   | Data Lake Storage Gen2 可用性 - OAuth 验证                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| 分析框架 | Cloudera     | CDH                  | [链接](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | 尚未提供                                                                                                  | 尚未提供                                                                                                  |
|                     | Cloudera     | Altus                | [链接](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *不支持*                                                                                                                  | 尚未提供                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [链接](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | 尚未提供                                                                                                  | 尚未提供                                                                                                  |
|                     | Qubole       |                      | [链接](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | 尚未提供                                                                                                  | 尚未提供                                                                                                  |
| ETL                 | StreamSets   |                      | [链接](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | 尚未提供                                                                                                  | 尚未提供                                                                                                  |
|                     | Informatica  |                      | [链接](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | 尚未提供                                                                                                  | 尚未提供                                                                                                  |
|                     | Attunity     |                      | [链接](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | 尚未提供                                                                                                  | 尚未提供                                                                                                  |
|                     | Alteryx      |                      | [链接](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | 尚未提供                                                                                                  | 尚未提供                                                                                                  |
|                     | ImanisData   |                      | [链接](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | 尚未提供                                                                                                  | 尚未提供                                                                                                  |
|                     | WANdisco     |                      | [链接](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [链接](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [链接](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>操作信息

Data Lake Storage Gen1 将特定消息和数据推送到其他服务，有助于对管道进行操作。 此表显示 Data Lake Storage Gen2 中相应支持的可用性。

| 数据类型                                                                                       | Data Lake Storage Gen1 的可用性                                                                 | Data Lake Storage Gen2 的可用性                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| 计费数据 - 发送到商业团队进行计费，然后提供给客户的计量  | 现已提供                                                                                             | 现已提供                                                                                                                           |
| 活动日志                                                                                          | [链接](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | 使用支持工单对特定持续时间的日志进行一次性请求 - 现已提供 Azure Monitor 集成 - 尚未提供 |
| 诊断日志                                                                                        | [链接](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | 使用支持工单对特定持续时间的日志进行一次性请求 - 现已提供 Azure Monitor 集成 - 尚未提供 |
| 度量值                                                                                                | *不支持*                                                                                               | 现已提供 - [链接](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>规划升级

此部分假设你已查看本指南的[评估升级就绪情况](#assess-your-upgrade-readiness)部分，并且已满足所有依赖关系。 如果在 Data Lake Storage Gen2 中仍有不可用的功能，除非知道相应的替代方法，否则请勿继续。 以下各部分对规划管道升级提供了相关指导。 本指南中的[执行升级](#performing-the-upgrade)部分介绍了实际升级的操作。

### <a name="upgrade-strategy"></a>升级策略

升级的最关键部分是确定策略。 这一决定将确定可用的选择。

此表列出了已用于迁移数据库、Hadoop 群集等的一些众所周知的策略。我们将在指南中采用类似的策略，并在上下文中采用它们。

| 策略                   | 优点                                                                                  | 缺点                                                           | 何时使用？                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 直接迁移                 | 最简单。                                                                                 | 复制数据、移动作业以及移动入口和出口需要停机时间                                             | 对于更简单的解决方案，由于没有多个解决方案访问相同的 Gen1 帐户，因此可以用快速控制的方式一起移动。                                                  |
| 一次性复制和增量复制 | 在源系统仍激活时执行复制可以减少停机时间。 | 移动入口和出口需要停机时间。                   | 要复制的数据量很大，因此不接受与直接迁移相关的停机时间。 转移之前，可能需要对目标系统上的重要生产数据进行测试。 |
| 并行采用              | 最少的停机时间，允许应用程序自行决定迁移的时间。           | 最精心的设计，两个系统之间需要双向同步。 | 对于复杂情况，在 Data Lake Storage Gen1 上构建的应用程序无法一次性切换，必须以增量方式移动。                                                      |

下面详细介绍每种策略所涉及的相关步骤。 这些步骤列出了将对升级中涉及的组件执行的操作。 这包括整个源系统、整个目标系统、源系统的入口源、源系统的出口目标以及源系统上运行的作业。

这些步骤并不是规范性的。 它们旨在为如何思考每种策略设定框架。 在策略实现时我们将为每种策略提供案例研究。

#### <a name="lift-and-shift"></a>直接迁移

1. 暂停源系统 - 入口源、作业、出口目标。

2. 将所有数据从源系统复制到目标系统。

3. 将所有入口源指向目标系统。 从目标系统指向出口目标。

4. 将所有作业移动到目标系统，并在目标系统上修改和运行这些作业。

5. 关闭源系统。

#### <a name="copy-once-and-copy-incremental"></a>一次性复制和增量复制

1. 将数据从源系统复制到目标系统。

2. 按固定间隔将增量数据从源系统复制到目标系统。

3. 从目标系统指向出口目标。

4. 在目标系统上移动、修改、运行所有作业。

5. 方便时以增量方式将入口源指向目标系统。

6. 一旦所有入口源都指向目标系统。

    1. 关闭增量复制。

    2. 关闭源系统。

#### <a name="parallel-adoption"></a>并行采用

1. 设置目标系统。

2. 在源系统和目标系统之间设置双向复制。

3. 以增量方式将入口源指向目标系统。

4. 以增量方式将作业移动到目标系统，然后在其上修改和运行作业。

5. 以增量方式从目标系统指向出口目标。

6. 在所有原始入口源、作业和出口目标在目标系统上运行后，关闭源系统。

### <a name="data-upgrade"></a>数据升级

用于执行升级的总体策略（如本指南[升级策略](#upgrade-strategy)部分所述）将决定可用于数据升级的工具。 下面是根据当前信息和建议列出的工具。 

#### <a name="tools-guidance"></a>工具指南

| 策略                       | 工具                                                                                                             | 优点                                                                                                                             | 注意事项                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **直接迁移**                 | [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | 托管云服务                                                                                                                | 仅复制数据。 当前无法复制 ACL。                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | 可使用此工具复制众所周知的 Hadoop 提供的工具权限（即 ACL）                                                   | 需要可以同时连接 Data Lake Storage Gen1 和 Gen2 的群集。                                                                                                                                                                                   |
| **一次性复制和增量复制** | Azure 数据工厂                                                                                                    | 托管云服务                                                                                                                | 若要支持 ADF 中的增量复制，需要以时序的方式组织数据。 增量复制的最短时间间隔是 [15 分钟](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger)。 对于更短的时间间隔，ADF 将不起作用。 当前无法复制 ACL。 |
| **并行采用**              | [WANdisco](http://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | 支持一致的复制，如果使用纯 Hadoop 环境连接到 Azure Data Lake Storage，则支持双向复制 | 如果不使用纯 Hadoop 环境，则复制可能会延迟。                                                                                                                                                                                                                                                  |

请注意，有些第三方可以处理 Data Lake Storage Gen1 到 Data Lake Storage Gen2 的升级，而不涉及上述数据/元数据复制工具（例如：[Cloudera](http://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)）。 它们提供执行数据迁移以及工作负载迁移的“一站式”体验。 可能必须对其生态系统之外的任何工具执行带外升级。

#### <a name="considerations"></a>注意事项

* 在开始升级的任何部分之前，需要首先手动创建 Data Lake Storage Gen2 帐户，因为当前指南不包括基于 Gen1 帐户信息的任何自动 Gen2 帐户流程。 确保对 [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) 和 [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account) 的帐户创建过程进行比较。

* Data Lake Storage Gen2 仅支持最大 5 TB 的文件。 若要升级到 Data Lake Storage Gen2，可能需要调整 Data Lake Storage Gen1 中的文件大小，使它们小于 5 TB。

* 如果使用的工具无法复制 ACL 或者你不想复制 ACL，需要在适当的顶级手动设置目标上的 ACL。 可以使用存储资源管理器来执行此操作。 确保这些 ACL 是默认 ACL，以便复制的文件和文件夹继承它们。

* 在 Data Lake Storage Gen1 中，可以设置的 ACL 的最高级别位于帐户的根目录。 但是在 Data Lake Storage Gen1 中，可以设置的 ACL 的最高级别位于文件系统的根文件夹，而非整个帐户。 因此，如果要在帐户级别设置默认 ACL，则需要在 Data Lake Storage Gen2 帐户中的所有文件系统中复制这些 ACL。

* 两个存储系统之间的文件命名限制是不同的。 从 Data Lake Storage Gen2 复制到 Data Lake Storage Gen1 时，尤其应注意这些差异，因为后者具有更多约束限制。

### <a name="application-upgrade"></a>应用程序升级

需要在 Data Lake Storage Gen1 或 Data Lake Storage Gen2 上生成应用程序时，必须首先选择合适的编程接口。 在该接口上调用 API 时，必须提供相应的 URI 和相应的凭据。 这三个元素的表示，API、URI 以及提供凭据的方式在 Data Lake Storage Gen1 和 Data Lake Storage Gen2 之间有所不同。因此，在升级过程中，需要适当映射这三种结构。

#### <a name="uri-changes"></a>URI 更改

这里的主要任务是将现有工作负载中使用的 adl:// URI 转换为 abfss:// URI。

Data Lake Storage Gen1 的 URI 方案在[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store)进行了详细介绍，但从广义上讲，它是 adl：//mydatalakestore.azuredatalakestore.net/ \<file_path\>。

用于访问 Data Lake Storage Gen2 文件的 URI 方案在[此处](https://docs.microsoft.com/azure/storage/data-lake-storage/use-hdi-cluster?branch=release-preview-abfs)进行了详细介绍，但从广义上讲，它是 abfss://\< FILE_SYSTEM_NAME \>\@\< ACCOUNT_NAME \>.dfs.core.widows.net/\<PATH\>。

需要浏览现有的应用程序，并确保已适当地更改 URI，使其指向 Data Lake Storage Gen2。 此外，还需要添加适当的凭据。 最后，如何停用原始应用程序并替换为新的应用程序需要与整体升级策略密切保持一致。

#### <a name="custom-applications"></a>自定义应用程序

根据应用程序在 Data Lake Storage Gen1 上使用的接口，将需要对其进行修改以适应 Data Lake Storage Gen2。

##### <a name="rest-apis"></a>REST API

如果应用程序使用 Data Lake Storage REST API，将需要修改应用程序以使用 Data Lake Storage Gen2 REST API。 “编程接口”部分提供了相关链接。

##### <a name="sdks"></a>SDK

正如“评估升级就绪情况”部分所述，SDK 目前暂未提供。 如果希望将应用程序移植到 Data Lake Storage Gen2，建议等待支持的 SDK 的推出。

##### <a name="powershell"></a>PowerShell

如“评估升级就绪情况”部分中所述，数据平面目前无法使用 PowerShell 支持。

可以使用 Data Lake Storage Gen2 中的合适项目替换管理平面 commandlet。 “编程接口”部分提供了相关链接。

##### <a name="cli"></a>CLI

如“评估升级就绪情况”部分中所述，数据平面目前无法使用 CLI 支持。

可以使用 Data Lake Storage Gen2 中的合适项目替换管理平面命令。 “编程接口”部分提供了相关链接。

### <a name="analytics-frameworks-upgrade"></a>分析框架升级

如果应用程序创建有关存储（如显式文件和文件夹路径）中信息的元数据，则需要在存储数据/元数据升级后执行其他操作。 这尤其适用于分析框架（例如 Azure HDInsight、Databricks 等），通常在存储数据上创建目录数据。

分析框架使用存储在远程存储的数据和元数据，如 Data Lake Storage Gen1 和 Gen2。 因此，从理论上讲，引擎可以是临时的且仅当作业需要对存储的数据运行时启动。

但是，为了优化性能，分析框架可能在远程存储中创建显式引用的文件和文件夹存储，然后创建缓存来保存它们。 如果远程数据的 URI 发生更改（例如，先前在 Data Lake Storage Gen1 中存储数据的群集，现在想要存储在 Data Lake Storage Gen2 中），则相同复制内容的 URI 将不同。 因此，数据和元数据升级这些引擎的缓存后还需要进行更新或再次初始化

在规划过程中，需要确定应用程序并找出如何再次初始化元数据信息以指向现在存储在 Data Lake Storage Gen2 中的数据。 以下是常用分析框架的指南，以帮助完成升级步骤。

#### <a name="azure-databricks"></a>Azure Databricks

这些步骤会有所不同，具体取决于所选择的升级策略。 当前部分假设已选择“直接迁移”策略。 此外，现有的用于访问 Data Lake Storage Gen1 帐户中的数据的 Databricks 工作区预计可以使用复制到 Data Lake Storage Gen2 帐户中的数据。

首先，请确保已创建 Gen2 帐户，然后使用合适的工具将数据从 Gen1 复制到 Gen2。 这些工具如本指南的[数据升级](#data-upgrade)部分中所述。

然后，[升级](https://docs.azuredatabricks.net/user-guide/clusters/index.html)现有 Databricks 群集，以开始使用 Databricks Runtime 5.1 或更高版本，这些版本应支持 Data Lake Storage Gen2。

此后的步骤基于现有 Databricks 工作区访问 Data Lake Storage Gen1 帐户中的数据的方式。 可以通过[直接](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly)从 Notebook 调用 adl:// URIs，或通过[装入点](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs)来完成。

如果通过提供完整 adl:// URIs 直接从 Notebook 进行访问，需要通过每个 Notebook 并更改配置以访问相应的 Data Lake Storage Gen2 URI。

如果继续，需要重新配置它，使其指向 Data Lake Storage Gen2 帐户。 不需要更多更改，Notebook 应可以像之前一样运作。

如果使用任何其他升级策略，可以创建上述步骤的变体以满足需求。

### <a name="azure-ecosystem-upgrade"></a>Azure 生态系统升级

本指南的 [Azure 生态系统](#azure-ecosystem)部分中提到的每个工具和服务都必须配置为可以使用 Data Lake Storage Gen2。

首先，确保有可以用于 Data Lake Storage Gen2 的集成。

然后，必须更改上述元素（例如：URI 和凭据）。 可以修改使用 Data Lake Storage Gen1 的现有实例或创建新的可以使用 Data Lake Storage Gen2 的实例。

### <a name="partner-ecosystem-upgrade"></a>合作伙伴生态系统升级

请为合作伙伴提供组件和工具，确保他们可以使用 Data Lake Storage Gen2。 

## <a name="performing-the-upgrade"></a>执行升级

### <a name="pre-upgrade"></a>升级前

作为其中一部分，你已完成“评估升级准备就绪”部分和本指南的[规划升级](#planning-for-an-upgrade)部分，已收到所有必要信息，并且已经制定了满足需求的计划。 在此阶段可能需要执行测试任务。

### <a name="in-upgrade"></a>升级中

根据选择的策略及解决方案的复杂性，此阶段可能会很简短或有所扩展，其中有多个工作负载等待被逐步移动到 Data Lake Storage Gen2。 这将是升级的最关键部分。

### <a name="post-upgrade"></a>升级后

转移操作完成后，最后的步骤将涉及到全面验证。 包括但不限于以下方面的验证：是否已有效地复制数据，是否已正确设置 ACL，e2e 管道是否正常运行等。完成验证后，现在可以关闭旧的管道，删除源 Data Lake Storage Gen1 帐户并全速运用基于 Data Lake Storage Gen2 的解决方案。

## <a name="conclusion"></a>结束语

可利用本文档中提供的指南升级解决方案，以使用 Data Lake Storage Gen2。 

如有其他问题或任何反馈，请在下面提供评论或在 [Azure 反馈论坛](https://feedback.azure.com/forums/327234-data-lake)中提供反馈。
