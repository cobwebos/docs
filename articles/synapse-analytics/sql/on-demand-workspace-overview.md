---
title: SQL 按需版本（预览版）
description: 了解 Azure Synapse Analytics 中的 Synapse SQL 按需版本。
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 55ef72b23f804ce7fcdb339ba4c66bcefe39db46
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020806"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的 SQL 按需版本（预览版） 

每个 Azure Synapse Analytics 工作区（预览版）都随附了可用于在 Lake 中查询数据的 SQL 按需版本（预览版）终结点。

SQL 按需版本是针对 Data Lake 中的数据运行的查询服务。 它可让你通过以下功能访问数据：
 
- 熟悉的 T-SQL 语法，可以就地查询数据，而无需将数据复制或加载到专用存储。 
- 通过 T-SQL 接口建立的集成连接，提供各种商业智能和即席查询工具（包括最流行的驱动程序）。 

SQL 按需版本是专为大规模数据和计算构建的分布式数据处理系统。 使用 SQL 按需版本可在几秒到几分钟内分析完大数据，具体所需时间取决于工作负荷。 得益于内置的查询执行容错机制，即使是对于涉及大型数据集的长时间运行的查询，该系统也能够提供很高的可靠性和成功率。

SQL 按需版本是无服务器服务，因此不需要设置基础结构，也不需要维护群集。 此服务的默认终结点将在每个 Azure Synapse 工作区内提供，因此在创建工作区后，可以立即开始查询数据。 保留的资源不产生任何费用，你只需为运行的查询所扫描的数据付费，因此，此模型是真正的按用量付费模型。  

如果在数据管道中使用 Apache Spark for Azure Synapse 进行数据准备、清理或扩充，可以直接从 SQL 按需版本[查询已在流程中创建的 Spark 外部表](develop-storage-files-spark-tables.md)。 使用[专用链接](../security/how-to-connect-to-workspace-with-private-links.md)可将 SQL 按需版本终结点整合到[托管工作区 VNet](../security/synapse-workspace-managed-vnet.md) 中。  

## <a name="who-is-sql-on-demand-for"></a>SQL 按需版本的目标用户

如果需要探索 Data Lake 中的数据、从这些数据获取见解，或者要优化现有的数据转换管道，则可从使用 SQL 按需版本获益。 它适用于以下方案：

- 基本发现和探索 - 快速推理 Data Lake 中各种格式（Parquet、CSV、JSON）的数据，以便你可以规划好如何从这些数据提取见解。
- 逻辑数据仓库 – 基于原始数据或异类数据提供关系抽象，而无需重新放置和转换数据，使你看到的数据始终是最新数据。
- 数据转换 - 使用 T-SQL 以简单、可缩放且高效的方式转换 Lake 中的数据，以便可将数据馈送到 BI 和其他工具，或者载入到关系数据存储（Synapse SQL 数据库、Azure SQL 数据库等）。

不同的职业角色可以从 SQL 按需版本获益：

- 数据工程师可以使用此服务探索 Lake、转换和准备数据，以及简化数据转换管道。 有关详细信息，请查看[此教程](tutorial-data-analyst.md)。
- 得益于 OPENROWSET 和自动架构推理等功能，数据科学家可以快速推理 Lake 中数据的内容和结构。
- 数据分析师可以使用熟悉的 T-SQL 语言或他们偏好的、可连接到 SQL 按需版本的工具，[探索数据科学家或数据工程师创建的数据和 Spark 外部表](develop-storage-files-spark-tables.md)。
- BI 专业人员可以快速[基于 Lake 中的数据创建 Power BI 报表](tutorial-connect-power-bi-desktop.md)和 Spark 表。

## <a name="what-do-i-need-to-do-to-start-using-it"></a>需要做好哪些准备才能开始使用 SQL 按需版本？

每个 Azure Synapse 工作区中都会提供 SQL 按需版本终结点。 可以创建一个工作区，然后使用熟悉的工具立即开始查询数据。

## <a name="client-tools"></a>客户端工具

使用 SQL 按需版本，可以将现有的 SQL 即席查询和商业智能工具连接到 Data Lake 中。 由于它提供用户熟悉的 T-SQL 语法，任何能够建立 TDS 连接 SQL 产品/服务的工具都可以[连接和查询 Synapse SQL 按需版本](connect-overview.md)。 可以与 Azure Data Studio 进行连接并运行即席查询，或者与 Power BI 进行连接以快速获取见解。

## <a name="is-full-t-sql-supported"></a>是否支持完整的 T-SQL？

SQL 按需版本提供 T-SQL 查询外围应用，该外围应用在某些方面略有增强/扩展，以便改善半结构化和非结构化数据的查询体验。 此外，由于 SQL 按需版本的设计，T-SQL 语言的某些方面不受支持，例如，目前不支持 DML 功能。

- 可以使用熟悉的概念来组织工作负荷：
- 数据库 - SQL 按需版本终结点可以包含多个数据库。
- 架构 - 数据库中可以包含一个或多个称作“架构”的对象所有权组。
- 视图
- 外部资源 – 数据源、文件格式和表

可通过以下方式强制实施安全性：

- 登录名和用户
- 用于控制对存储帐户的访问的凭据
- 在每个对象级别授予、拒绝和撤销权限
- Azure Active Directory 集成

支持的 T-SQL：

- 支持完整的 [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 外围应用，包括大部分 SQL 函数
- CETAS - CREATE EXTERNAL TABLE AS SELECT
- 仅与视图和安全性相关的 DDL 语句

SQL 按需版本没有本地存储，只有元数据对象才存储在数据库中。 因此，不支持与以下概念相关的 T-SQL：

- 表
- 触发器
- 具体化视图
- 不与视图和安全性相关的 DDL 语句
- DML 语句

### <a name="extensions"></a>扩展

为了使就地查询 Data Lake 文件中驻留的数据的体验顺畅，SQL 按需版本添加了以下功能，以此扩展了现有的 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 函数：

[查询多个文件或文件夹](develop-storage-files-overview.md#query-multiple-files-or-folders)

[PARQUET 文件格式](develop-storage-files-overview.md#parquet-file-format)

[用于处理分隔文本的其他选项（字段终止符、行终止符、转义字符）](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[读取选定的列子集](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[架构推理](develop-storage-files-overview.md#schema-inference)

[filename 函数](develop-storage-files-overview.md#filename-function)

[filepath 函数](develop-storage-files-overview.md#filepath-function)

[处理复杂类型以及嵌套或重复的数据结构](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>安全性

SQL 按需版本提供了一些机制来保护对数据的访问。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 集成和多重身份验证

借助 SQL 按需版本，可以使用 [Azure Active Directory 集成](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)集中管理数据库用户和其他 Microsoft 服务的标识。 此功能简化了权限管理，增强了安全性。 Azure Active Directory (Azure AD) 支持[多重身份验证](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA)，以便在支持单一登录过程的同时提高数据和应用程序安全性。

#### <a name="authentication"></a>身份验证

SQL 按需版本身份验证指的是用户连接到终结点时如何证明其身份。 支持两种类型的身份验证：

- **SQL 身份验证**

  此身份验证方法使用用户名和密码。

- **Azure Active Directory 身份验证**：

  此身份验证方法使用 Azure Active Directory 管理的标识。 对于 Azure AD 用户，可以启用多重身份验证。 请[尽可能](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)使用 Active Directory 身份验证（集成安全性）。

#### <a name="authorization"></a>授权

授权指的是用户可以在 SQL 按需版本数据库中执行哪些操作，并且授权由用户帐户的数据库角色成员身份和对象级权限控制。

如果使用 SQL 身份验证，则 SQL 用户仅存在于 SQL 按需版本中，而权限范围将限定为 SQL 按需版本中的对象。 不能直接向 SQL 用户授予访问其他服务（例如 Azure 存储）中安全对象的权限，因为该用户仅存在于 SQL 按需版本的范围内。 SQL 用户需使用[支持的授权类型](develop-storage-files-storage-access-control.md#supported-storage-authorization-types)之一来访问文件。

如果使用 Azure AD 身份验证，则用户可以登录到 SQL 按需版本和其他服务（例如 Azure 存储），并可以向 Azure AD 用户授予权限。

### <a name="access-to-storage-accounts"></a>访问存储帐户

登录到 SQL 按需版本服务的用户必须获得访问和查询 Azure 存储中的文件的授权。 SQL 按需版本支持以下授权类型：

- 共享访问签名 (SAS) 提供对存储帐户中的资源的委托访问权限。 通过 SAS，可以授予客户端对存储帐户中资源的访问权限，而无需共享帐户密钥。 通过 SAS 可以精细控制向具有 SAS 的客户端授予的访问权限类型：有效期间隔、授予的权限、可接受的 IP 地址范围、可接受的协议 (https/http)。

- 用户标识（也称为“直通”）是一种授权类型。在使用这种授权类型的情况下，登录到 SQL 按需版本的 Azure AD 用户的标识会用来授予对数据的访问权限。 在访问数据之前，Azure 存储管理员必须向访问数据的 Azure AD 用户授予权限。 此授权类型使用登录到 SQL 按需版本的 Azure AD 用户，因此不受 SQL 用户类型的支持。

## <a name="next-steps"></a>后续步骤
可在以下文章中找到有关终结点连接和查询文件的更多信息： 
- [连接到终结点](connect-overview.md)
- [查询文件](develop-storage-files-overview.md)
