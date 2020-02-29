---
title: Authentication
description: 了解如何使用 Azure Active Directory （AAD）或 SQL Server 身份验证向 Azure Synapse Analytics 进行身份验证。
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 235d13cfd6f33830d832a6a79e3bc1c78bbfe53e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195968"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>对 Azure Synapse Analytics 进行身份验证
了解如何使用 Azure Active Directory （AAD）或 SQL Server 身份验证向 Azure Synapse 中的 SQL Analytics 进行身份验证。

若要连接到 SQL 池，必须传入安全凭据进行身份验证。 建立连接时，特定的连接设置已配置为建立查询会话的一部分。  

有关安全性以及如何启用与数据仓库的连接的详细信息，请参阅[保护数据库文档](sql-data-warehouse-overview-manage-security.md)。

## <a name="sql-authentication"></a>SQL 身份验证
若要连接到 SQL 池，必须提供以下信息：

* 完全限定的服务器名称
* 指定 SQL 身份验证
* 用户名
* 密码
* 默认数据库（可选）

默认情况下，连接连接到*master*数据库而不是用户数据库。 若要连接到用户数据库，可以选择执行以下两项操作之一：

* 在 SSDT、SSMS 或应用程序连接字符串中将服务器注册到 SQL Server 对象资源管理器时指定默认数据库。 例如，包含 ODBC 连接的 InitialCatalog 参数。
* 在 SSDT 中创建会话之前先突出显示用户数据库。

> [!NOTE]
> 不支持使用 Transact-SQL 语句 **USE MyDatabase;** 更改连接的数据库。 有关使用 SSDT 连接到 SQL 池的指南，请参阅[使用 Visual Studio 进行查询](sql-data-warehouse-query-visual-studio.md)一文。
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 身份验证
[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)身份验证是通过使用 Azure Active Directory （Azure AD）中的标识连接到 SQL 池的机制。 通过 Azure Active Directory 身份验证，可以在一个中心位置中集中管理数据库用户和其他 Microsoft 服务的标识。 中央 ID 管理提供一个单一位置来管理 Azure Synapse 用户并简化权限管理。 

### <a name="benefits"></a>优点
Azure Active Directory 的优点包括：

* 提供一个 SQL Server 身份验证的替代方法。
* 帮助阻止用户标识在数据库服务器之间激增。
* 允许在单一位置中轮换密码
* 使用外部 (AAD) 组管理数据库权限。
* 通过启用集成的 Windows 身份验证和 Azure Active Directory 支持的其他形式的身份验证来消除存储密码。
* 使用包含的数据库用户在数据库级别对标识进行身份验证。
* 支持对连接到 SQL 池的应用程序进行基于令牌的身份验证。
* 通过 Active Directory 针对各种工具（包括[SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md)和[SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)）的通用身份验证来支持多重身份验证。

> [!NOTE]
> Azure Active Directory 仍然相对较新，具有某些限制。 若要确保 Azure Active Directory 适用于当前环境，请参阅 [Azure AD 功能和限制](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)，尤其是那些需要额外考虑的内容。
> 
> 

### <a name="configuration-steps"></a>配置步骤
按照这些步骤配置 Azure Active Directory 身份验证。

1. 创建并填充 Azure Active Directory
2. 可选：关联或更改当前与 Azure 订阅关联的活动目录
3. 为 Azure Synapse 创建 Azure Active Directory 管理员
4. 配置客户端计算机
5. 在映射到 Azure AD 标识的数据库中创建包含的数据库用户
6. 使用 Azure AD 标识连接到 SQL 池

目前，Azure Active Directory 用户不会显示在 SSDT 对象资源管理器中。 解决方法是在 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) 中查看这些用户。

### <a name="find-the-details"></a>查看详细信息
* 对于 azure Synapse 中的 Azure SQL 数据库和 SQL Analytics，配置和使用 Azure Active Directory 身份验证的步骤几乎完全相同。 按照[使用 Azure Active Directory 身份验证连接到 Sql 数据库或 Sql 池](../sql-database/sql-database-aad-authentication.md)主题中的详细步骤进行操作。
* 创建自定义数据库角色，并向角色添加用户。 然后，向角色授予具体权限。 有关详细信息，请参阅[数据库引擎权限入门](https://msdn.microsoft.com/library/mt667986.aspx)。

## <a name="next-steps"></a>后续步骤
若要开始查询 Visual Studio 和其他应用程序，请参阅[通过 Visual studio 进行查询](sql-data-warehouse-query-visual-studio.md)。
