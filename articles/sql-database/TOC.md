# 概述
## [什么是 SQL 数据库？](sql-database-technical-overview.md)
### [服务层](sql-database-service-tiers.md)
### [数据库事务单位](sql-database-what-is-a-dtu.md)
### [DTU 基准概述](sql-database-benchmark-overview.md)
### [资源限制](sql-database-resource-limits.md)
### [功能](sql-database-features.md)
### [SQL 数据库常见问题](sql-database-faq.md)
## 优点
### [学习和应用](sql-database-learn-and-adapt.md)
### [动态缩放](sql-database-scale-on-the-fly.md)
### [构建多租户应用](sql-database-build-multi-tenant-apps.md)
### [安全和保护](sql-database-helps-secures-and-protects.md)
### [在环境中工作](sql-database-works-in-your-environment.md)
## 比较
### [SQL 数据库与 VM 上的 SQL](sql-database-paas-vs-sql-server-iaas.md)
### [T-SQL 的差异](sql-database-transact-sql-information.md)
### [SQL 与 NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [SQL 数据库工具](sql-database-manage-overview.md)
## [SQL 数据库教程](sql-database-explore-tutorials.md)
## [解决方案快速入门](sql-database-solution-quick-starts.md)
## 安全性
### [Azure SQL 数据库的 Azure 安全中心](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL 安全中心](https://msdn.microsoft.com/library/azure/bb510589)

# 入门
## 数据库和服务器
### 单一数据库
#### 学习
##### [Azure 门户教程：使用 Azure 门户创建 Azure SQL 数据库](sql-database-get-started.md)
#### 操作
##### [Azure 门户教程：使用 Azure 门户创建 Azure SQL 数据库](sql-database-get-started.md)
##### [Azure 门户教程：使用 PowerShell 创建 Azure SQL 数据库](sql-database-get-started-powershell.md)
##### [Azure 门户教程：使用 C# 创建 Azure SQL 数据库](sql-database-get-started-csharp.md)
### 弹性数据库池
#### 学习
##### [弹性数据库池](sql-database-elastic-pool.md)
##### [何时使用弹性数据库池](sql-database-elastic-pool-guidance.md)
##### [弹性池定价](sql-database-elastic-pool-price.md)
#### 操作
##### [使用 Azure 门户进行创建](sql-database-elastic-pool-create-portal.md)
##### [使用 PowerShell 进行创建](sql-database-elastic-pool-create-powershell.md)
##### [使用 C# 进行创建](sql-database-elastic-pool-create-csharp.md)
### 扩大
#### 学习
##### [扩大](sql-database-elastic-scale-introduction.md)
##### [弹性缩放](sql-database-elastic-scale-get-started.md)
##### [构建可缩放的云数据库](sql-database-elastic-database-client-library.md)
##### [跨数据库作业](sql-database-elastic-jobs-overview.md)
##### [弹性工具常见问题](sql-database-elastic-scale-faq.md)
##### [弹性数据库工具术语表](sql-database-elastic-scale-glossary.md)
##### [在扩展云数据库之间移动数据](sql-database-elastic-scale-overview-split-and-merge.md)
#### 操作
##### [弹性作业](sql-database-elastic-jobs-getting-started.md)
##### [跨数据库报告](sql-database-elastic-query-getting-started.md)
##### [跨数据库查询](sql-database-elastic-query-getting-started-vertical.md)
##### [卸载弹性作业](sql-database-elastic-jobs-uninstall.md)
## 迁移和移动数据
### 学习
#### [迁移数据库](sql-database-cloud-migrate.md)
#### [事务复制](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [数据同步](sql-database-get-started-sql-data-sync.md)
#### [复制 SQL 数据库](sql-database-copy.md)
### 操作
#### 确定数据库兼容性
##### [SQL 包实用工具](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### 修复数据库兼容性问题
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [SQL Azure 迁移向导](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [SQL Server Management Studio 迁移向导](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### 复制数据库
##### [使用 Azure 门户进行复制](sql-database-copy-portal.md)
##### [使用 PowerShell 进行复制](sql-database-copy-powershell.md)
##### [使用 T-SQL 进行复制](sql-database-copy-transact-sql.md)
#### 将数据库导出到 BACPAC
##### [Azure 门户](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [SQL 包实用工具](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### 从 BACPAC 文件导入数据库
##### [Azure 门户](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [SQL 包实用工具](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [使用 BCP 从 CSV 文件加载](sql-database-load-from-csv-with-bcp.md)
## 身份验证和授予访问权限
### 学习
#### [概述](sql-database-security.md)
#### [安全准则](sql-database-security-guidelines.md)
#### [管理登录名](sql-database-manage-logins.md)
### 操作
### [教程：SQL 身份验证和访问权限](sql-database-get-started-security.md)
## 保护数据
### 学习
#### 审核
##### [审核](sql-database-auditing-get-started.md)
##### [ 针对审核的下层客户端支持和 IP 终结点更改](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [威胁检测](sql-database-threat-detection-get-started.md)
#### 加密数据
##### [Azure 密钥保管库](sql-database-always-encrypted-azure-key-vault.md)
##### [Windows 证书存储](sql-database-always-encrypted.md)
##### [透明数据加密](https://msdn.microsoft.com/library/azure/dn948096)
##### [列加密](https://msdn.microsoft.com/library/azure/ms179331)
#### 对数据进行掩码
##### 动态数据掩码
###### [Azure 门户](sql-database-dynamic-data-masking-get-started.md)
### 操作
#### [使用 Azure 门户进行动态数据掩码](sql-database-dynamic-data-masking-get-started.md)
## 业务连续性
### 学习
#### [概述](sql-database-business-continuity.md)
#### [数据库备份](sql-database-automated-backups.md)
#### [长期保留](sql-database-long-term-retention.md)
#### [使用备份恢复数据库](sql-database-recovery-using-backups.md)
#### [灾难恢复的身份验证要求](sql-database-geo-replication-security-config.md)
#### [业务连续性设计方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [使用弹性池的灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [滚动升级](sql-database-manage-application-rolling-upgrade.md)
### 操作
#### 还原已删除的数据库
##### [Azure 门户](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### 时间点还原
##### [Azure 门户](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### 异地还原数据库
##### [Azure 门户](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [单一表](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [在数据中心服务中断后恢复](sql-database-disaster-recovery.md)
#### [执行灾难恢复演练](sql-database-disaster-recovery-drills.md)
### 复制数据库
#### [活动异地复制概述](sql-database-geo-replication-overview.md)
#### 配置活动异地复制
##### [Azure 门户](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### 使用活动异地复制进行故障转移
##### [Azure 门户](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## 应用开发
### 学习
#### [数据库应用程序开发概述](sql-database-develop-overview.md)
#### [连接库](sql-database-libraries.md)
#### [多租户 SaaS 应用程序](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [用于 ADO.NET 4.5 的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [获取用来对应用程序进行身份验证的值](sql-database-client-id-keys.md)
### 操作
#### 连接应用程序
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C 和 C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [使用 Visual Studio 进行连接](sql-database-connect-query.md)
#### [构建客户端应用程序](https://www.microsoft.com/sql-server/developer-get-started)
#### [处理错误消息](sql-database-develop-error-messages.md)
### 软件即服务的客户实现
#### [Daxko/CSI 软件](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## 数据库开发
### 学习
#### 临时表
##### [临时表](sql-database-temporal-tables.md)
##### [保留策略](sql-database-temporal-tables-retention-policy.md)
#### [JSON 数据](sql-database-json-features.md)
#### [内存中](sql-database-in-memory.md)
### 操作
#### [SQL Server 开发](https://msdn.microsoft.com/library/ms179422.aspx)
## 监视和优化
### 学习
#### [单一数据库](sql-database-single-database-monitor.md)
#### [SQL 数据库顾问概述](sql-database-advisor.md)
#### [单一数据库指南](sql-database-performance-guidance.md)
#### [在 Azure 门户中深入分析工作负荷](sql-database-performance.md)
#### [使用批处理](sql-database-use-batching-to-improve-performance.md)
## SQL 数据库 V11
### [Web 和 Business Edition 停用](sql-database-web-business-sunset-faq.md)
### [服务层顾问](sql-database-service-tier-advisor.md)
### [弹性池评估工具](sql-database-elastic-pool-database-assessment-powershell.md)
### [升级到 V12](sql-database-v12-plan-prepare-upgrade.md)
#### [使用 Azure 门户进行升级](sql-database-upgrade-server-portal.md)
#### [使用 PowerShell 进行升级](sql-database-upgrade-server-powershell.md)
#### [定价层建议](sql-database-service-tier-advisor.md)

# 如何
## 创建和管理
### 服务器和数据库
#### [单一数据库](sql-database-manage-portal.md)
#### [Azure 门户](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### 弹性数据库池
#### [使用 Azure 门户进行创建](sql-database-elastic-pool-create-portal.md)
#### [使用 PowerShell 进行创建](sql-database-elastic-pool-create-powershell.md)
#### [使用 C# 进行创建](sql-database-elastic-pool-create-csharp.md)
#### [使用 Azure 门户进行管理](sql-database-elastic-pool-manage-portal.md)
#### [使用 PowerShell 进行管理](sql-database-elastic-pool-manage-powershell.md)
#### [使用 C# 进行管理](sql-database-elastic-pool-manage-csharp.md)
#### [使用 T-SQL 进行管理](sql-database-elastic-pool-manage-tsql.md)
### 分片的数据库
#### [使用分片映射管理器](sql-database-elastic-scale-shard-map-management.md)
#### [拆分/合并安全配置](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [使用 Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [使用实体框架](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [依赖于数据的路由](sql-database-elastic-scale-data-dependent-routing.md)
#### [多租户行级别安全性](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [管理凭据](sql-database-elastic-scale-manage-credentials.md)
#### [部署拆分 / 合并服务](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [添加分片](sql-database-elastic-scale-add-a-shard.md)
#### [使用 RecoveryManager 类解决分片映射问题](sql-database-elastic-database-recovery-manager.md)
###  身份验证
#### [Azure AD 身份验证](sql-database-aad-authentication.md)
#### [多重身份验证](sql-database-ssms-mfa-authentication.md)
### 防火墙规则
#### [Azure 门户](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [REST API](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### 作业和自动化
#### [服务安装](sql-database-elastic-jobs-service-installation.md)
#### [在 Azure 门户中创建和管理弹性作业](sql-database-elastic-jobs-create-and-manage.md)
#### [使用 PowerShell 创建和管理弹性作业](sql-database-elastic-jobs-powershell.md)
#### [升级客户端库](sql-database-elastic-scale-upgrade-client-library.md)
#### [使用 Azure 自动化服务管理 SQL 数据库](sql-database-manage-automation.md)
### [防火墙](sql-database-firewall-configure.md)
## 加密数据
### [Always Encrypted 概述](sql-database-always-encrypted.md)
### [透明数据加密](https://msdn.microsoft.com/library/azure/dn948096)
### [列加密](https://msdn.microsoft.com/library/azure/ms179331)
## 迁移
### 确定兼容性
#### [SQL 包实用工具](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### 修复兼容性问题
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SQL Azure 迁移向导](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [使用 SQL Server Management Studio 迁移向导](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [使用事务复制](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [迁移要扩展的现有已扩展数据库](sql-database-elastic-convert-to-use-elastic-tools.md)
## 监视和优化
### [Query Performance Insight](sql-database-query-performance.md)
### [SQL 数据库顾问](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [兼容性级](sql-database-compatibility-level-query-performance-130.md)
### [分片映射管理器的性能计数器](sql-database-elastic-database-perf-counters.md)
### [性能优化提示](sql-database-troubleshoot-performance.md)
### 更改服务层和性能级别
#### [使用 Azure 门户](sql-database-scale-up.md)
#### [使用 PowerShell](sql-database-scale-up-powershell.md)
### [创建警报](sql-database-insights-alerts-portal.md)
### In-Memory OLTP
#### [采用内存中 OLTP](sql-database-in-memory-oltp-migration.md)
#### [监视内存中 OLTP 存储](sql-database-in-memory-oltp-monitoring.md)
### 查询存储
#### [使用 Query Store 监视性能](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Query Store 使用方案](https://msdn.microsoft.com/library/mt614796.aspx)
#### [操作 Query Store](sql-database-operate-query-store.md)
### 扩展的事件
#### [扩展事件](sql-database-xevent-db-diff-from-svr.md)
#### [事件文件目标代码](sql-database-xevent-code-event-file.md)
#### [环形缓冲区目标代码](sql-database-xevent-code-ring-buffer.md)
## 移动数据
### [复制 SQL 数据库](sql-database-copy.md)
#### [Azure 门户](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### 将数据库导出到 BACPAC
#### [Azure 门户](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [SQL 包实用工具](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### 从 BACPAC 文件导入数据库
#### [Azure 门户](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [SQL 包实用工具](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [使用 BCP 从 CSV 文件加载](sql-database-load-from-csv-with-bcp.md)
### [在扩展云数据库之间移动数据](sql-database-elastic-scale-overview-split-and-merge.md)
## 查询
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [多分片查询](sql-database-elastic-scale-multishard-querying.md)
### 跨数据库查询
#### [概述](sql-database-elastic-query-overview.md)
#### [使用不同的架构执行跨数据库查询](sql-database-elastic-query-vertical-partitioning.md)
#### [跨数据库报告](sql-database-elastic-query-horizontal-partitioning.md)
#### [跨云数据库的分布式事务](sql-database-elastic-transactions-overview.md)
## 还原
### 还原已删除的数据库
#### [Azure 门户](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### 时间点还原
#### [Azure 门户](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### 异地还原
#### [Azure 门户](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [单一表](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [在数据中心服务中断后恢复](sql-database-disaster-recovery.md)
### [执行灾难恢复演练](sql-database-disaster-recovery-drills.md)
## 复制
### [活动异地复制概述](sql-database-geo-replication-overview.md)
### 配置
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### 故障转移
#### [Azure 门户](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## 故障排除
### [连接问题](sql-database-troubleshoot-common-connection-issues.md)
### [暂时性连接错误](sql-database-troubleshoot-connection.md)
### [诊断和预防](sql-database-connectivity-issues.md)
### [权限](sql-database-troubleshoot-permissions.md)
### [移动数据库](sql-database-troubleshoot-moving-data.md)


# 参考
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell（弹性数据库）](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](https://docs.microsoft.com)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [REST](/rest/api/sql/)

# 相关内容
## SQL 数据库管理库
### [获取 SQL 数据库管理库包](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server 驱动程序](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)

### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)


# 资源
## [定价](https://azure.microsoft.com/pricing/details/sql-database/)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/sql-azure)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [服务更新](https://azure.microsoft.com/updates/?service=sql-database)
## [SQL Server工具](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)


<!--HONumber=Dec16_HO1-->


