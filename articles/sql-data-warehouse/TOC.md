# 概述

## [关于 SQL 数据仓库](sql-data-warehouse-overview-what-is.md)
## [备忘单](cheat-sheet.md)

# 快速入门

## [创建并连接 - 门户](create-data-warehouse-portal.md)

# 教程
## [1 - 从 blob 加载数据](load-data-from-azure-blob-storage-using-polybase.md)

# 概念
## 服务功能
### [MPP 体系结构](massively-parallel-processing-mpp-architecture.md)
### [性能层](performance-tiers.md)
### [数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [数据仓库备份](sql-data-warehouse-backups.md)
### [审核](sql-data-warehouse-auditing-overview.md)
### [容量限制](sql-data-warehouse-service-capacity-limits.md)
### [常见问题](sql-data-warehouse-overview-faq.md)

## “安全”
### [概述](sql-data-warehouse-overview-manage-security.md)
### [身份验证](sql-data-warehouse-authentication.md)


## 迁移到 SQL 数据仓库
### [概述](sql-data-warehouse-overview-migrate.md)
### [迁移架构](sql-data-warehouse-migrate-schema.md)
### [迁移代码](sql-data-warehouse-migrate-code.md)
### [迁移数据](sql-data-warehouse-migrate-data.md)

## 加载和移动数据
### [概述](design-elt-data-loading.md)
### [最佳实践](guidance-for-loading-data.md)


## 集成
### [概述](sql-data-warehouse-overview-integrate.md)
### [SQL 数据库弹性查询](how-to-use-elastic-query-with-sql-data-warehouse.md)


## 监视和优化
### [指南](resource-classes-for-workload-management.md)
### [列存储压缩](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [监视](sql-data-warehouse-manage-monitor.md)
### [故障排除](sql-data-warehouse-troubleshoot.md)

## 开发数据仓库
### [概述](sql-data-warehouse-overview-develop.md)
### [数据仓库组件](sql-data-warehouse-overview-workload.md)

### 表
#### [概述](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [数据类型](sql-data-warehouse-tables-data-types.md)
#### [分布式表](sql-data-warehouse-tables-distribute.md)
#### [索引](sql-data-warehouse-tables-index.md)
#### [标识](sql-data-warehouse-tables-identity.md)
#### [分区](sql-data-warehouse-tables-partition.md)
#### [复制的表](design-guidance-for-replicated-tables.md)
#### [统计信息](sql-data-warehouse-tables-statistics.md)
#### [临时](sql-data-warehouse-tables-temporary.md)

### 查询
#### [动态 SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [Group by 选项](sql-data-warehouse-develop-group-by-options.md)
#### [标签](sql-data-warehouse-develop-label.md)

### T-SQL 语言元素
#### [循环](sql-data-warehouse-develop-loops.md)
#### [存储过程](sql-data-warehouse-develop-stored-procedures.md)
#### [Transactions](sql-data-warehouse-develop-transactions.md)
#### [事务最佳实践](sql-data-warehouse-develop-best-practices-transactions.md)
#### [用户定义的架构](sql-data-warehouse-develop-user-defined-schemas.md)
#### [变量赋值](sql-data-warehouse-develop-variable-assignment.md)
#### [视图](sql-data-warehouse-develop-views.md)

# 操作指南
## 服务功能
### [还原数据仓库 - 门户](sql-data-warehouse-restore-database-portal.md)
### [还原数据仓库 - PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [还原数据仓库 - REST API](sql-data-warehouse-restore-database-rest-api.md)

## “安全”
### [启用加密 - 门户](sql-data-warehouse-encryption-tde.md)
### [启用加密 - T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [威胁检测](sql-data-warehouse-security-threat-detection.md)


## 加载和移动数据
### [AdventureWorks](sql-data-warehouse-load-sample-databases.md)
### [Contoso 公共数据](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [AzCopy](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## 集成
### [配置 SQL 数据库弹性查询](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [添加 Azure 流分析作业](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [使用机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [使用 Power BI 进行可视化](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## 监视和优化
### [分析工作负荷](analyze-your-workload.md)

## 向外扩展
### [管理计算 - 门户](sql-data-warehouse-manage-compute-portal.md)
### [管理计算 - PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [管理计算 - REST API](sql-data-warehouse-manage-compute-rest-api.md)
### [自动化计算级别](manage-compute-with-azure-functions.md)


# 引用


## T-SQL
### [完整参考信息](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [SQL DW 语言元素](sql-data-warehouse-reference-tsql-language-elements.md)
### [SQL DW 语句](sql-data-warehouse-reference-tsql-statements.md)
## [系统视图](sql-data-warehouse-reference-tsql-system-views.md)
## [PowerShell cmdlets](sql-data-warehouse-reference-powershell-cmdlets.md)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=databases)
## [论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [功能请求](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [服务更新](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [堆栈溢出](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [支持](sql-data-warehouse-get-started-create-support-ticket.md)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## 合作伙伴
### [商业智能](sql-data-warehouse-partner-business-intelligence.md)
### [数据集成](sql-data-warehouse-partner-data-integration.md)
### [数据管理](sql-data-warehouse-partner-data-management.md)
