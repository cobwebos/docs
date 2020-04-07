---
title: 身份验证
description: 了解如何使用 Azure 活动目录 （Azure AD） 或 SQL Server 身份验证对 Azure 同步分析进行身份验证。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: fcd9607cd630f41c67c655c2e8016254600b52e2
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745391"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>对 Azure 同步分析进行身份验证

了解如何使用 Azure 活动目录 （AAD） 或 SQL Server 身份验证对 Azure 突触中的 SQL 分析进行身份验证。

要连接到 SQL 池，必须传递安全凭据以进行身份验证。 建立连接时，特定的连接设置已配置为建立查询会话的一部分。  

有关安全性以及如何启用与数据仓库的连接的详细信息，请参阅[保护数据库文档](sql-data-warehouse-overview-manage-security.md)。

## <a name="sql-authentication"></a>SQL 身份验证

要连接到 SQL 池，必须提供以下信息：

* 完全限定的服务器名称
* 指定 SQL 身份验证
* 用户名
* 密码
* 默认数据库（可选）

默认情况下，您的连接连接到*主*数据库，而不是用户数据库。 若要连接到用户数据库，可以选择执行以下两项操作之一：

* 在 SSDT、SSMS 或应用程序连接字符串中将服务器注册到 SQL Server 对象资源管理器时指定默认数据库。 例如，包含 ODBC 连接的 InitialCatalog 参数。
* 在 SSDT 中创建会话之前先突出显示用户数据库。

> [!NOTE]
> 不支持使用 Transact-SQL 语句 **USE MyDatabase;** 更改连接的数据库。 有关使用 SSDT 连接到 SQL 池的指导，请参阅[使用 Visual Studio 的查询](sql-data-warehouse-query-visual-studio.md)一文。

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 身份验证

[Azure 活动目录](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)身份验证是使用 Azure 活动目录 （Azure AD） 中的标识连接到 SQL 池的机制。 通过 Azure Active Directory 身份验证，可以在一个中心位置中集中管理数据库用户和其他 Microsoft 服务的标识。 中央 ID 管理提供了管理 Azure 突触用户的单一位置，并简化了权限管理。

### <a name="benefits"></a>优点

Azure Active Directory 的优点包括：

* 提供一个 SQL Server 身份验证的替代方法。
* 帮助阻止用户标识在数据库服务器之间激增。
* 允许在单一位置中轮换密码
* 使用外部 （Azure AD） 组管理数据库权限。
* 通过启用集成的 Windows 身份验证和 Azure Active Directory 支持的其他形式的身份验证来消除存储密码。
* 使用包含的数据库用户在数据库级别对标识进行身份验证。
* 支持连接到 SQL 池的应用程序基于令牌的身份验证。
* 支持通过各种工具（包括 [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 和 [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)）的 Active Directory 通用身份验证进行多重身份验证。

> [!NOTE]
> Azure Active Directory 仍然相对较新，具有某些限制。 若要确保 Azure Active Directory 适用于当前环境，请参阅 [Azure AD 功能和限制](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations)，尤其是那些需要额外考虑的内容。

### <a name="configuration-steps"></a>配置步骤

按照这些步骤配置 Azure Active Directory 身份验证。

1. 创建并填充 Azure Active Directory
2. 可选：关联或更改当前与 Azure 订阅关联的活动目录
3. 为 Azure 突触创建 Azure 活动目录管理员
4. 配置客户端计算机
5. 在映射到 Azure AD 标识的数据库中创建包含的数据库用户
6. 使用 Azure AD 标识连接到 SQL 池

目前，Azure Active Directory 用户不会显示在 SSDT 对象资源管理器中。 解决方法是在 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#azure-ad-features-and-limitations) 中查看这些用户。

### <a name="find-the-details"></a>查看详细信息

* 在 Azure Synaps 中配置和使用 Azure 活动目录身份验证的步骤几乎相同。 按照主题"通过使用 Azure[活动目录身份验证连接到 SQL 数据库或 SQL 池"](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中的详细步骤。
* 创建自定义数据库角色，并向角色添加用户。 然后，向角色授予具体权限。 有关详细信息，请参阅[数据库引擎权限入门](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="next-steps"></a>后续步骤

要开始使用 Visual Studio 和其他应用程序查询，请参阅[使用 Visual Studio 查询](sql-data-warehouse-query-visual-studio.md)。
