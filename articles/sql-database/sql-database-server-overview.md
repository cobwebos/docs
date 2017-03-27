---
title: "Azure SQL 数据库逻辑服务器概述 | Microsoft 文档"
description: "此页提供使用 Azure SQL 逻辑服务器的注意事项和指南。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 79a9e72d29b5522dc3960b79bae7876f21acb4c5
ms.openlocfilehash: 07181e5d35703cddf8a896badd45e7485c9e07a2


---
# <a name="azure-sql-database-logical-servers"></a>Azure SQL 数据库逻辑服务器

本主题提供使用 Azure SQL 逻辑服务器的注意事项和指南。 有关 Azure SQL 数据库的信息，请参阅 [SQL 数据库](sql-database-overview.md)。

## <a name="what-is-an-azure-sql-database-logical-server"></a>什么是 Aure SQL 数据库逻辑服务器？
Azure SQL 数据库逻辑服务器充当多个数据库的中心管理点。 在 SQL 数据库中，服务器是一个逻辑构造，它不同于你在本地环境中可能已熟悉的 SQL Server 实例。 具体而言，SQL 数据库服务不保证数据库相对于其逻辑服务器的位置，并且不公开实例级访问权限或功能。  

Azure 数据库逻辑服务器：

- 在 Azure 订阅中创建，但可以与其包含的资源一起移到另一个订阅
- 是数据库、弹性池和数据仓库的父资源
- 为数据库、弹性池、数据仓库提供命名空间
- 是具有强生存期语义的逻辑容器 - 删除服务器时将删除所包含的数据库、弹性池、数据仓库
- 参与 Azure 基于角色的访问控制 (RBAC)；服务器中的数据库、弹性池从服务器继承访问权限
- 在数据库和弹性池的标识中是位置靠前的元素，用于 Azure 资源管理目的（请参阅数据库和池的 URL 方案）
- 并置区域中的资源
- 为数据库访问提供连接终结点 (<serverName>.database.windows.net)
- 连接到 master 数据库通过 DMV 提供对与包含资源相关的元数据的访问 
- 提供应用于其数据库的管理策略的作用域：登录名、防火墙、审核、威胁检测等。 
- 受父订阅中的配额的限制（每个订阅六个服务器 - [请在此处参阅订阅限制](../azure-subscription-service-limits.md)）
- 为它包含的资源提供数据库配额和 DTU 配额的作用域（例如 V12 中的 45000 DTU）
- 是在包含资源上启用的功能的版本控制作用域（最新版本为 V12）
- 服务器级主体登录名可以管理服务器上的所有数据库
- 可以包含与本地 SQL Server 实例中的登录名类似的登录名，这些登录名有权访问服务器上的一个或多个数据库，并可以向这些登录名授予有限的管理权限。 有关详细信息，请参阅[登录名](sql-database-manage-logins.md)。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>如何连接到 Azure SQL 数据库逻辑服务器并向其进行身份验证？

- **身份验证和授权**：Azure SQL 数据库支持 SQL 身份验证和 Azure Active Directory 身份验证（但存在某些限制 - 请参阅[使用 Azure Active Directory 身份验证连接到 SQL 数据库](sql-database-aad-authentication.md)了解身份验证。 可以通过服务器的 master 数据库连接到 Azure SQL 数据库并进行身份验证，也可以直接连接到用户数据库并进行身份验证。 有关详细信息，请参阅[在 Azure SQL 数据库中管理数据库和登录名](sql-database-manage-logins.md)。 不支持 Windows 身份验证。 
- **TDS**：Microsoft Azure SQL 数据库支持表格格式数据流 (TDS) 协议客户端 7.3 版或更高版本。
- **TCP/IP**：只允许 TCP/IP 连接。
- **SQL 数据库防火墙**：为了帮助保护你的数据，在你指定哪些计算机具有访问权限之前，SQL 数据库防火墙将禁止所有对数据库服务器或其数据库的访问。 请参阅[防火墙](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>支持哪些排序规则？

Microsoft Azure SQL 数据库（包括 master 主数据）使用的默认数据库排序规则是 **SQL_LATIN1_GENERAL_CP1_CI_AS**。其中，**LATIN1_GENERAL** 是英语（美国），**CP1** 是代码页 1252，**CI** 是不区分大小写，**AS** 是区分重音。 建议在创建后不要更改 V12 数据库的排序规则。 有关排序规则的详细信息，请参阅 [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)。

## <a name="what-are-the-naming-requirements-for-database-objects"></a>数据库对象的命名要求有哪些？

所有新对象的名称都必须符合 SQL Server 标识符规则。 有关详细信息，请参阅[标识符](https://msdn.microsoft.com/library/ms175874.aspx)。

## <a name="what-features-are-supported"></a>支持哪些功能？

有关支持的功能的信息，请参阅[功能](sql-database-features.md)。 有关不支持某些类型的功能的原因，另请参阅 [Azure SQL 数据库 Transact-SQL 差异](sql-database-transact-sql-information.md)来了解详细背景。

## <a name="how-do-i-manage-a-logical-server"></a>如何管理逻辑服务器？

可以使用几种方法来管理 Azure SQL 数据库逻辑服务器：
- [Azure 门户](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库服务的信息，请参阅[什么是 SQL 数据库？](sql-database-technical-overview.md)
- 有关支持的功能的信息，请参阅[功能](sql-database-features.md)
- 有关 Azure SQL 数据库的概述，请参阅 [SQL 数据库概述](sql-database-overview.md)
- 有关 Transact-SQL 支持和差异的信息，请参阅 [Azure SQL 数据库 Transact-SQL 差异](sql-database-transact-sql-information.md)。
- 了解基于**服务层**的具体资源配额和限制的信息。 有关服务层的概述，请参阅 [SQL 数据库服务层](sql-database-service-tiers.md)。
- 有关安全概述，请参阅 [Azure SQL 数据库安全概述](sql-database-security-overview.md)。
- 有关驱动程序可用性和 SQL 数据库支持的信息，请参阅[用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)。




<!--HONumber=Feb17_HO1-->


