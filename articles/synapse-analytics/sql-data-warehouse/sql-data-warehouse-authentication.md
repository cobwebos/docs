---
title: 身份验证
description: 了解如何使用 Azure Active Directory (Azure AD) 或 SQL Server 身份验证向 Azure Synapse Analytics 进行身份验证。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 29709dc03ee3a06bdf2aec2587909a08ee13504e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206723"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>对 Azure Synapse Analytics 进行身份验证

了解如何使用 Azure Active Directory (AAD) 或 SQL Server 身份验证对 Azure Synapse 中的 Synapse SQL 进行身份验证。

若要连接到 SQL 池，必须传入安全凭据进行身份验证。 建立连接时，特定的连接设置已配置为建立查询会话的一部分。  

若要深入了解安全性以及如何启用与数据仓库的连接，请参阅[保护数据库文档](sql-data-warehouse-overview-manage-security.md)。

## <a name="sql-authentication"></a>SQL 身份验证

若要连接到 SQL 池，必须提供以下信息：

* 完全限定的服务器名称
* 指定 SQL 身份验证
* 用户名
* 密码
* 默认数据库（可选）

默认情况下，你的连接连接到 master 数据库而不是用户数据库。 若要连接到用户数据库，可以选择执行以下两项操作之一：

* 在 SSDT、SSMS 或应用程序连接字符串中将您的服务器注册到 SQL Server 对象资源管理器时指定默认数据库。 例如，包含 ODBC 连接的 InitialCatalog 参数。
* 在 SSDT 中创建会话之前先突出显示用户数据库。

> [!NOTE]
> 不支持使用 Transact-SQL 语句 **USE MyDatabase;** 更改连接的数据库。 有关使用 SSDT 连接到 SQL 池的指南，请参阅[使用 Visual Studio 进行查询](sql-data-warehouse-query-visual-studio.md)一文。

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 身份验证

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 身份验证是一种使用 Azure Active Directory (Azure AD) 中的标识连接到 SQL 池的机制。 通过 Azure Active Directory 身份验证，可以在一个中心位置中集中管理数据库用户和其他 Microsoft 服务的标识。 集中 ID 管理提供了一个单一位置来管理 Azure Synapse 用户，并简化权限管理。

### <a name="benefits"></a>优点

Azure Active Directory 的优点包括：

* 提供一个 SQL Server 身份验证的替代方法。
* 帮助阻止用户标识在服务器之间激增。
* 允许在单一位置中轮换密码
* 使用外部 (Azure AD) 组管理数据库权限。
* 通过启用 Azure Active Directory 支持的集成 Windows 身份验证和 o 管理中心捆绑形式的身份验证来消除存储密码。
* 使用包含的数据库用户在数据库级别对标识进行身份验证。
* 支持对连接到 SQL 池的应用程序进行基于令牌的身份验证。
* 通过对各种工具（包括 [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 和 [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)）的 Active Directory 通用身份验证支持多重身份验证。

> [!NOTE]
> Azure Active Directory 仍然相对较新，具有某些限制。 若要确保 Azure Active Directory 适用于环境，请参阅 [Azure AD features and limitations](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations)（Azure AD 功能和限制），尤其是那些需要额外考虑的内容。

### <a name="configuration-steps"></a>配置步骤

按照这些步骤配置 Azure Active Directory 身份验证。

1. 创建并填充 Azure Active Directory
2. 可选：关联或更改当前与 Azure 订阅关联的 Active Directory
3. 为 Azure Synapse 创建 Azure Active Directory 管理员
4. 配置客户端计算机
5. 在映射到 Azure AD 标识的数据库中创建包含的数据库用户
6. 使用 Azure AD 标识连接到 SQL 池

目前，Azure Active Directory 用户不会显示在 SSDT 对象资源管理器中。 解决方法是在 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 中查看这些用户。

### <a name="find-the-details"></a>查看详细信息

* 配置和使用 Azure Active Directory 身份验证的步骤与适用于 Azure SQL 数据库和 Azure Synapse 中的 Synapse SQL 的步骤几乎完全相同。 请遵循主题[使用 Azure Active Directory 身份验证连接到 SQL 数据库或 SQL 池](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中的详细步骤。
* 创建自定义数据库角色，并向角色添加用户。 然后授予角色具体权限。 有关详细信息，请参阅 [数据库引擎权限入门](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="next-steps"></a>后续步骤

若要开始使用 Visual Studio 和其他应用程序进行查询，请参阅[使用 Visual Studio 进行查询](sql-data-warehouse-query-visual-studio.md)。
