---
title: SQL 数据库应用程序开发概述 | Microsoft Docs
description: 了解可用于连接到 SQL 数据库的连接库和最佳实践。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: efb6d932e616ada6b8dfff637af469c16fc2f293
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723402"
---
# <a name="sql-database-application-development-overview"></a>SQL 数据库应用程序开发概述

本文逐步讲解开发人员在编写代码以连接到 Azure SQL 数据库时应考虑的基本注意事项。 本文适用于 Azure SQL 数据库的所有部署模型（单一数据库、弹性池、托管实例）。

> [!TIP]
> 如果需要设置 Azure SQL 数据库，请查看[单一数据库](sql-database-single-database-quickstart-guide.md)和[托管实例](sql-database-managed-instance-quickstart-guide.md)的入门指南。
>

## <a name="language-and-platform"></a>语言和平台

可以使用各种[编程语言和平台](sql-database-connect-query.md)连接和查询 Azure SQL 数据库。 可以找到可用于连接到 Azure SQL 数据库的[示例应用程序](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0)。

你可以利用类似 [cheetah](https://github.com/wunderlist/cheetah)、[sql-cli](https://www.npmjs.com/package/sql-cli)、[VS Code](https://code.visualstudio.com/) 的开源工具。 此外，Azure SQL 数据库可与 Microsoft 工具（如 [Visual Studio](https://www.visualstudio.com/downloads/) 和 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)）配合使用。 还可以使用 Azure 门户、PowerShell 和 REST API 帮助提高工作效率。

## <a name="authentication"></a>Authentication

对 Azure SQL 数据库的访问使用登录名和防火墙进行保护。 Azure SQL 数据库支持 SQL Server 和 [Azure Active Directory (AAD) 身份验证](sql-database-aad-authentication.md)用户和登录名。 AAD 登录名仅在托管实例中可用。 

详细了解[管理数据库访问和登录名](sql-database-manage-logins.md)。

## <a name="connections"></a>连接

在客户端连接逻辑中，将默认超时替换为 30 秒。 默认值 15 秒对于依赖于 Internet 的连接而言太短。

如果在使用[连接池](https://msdn.microsoft.com/library/8xx3tyca.aspx)，请确保在程序不活跃地使用连接时将其关闭，而不是准备重用它。

避免长时间运行的事务，因为任何基础结构或连接故障可能会使事务回滚。 如果可能，将事务拆分为多个较小事务，并使用[批处理改进性能](sql-database-use-batching-to-improve-performance.md)。

## <a name="resiliency"></a>复原

Azure SQL 数据库是一种云服务，在其中可能会遇到在底层基础结构中或云实体之间的通信中发生的暂时性错误。 尽管 Azure SQL 数据库在发生暂时性基础结构故障时可复原，但这些故障可能会影响连接。 如果在连接到 SQL 数据库时发生暂时性错误，代码应[重试调用](sql-database-connectivity-issues.md)。 建议让重试逻辑使用退让逻辑，这样就不会因为多个客户端同时重试而对 SQL 数据库造成混乱。 重试逻辑取决于 [SQL 数据库客户端程序的错误消息](sql-database-develop-error-messages.md)。

有关如何为 Azure SQL 数据库上的计划内维护事件做好准备的详细信息，请参阅[规划 Azure SQL 数据库中的 Azure 维护事件](sql-database-planned-maintenance.md)。

## <a name="network-considerations"></a>网络注意事项

- 在托管客户端程序的计算机上，确保防火墙允许端口 1433 上的传出 TCP 通信。  详细信息：[配置 Azure SQL 数据库防火墙](sql-database-configure-firewall-settings.md)。
- 客户端在 Azure 虚拟机 (VM) 上运行时，如果客户端程序连接到 SQL 数据库，则必须打开 VM 上的某些端口范围。 详细信息：[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)。
- 与 Azure SQL 数据库建立的客户端连接有时会绕过代理直接与数据库交互。 除 1433 以外的端口变得非常重要。 有关详细信息，请参阅 [Azure SQL 数据库连接体系结构](sql-database-develop-direct-route-ports-adonet-v12.md)和[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-connectivity-architecture.md)。
- 托管实例的网络配置，请参阅[托管实例的网络配置](sql-database-howto-managed-instance.md#network-configuration)。

## <a name="next-steps"></a>后续步骤

浏览 [SQL 数据库的所有功能](sql-database-technical-overview.md)。
