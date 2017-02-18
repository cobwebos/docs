---
title: "在 Windows上使用 PHP 连接到 SQL 数据库 | Microsoft 文档"
description: "演示一个示例 PHP 程序，该程序可以从 Windows 客户端连接到 Azure SQL 数据库，并与客户端所需的软件组件建立链接。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: e1c3e7e0f6ca097e3ee41995defe5c1df666d39e


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>在 Windows上使用 PHP 连接到 SQL 数据库
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主题演示了如何从 Windows 运行的、以 PHP 编写的客户端应用程序连接到 Azure SQL 数据库。

## <a name="step-1--configure-development-environment"></a>步骤 1：配置开发环境
[配置用于 PHP 开发的开发环境](https://docs.microsoft.com/sql/connect/php/step-1-configure-development-environment-for-php-development/)

## <a name="step-2-create-a-sql-database"></a>步骤 2：创建 SQL 数据库
请参阅[入门页](sql-database-get-started.md)，以了解如何创建示例数据库。  必须根据指南创建 **AdventureWorks** 数据库模板。 下面所示的示例只适用于 **AdventureWorks 架构**。

## <a name="step-3-get-connection-details"></a>步骤 3：获取连接详细信息
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>步骤 4：运行示例代码
* [使用 PHP 连接到 SQL 以进行概念认证](https://docs.microsoft.com/sql/connect/php/step-3-proof-of-concept-connecting-to-sql-using-php/)
* [使用 PHP 弹性连接到 SQL](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php/)

## <a name="next-steps"></a>后续步骤
* 参阅 [SQL 数据库开发概述](sql-database-develop-overview.md)
* 有关 [Microsoft PHP Driver for SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server/) 的详细信息
* 有关 PHP 安装和用法的详细信息，请参阅使用 [PHP 访问 SQL Server 数据库](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx)。

## <a name="additional-resources"></a>其他资源
* [包含 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。




<!--HONumber=Feb17_HO1-->


