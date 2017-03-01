---
title: "使用 Node.js 连接到 SQL 数据库| Microsoft 文档"
description: "演示了一个可以用来连接到 Azure SQL 数据库的 Node.js 代码示例。"
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: 6c3f6bc00d147bc498a859560ffe4719ecf888bf


---
# <a name="connect-to-sql-database-by-using-nodejs"></a>使用 Node.js 连接到 SQL 数据库
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主题说明如何使用 Node.js 来连接和查询 Azure SQL 数据库。 可以从 Windows、Ubuntu Linux 或 Mac 平台运行此示例。

## <a name="step-1-configure-development-environment"></a>步骤 1：配置开发环境
[使用 Tedious Node.js Driver for SQL Server 的先决条件](https://docs.microsoft.com/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/)

## <a name="step-2-create-a-sql-database"></a>步骤 2：创建 SQL 数据库
请参阅[入门页](sql-database-get-started.md)，以了解如何创建示例数据库。  必须根据指南创建 **AdventureWorks** 数据库模板。 下面所示的示例只适用于 **AdventureWorks 架构**。

## <a name="step-3-get-connection-details"></a>步骤 3：获取连接详细信息
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>步骤 4：运行示例代码
[使用 Node.js 连接到 SQL 以进行概念认证](https://docs.microsoft.com/sql/connect/node-js/step-3-proof-of-concept-connecting-to-sql-using-node-js/)

## <a name="next-steps"></a>后续步骤
* 参阅 [SQL 数据库开发概述](sql-database-develop-overview.md)
* 有关 [Microsoft Node.js Driver for SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/) 的详细信息

## <a name="additional-resources"></a>其他资源
* [包含 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。




<!--HONumber=Feb17_HO1-->


