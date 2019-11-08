---
title: 机器学习服务的主要差异（预览）
description: 本主题介绍 Azure SQL 数据库机器学习服务（使用 R）和 SQL Server 机器学习服务之间的主要差异。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 1397f5d81ddf63740d733111b965a0517a2b917f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827470"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Azure SQL 数据库（预览版）中机器学习服务与 SQL Server 之间的主要差异

（预览版）中的 Azure SQL 数据库机器学习服务的功能与[SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)类似。 下面是一些重要的差异。

> [!IMPORTANT]
> Azure SQL 数据库机器学习服务当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="language-support"></a>语言支持

SQL Server 通过[扩展性框架](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)支持 R 和 Python。 SQL 数据库不支持这两种语言。 主要差异为：

- R 是 SQL 数据库中唯一受支持的语言。 目前不支持 Python。
- R 版本为 3.4.4。
- 无需通过 `external scripts enabled` 配置 `sp_configure`。 [注册](sql-database-machine-learning-services-overview.md#signup)后，即会为 SQL 数据库启用机器学习。

## <a name="package-management"></a>包管理

SQL 数据库和 SQL Server 之间的 R 程序包管理和安装工作存在差异。 差异为：

- 通过 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 或 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 安装 R 程序包。
- 程序包无法执行出站网络调用。 此限制类似于 SQL Server 中[机器学习服务的默认防火墙规则](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)，但不能在 SQL 数据库中进行更改。
- 不支持依赖于外部运行时（例如 Java）的程序包，也不支持需要访问 OS API 才能安装或使用的程序包。

## <a name="writing-to-a-temporary-table"></a>写入临时表

如果在 Azure SQL 数据库中使用 RODBC，则不能写入临时表，无论是在 `sp_execute_external_script` 会话内部还是外部创建。 解决方法是使用[RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata)和[rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) （使用 overwrite = FALSE 和 append = "rows"）写入在 `sp_execute_external_script` 查询之前创建的全局临时表。

## <a name="resource-governance"></a>资源调控

无法通过 [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 和外部资源池限制 R 资源。

在公共预览版期间，R 资源最多可设置为 SQL 数据库资源的20%，并且取决于所选的服务层。 有关详细信息，请参阅 [Azure SQL 数据库购买模型](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)。
### <a name="insufficient-memory-error"></a>内存不足错误

如果没有足够的可用内存用于 R，将收到一条错误消息。 常见的错误消息包括：

- 无法与 "R" 脚本的运行时通信，请求 id 为： * * * * * * *。 请检查 ' R ' 运行时的要求
- 执行 "sp_execute_external_script" 时出现 "R" 脚本错误，其 HRESULT 为0x80004004。 ...发生外部脚本错误： "。。无法在 C 函数 "R_AllocStringBuffer" 中分配内存（0 Mb）
- 发生外部脚本错误：错误：无法分配大小矢量。

内存使用情况取决于 R 脚本中使用的数量，以及正在执行的并行查询数。 如果收到上述错误，可以将数据库扩展到更高的服务层，以解决此问题。

## <a name="next-steps"></a>后续步骤

- 请参阅概述： [AZURE SQL 数据库与 R （预览版）机器学习服务](sql-database-machine-learning-services-overview.md)。
- 若要了解如何使用 R 查询 Azure SQL 数据库机器学习服务（预览版），请参阅[快速入门指南](sql-database-connect-query-r.md)。
- 若要开始了解一些简单的 R 脚本，请参阅[在 AZURE SQL 数据库中创建和运行简单的 r 脚本机器学习服务（预览版）](sql-database-quickstart-r-create-script.md)。
