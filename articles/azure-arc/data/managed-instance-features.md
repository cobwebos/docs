---
title: 支持 Azure Arc 的特性和功能 SQL 托管实例
description: 支持 Azure Arc 的特性和功能 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 810a08d2f72359b385d2a7567b796aa222c6ab14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934821"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>支持 Azure Arc 的特性和功能 SQL 托管实例

启用 Azure Arc 的 SQL 托管实例与 SQL Server 的最新稳定版本共享通用基本代码。 大多数标准 SQL 语言、查询处理和数据库管理功能都是相同的。 SQL Server 与 SQL 数据库或 SQL 托管实例都有以下功能：

- 语言功能 - [流语言和关键字](/sql/t-sql/language-elements/control-of-flow)、[光标](/sql/t-sql/language-elements/cursors-transact-sql)、[数据类型](/sql/t-sql/data-types/data-types-transact-sql)、[DML 语句](/sql/t-sql/queries/queries)、[谓词](/sql/t-sql/queries/predicates)、[序号](/sql/relational-databases/sequence-numbers/sequence-numbers)、[存储过程](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)和[变量](/sql/t-sql/language-elements/variables-transact-sql)的控制。
- 数据库功能 - [自动优化（计划强制）](/sql/relational-databases/automatic-tuning/automatic-tuning)、[更改跟踪](/sql/relational-databases/track-changes/about-change-tracking-sql-server)、[数据库排序规则](/sql/relational-databases/collations/set-or-change-the-database-collation)、[包含的数据库](/sql/relational-databases/databases/contained-databases)、[包含的用户](/sql/relational-databases/security/contained-database-users-making-your-database-portable)、[数据压缩](/sql/relational-databases/data-compression/data-compression)、[数据库配置设置](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)、[联机索引操作](/sql/relational-databases/indexes/perform-index-operations-online)、[分区](/sql/relational-databases/partitions/partitioned-tables-and-indexes)和[时态表](/sql/relational-databases/tables/temporal-tables)（[参阅入门指南](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)）。
- 安全功能-[应用程序角色](/sql/relational-databases/security/authentication-access/application-roles)、[动态数据屏蔽](/sql/relational-databases/security/dynamic-data-masking) ([通过 Azure 门户) 的 SQL 数据库动态数据掩码入门](../../azure-sql/database/dynamic-data-masking-configure-portal.md)[行级别安全性](/sql/relational-databases/security/row-level-security)
- 多模型功能- [图形处理](/sql/relational-databases/graphs/sql-graph-overview)、 [JSON 数据](/sql/relational-databases/json/json-data-sql-server)、 [OPENXML](/sql/t-sql/functions/openxml-transact-sql)、 [空间](/sql/relational-databases/spatial/spatial-data-sql-server)、 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)和 [XML 索引](/sql/t-sql/statements/create-xml-index-transact-sql)。



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>启用了 Azure Arc 的 SQL 托管实例功能

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS 高可用性  
  
|Feature|已启用 Azure Arc 的 SQL 托管实例|
|-------------|----------------|
|日志传送|是| 
|备份压缩|是|
|数据库快照|是|
|Always On 故障转移群集实例<sup>1</sup>| 不适用。 可用的类似功能 |
|Always On 可用性组<sup>2</sup>|已计划 HA 功能。|
|基本可用性组 <sup>2</sup>|已计划 HA 功能。|
|最小副本提交可用性组 <sup>2</sup>|已计划 HA 功能。|
|无群集的可用性组|是|
|联机页面和文件还原|是|
|联机索引|是|
|可恢复的联机索引重新生成|是|
|联机架构更改|是|
|快速恢复|是|
|镜像备份|是|
|热插拔内存和 CPU|是|
|加密备份|是|
|Azure 的混合备份（URL 的备份）|是|

<sup>1</sup> 在出现 pod 故障的情况下，新的 SQL 托管实例将启动并重新附加到包含数据的永久性卷。 [在此处了解有关 Kubernetes 永久性卷的详细信息](https://kubernetes.io/docs/concepts/storage/persistent-volumes)。

<sup>2</sup> 未来版本将提供 AG 功能 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS 可伸缩性和性能  

|Feature|已启用 Azure Arc 的 SQL 托管实例|
|-------------|----------------|
|列存储|   是|
|聚集列存储索引中的大型对象二进制文件|    是|
|联机非聚集列存储索引重新生成| 是|
|内存中 OLTP|    是|
|永久性主内存|    是|
|表和索引分区|  是
|数据压缩|  是|
|Resource Governor| 是|
|已分区表并行度| 是|
|NUMA 感知、大型页内存和缓冲区数组分配|  是|
|IO 资源调控|    是|
|延迟持续性|    是|
|自动优化|  是|
|批处理模式自适应联接| 是|
|批处理模式内存授予反馈|  是|
|多语句表值函数的交错执行|  是|
|大容量插入改进   |是|

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS 安全性  
|Feature|已启用 Azure Arc 的 SQL 托管实例|
|-------------|----------------|
|行级安全性|    是|
|Always Encrypted|  是|
|具有安全 enclave 的 Always Encrypted| 否|
|动态数据掩码|  是|
|基本审核|    是|
|精细审核| 是|
|透明数据库加密|   是|
|用户定义的角色|    是|
|包含的数据库|   是|
|备份加密|    是|

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS 可管理性  

|Feature|已启用 Azure Arc 的 SQL 托管实例|
|-------------|----------------|
|专用管理连接|    是|
|PowerShell 脚本支持|  是|
|支持数据层应用程序组件操作 - 提取、部署、升级、删除| 是
|策略自动执行（检查计划和更改）   |是|
|性能数据收集器|    是|
|标准性能报表   |是|
|计划指南和计划指南的计划冻结| 是|
|使用 NOEXPAND 提示的索引视图的直接查询|   是|
|自动索引视图维护    |是|
|分布式分区视图| 是|
|并行索引操作    |是|
|查询优化器自动使用索引视图|  是|
|并行一致性检查 |是|


### <a name="programmability"></a><a name="Programmability"></a> Programmability  

|Feature|已启用 Azure Arc 的 SQL 托管实例|
|-------------|----------------|
|JSON|  是 |       |
|查询存储    |是    |       
|临时|  是 |       
|本机 XML 支持|    是 |       
|XML 索引   |是    |       
|MERGE 和 UPSERT 功能|   是 |       
|日期和时间数据类型    |是    |       
|国际化支持|  是 |       
|全文和语义搜索 |    否      |
|查询中的语言规范 |是        |   
|Service Broker（消息传递）|    是     |   
|Transact-SQL 端点|    是 |       
|图形| 是 |   
|机器学习服务| 否  |   
|PolyBase| 否   |


### <a name="tools"></a>工具

启用 Azure Arc 的 SQL 托管实例支持可帮助你管理数据的各种数据工具。

| **工具** | 已启用 Azure Arc 的 SQL 托管实例|
| --- | --- | --- |
| Azure 门户 <sup>1</sup> | 否 |
| Azure CLI | 否 |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | 是 |
| Azure PowerShell | 是 |
| [BACPAC 文件（导出）](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 |
| [BACPAC 文件（导入）](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | 是 |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | 是 |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | 是 |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | 是 |

<sup>1</sup> Azure 门户仅用于在预览期间查看只读模式下启用了 Azure ARC 的 SQL 托管实例。


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> 服务 & 不支持的功能

以下功能和服务不适用于启用了 Azure Arc 的 SQL 托管实例。 随着时间的推移，对这些功能的支持将越来越多。

| 区域 | 不支持的功能或服务 |
|-----|-----|
| **数据库引擎** | 合并复制 |
| &nbsp; | Stretch DB |
| &nbsp; | 具有第三方连接的分布式查询 |
| &nbsp; | 除 SQL Server 和 Azure SQL 产品之外的其他数据源的链接服务器 |
| &nbsp; | 系统扩展存储过程（XP_CMDSHELL 等） |
| &nbsp; | FileTable，FILESTREAM |
| &nbsp; | 带有 EXTERNAL_ACCESS 或 UNSAFE 权限集的 CLR 程序集 |
| &nbsp; | 缓冲池扩展 |
| **SQL Server 代理** |  子系统：CmdExec、PowerShell、队列读取器、SSIS、SSAS、SSRS |
| &nbsp; | 警报 |
| &nbsp; | 托管备份 |
| **高可用性** | 数据库镜像  |
| **安全性** | 可扩展的密钥管理 |
| &nbsp; | 链接服务器的 AD 身份验证 | 
| &nbsp; | 可用性组 (AG) 的 AD 身份验证 | 