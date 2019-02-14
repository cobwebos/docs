---
title: Azure SQL 数据库中的机器学习服务（使用 R，预览版）的主要差异概览
description: 本主题介绍 Azure SQL 数据库机器学习服务（使用 R）和 SQL Server 机器学习服务之间的主要差异。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: adc303e9b375aaa6f37e9e79ea5434675c75523d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824792"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Azure SQL 数据库中的机器学习服务和 SQL Server 中的机器学习服务之间的主要差异

Azure SQL 数据库中机器学习服务（使用 R）的功能类似于 [SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)。 以下是其中一些主要差异。

## <a name="language-support"></a>语言支持

SQL Server 通过[扩展性框架](https://docs.microsoft.com/en-us/sql/advanced-analytics/concepts/extensibility-framework)支持 R 和 Python。 SQL 数据库不支持这两种语言。 主要差异为：

- R 是 SQL 数据库中唯一受支持的语言。 目前不支持 Python。
- R 版本为 3.4.4。
- 无需通过 `sp_configure` 配置 `external scripts enabled`。 [注册](sql-database-machine-learning-services-overview.md#signup)后，即会为 SQL 数据库启用机器学习。

## <a name="package-management"></a>包管理

SQL 数据库和 SQL Server 之间的 R 程序包管理和安装工作存在差异。 差异为：

- 通过 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 或 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 安装 R 程序包。
- 程序包无法执行出站网络调用。 此限制类似于 SQL Server 中的[机器学习服务默认防火墙规则](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)，但在 SQL 数据库中不能更改。
- 不支持依赖于外部运行时（例如 Java）的程序包，也不支持需要访问 OS API 才能安装或使用的程序包。

## <a name="resource-governance"></a>资源调控

无法通过 [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 和外部资源池限制 R 资源。 R 资源是 SQL 数据库资源的一部分，具体取决于选择的服务层。 有关详细信息，请参阅 [Azure SQL 数据库购买模型](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)。

## <a name="security-isolation"></a>安全隔离

在 Azure SQL 数据库中，SQL 平台抽象层 (SQLPAL) 提供外部进程隔离。 此隔离为运行 R 脚本提供额外的安全层。

## <a name="next-steps"></a>后续步骤

- 有关一般信息，请参阅 [SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics)文档
- 如需了解如何在 Azure SQL 数据库中使用机器学习服务（使用 R），请参阅[快速入门指南](sql-database-connect-query-r.md)。
- 有关详细信息，请参阅 [SQL Server R 语言教程](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)