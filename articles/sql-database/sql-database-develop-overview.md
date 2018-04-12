---
title: SQL 数据库应用程序开发概述 | Microsoft Docs
description: 了解可用于连接到 SQL 数据库的连接库和最佳实践。
services: sql-database
author: stevestein
manager: craigg
ms.reviewer: genemi
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: dc78a43fb99428eac921dd0cfffe5547f523a133
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="sql-database-application-development-overview"></a>SQL 数据库应用程序开发概述
本文逐步讲解开发人员在编写代码以连接到 Azure SQL 数据库时应考虑的基本注意事项。

> [!TIP]
> 若需通过教程来了解如何创建服务器、创建基于服务器的防火墙、查看服务器属性、使用 SQL Server Management Studio 进行连接、查询 master 数据库、创建示例数据库和空数据库、查询数据库属性、使用 SQL Server Management Studio 进行连接，以及查询示例数据库，请参阅[入门教程](sql-database-get-started-portal.md)。
>

## <a name="language-and-platform"></a>语言和平台
为各种编程语言和平台提供了代码示例。 可在以下位置找到代码示例的链接： 

* 详细信息：[用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)。

## <a name="tools"></a>工具 
可以利用开源工具，如 [cheetah](https://github.com/wunderlist/cheetah)、[sql-cli](https://www.npmjs.com/package/sql-cli)、[VS Code](https://code.visualstudio.com/)。 此外，Azure SQL 数据库可与 Microsoft 工具（如 [Visual Studio](https://www.visualstudio.com/downloads/) 和 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)）配合使用。  还可以使用 Azure 管理门户、PowerShell 和 REST API 帮助提高工作效率。

## <a name="resource-limitations"></a>资源限制
Azure SQL 数据库使用两种不同的机制管理可用于数据库的资源：资源调控和强制限制。

* 详细信息：[Azure SQL 数据库资源限制](sql-database-service-tiers.md)。

## <a name="security"></a>安全
Azure SQL 数据库提供用于在 SQL 数据库中限制访问、保护数据和监视活动的资源。

* 详细信息：[保护 SQL 数据库](sql-database-security-overview.md)。

## <a name="authentication"></a>身份验证
* Azure SQL 数据库支持 SQL Server 身份验证用户和登录名，以及 [Azure Active Directory 身份验证](sql-database-aad-authentication.md)用户和登录名。
* 需要指定一个特定的数据库，而不要默认使用 master 数据库。
* 无法使用 SQL 数据库上的 Transact-SQL **USE myDatabaseName;** 语句切换到其他数据库。
* 详细信息：[SQL 数据库安全：管理数据库的访问和登录安全](sql-database-manage-logins.md)。

## <a name="resiliency"></a>复原
如果在连接到 SQL 数据库时发生暂时性错误，代码应重试调用。  建议让重试逻辑使用退让逻辑，这样就不会因为多个客户端同时重试而对 SQL 数据库造成混乱。

* 代码示例：有关演示重试逻辑的代码示例，请在以下位置参阅所选语言的示例：[用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)。
* 详细信息：[SQL 数据库客户端程序的错误消息](sql-database-develop-error-messages.md)。

## <a name="managing-connections"></a>管理连接
* 在客户端连接逻辑中，将默认超时替换为 30 秒。  默认值 15 秒对于依赖于 Internet 的连接而言太短。
* 如果在使用[连接池](http://msdn.microsoft.com/library/8xx3tyca.aspx)，请确保在程序不活跃地使用连接时将其关闭，而不是准备重用它。

## <a name="network-considerations"></a>网络注意事项
* 在托管客户端程序的计算机上，确保防火墙允许端口 1433 上的传出 TCP 通信。  详细信息：[配置 Azure SQL 数据库防火墙](sql-database-configure-firewall-settings.md)。
* 客户端在 Azure 虚拟机 (VM) 上运行时，如果客户端程序连接到 SQL 数据库，则必须打开 VM 上的某些端口范围。 详细信息：[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)。
* 与 Azure SQL 数据库建立的客户端连接有时会绕过代理直接与数据库交互。 除 1433 以外的端口变得非常重要。 有关详细信息，请参阅 [Azure SQL 数据库连接体系结构](sql-database-develop-direct-route-ports-adonet-v12.md)和[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-connectivity-architecture.md)。

## <a name="data-sharding-with-elastic-scale"></a>数据分片和弹性缩放
弹性缩放简化了扩展（和缩减）过程。 

* [包含 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。
* [数据依赖型路由](sql-database-elastic-scale-data-dependent-routing.md)。
* [Azure SQL 数据库 Elastic Scale 预览版入门](sql-database-elastic-scale-get-started.md)。

## <a name="next-steps"></a>后续步骤
浏览所有 [SQL 数据库的功能](sql-database-technical-overview.md)。
