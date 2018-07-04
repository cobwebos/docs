# [Data Lake Storage Gen1 文档](index.md)
# [切换到 Data Lake Storage Gen2（预览版）文档](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction)

# 概述
## [Data Lake Storage Gen1 概述](data-lake-store-overview.md)
## [Data Lake Storage Gen1 与 Azure 存储的比较](data-lake-store-comparison-with-blob-storage.md)
## [使用 Data Lake Storage Gen1 处理大数据](data-lake-store-data-scenarios.md)
## [使用 Data Lake Storage Gen1 的开源应用程序](data-lake-store-compatible-oss-other-applications.md)
## [使用 Data Lake Storage Gen1 的最佳做法](data-lake-store-best-practices.md)

# 入门
## [使用门户](data-lake-store-get-started-portal.md)
## [使用 PowerShell](data-lake-store-get-started-powershell.md)
## [使用 Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)


# 如何
## 加载和移动数据
### [使用 Azure 数据工厂](../data-factory/load-azure-data-lake-store.md)
### [使用存储资源管理器](data-lake-store-in-storage-explorer.md)
### [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [使用 Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [从脱机源上传数据](data-lake-store-offline-bulk-data-upload.md)
### [跨区域迁移 Data Lake Store](data-lake-store-migration-cross-region.md)

## 保护数据
### [安全性概述](data-lake-store-security-overview.md)
### [Data Lake Storage Gen1 中的访问控制](data-lake-store-access-control.md)
### [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
### [加密](data-lake-store-encryption.md)

## 使用 Data Lake Storage Gen1 进行身份验证
### [身份验证选项](data-lakes-store-authentication-using-azure-active-directory.md)
### [最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [使用 Java](data-lake-store-end-user-authenticate-java-sdk.md)
#### [使用 .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
#### [使用 REST API](data-lake-store-end-user-authenticate-rest-api.md)
#### [使用 Python](data-lake-store-end-user-authenticate-python.md)
### [服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [使用 Java](data-lake-store-service-to-service-authenticate-java.md)
#### [使用 .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [使用 REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [使用 Python](data-lake-store-service-to-service-authenticate-python.md)

## 使用 Data Lake Storage Gen1
### 帐户管理操作
#### [使用 .NET SDK](data-lake-store-get-started-net-sdk.md)
#### [使用 REST API](data-lake-store-get-started-rest-api.md)
#### [使用 Python](data-lake-store-get-started-python.md)
### 文件系统操作
#### [使用 .NET SDK](data-lake-store-data-operations-net-sdk.md)
#### [使用 Java SDK](data-lake-store-get-started-java-sdk.md)
#### [使用 REST API](data-lake-store-data-operations-rest-api.md)
#### [使用 Python](data-lake-store-data-operations-python.md)

## 性能
### [Azure Data Lake Storage Gen1 性能优化指南](data-lake-store-performance-tuning-guidance.md)
### [将 PowerShell 与 Data Lake Storage Gen1 配合使用的性能优化指南](data-lake-store-performance-tuning-powershell.md)
### [Spark on HDInsight 和 Data Lake Storage Gen1 性能优化指南](data-lake-store-performance-tuning-spark.md)
### [Hive on HDInsight 和 Data Lake Storage Gen1 性能优化指南](data-lake-store-performance-tuning-hive.md)
### [MapReduce on HDInsight 和 Data Lake Storage Gen1 性能优化指南](data-lake-store-performance-tuning-mapreduce.md)
### [Storm on HDInsight 和 Data Lake Storage Gen1 性能优化指南](data-lake-store-performance-tuning-storm.md)

## 与 Azure 服务集成
### 与 HDInsight 配合使用
#### [使用 Azure 门户](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [使用 Azure PowerShell（默认存储）](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [使用 Azure PowerShell（附加存储）](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [使用 Azure 模板](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [从 Azure VNET 中的 VM 访问](data-lake-store-connectivity-from-vnets.md)
### [与 Data Lake Analytics 配合使用](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [与 Azure 事件中心配合使用](data-lake-store-archive-eventhub-capture.md)
### [与数据工厂配合使用](../data-factory/load-azure-data-lake-store.md)
### [与流分析配合使用](data-lake-store-stream-analytics.md)
### [与 Power BI 配合使用](data-lake-store-power-bi.md)
### [与数据目录配合使用](data-lake-store-with-data-catalog.md)
### [在 SQL 数据仓库中使用 PolyBase](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [与 SQL Server Integration Services 配合使用](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [更多 Azure 集成选项](data-lake-store-integrate-with-other-services.md)

## 管理
### [访问诊断日志](data-lake-store-diagnostic-logs.md)
### [高可用性规划](data-lake-store-disaster-recovery-guidance.md)

# 引用
## [代码示例](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python（帐户管理）](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)
## [Python（文件系统管理）](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [Azure CLI](https://docs.microsoft.com/cli/azure/dls)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/)
## [Data Lake Store 博客](https://blogs.msdn.microsoft.com/azuredatalake/)
## [在 UserVoice 上提供反馈](https://feedback.azure.com/forums/327234-data-lake)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [定价](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [服务更新](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Stack Overflow 论坛](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
