---
title: "设计你的第一个 Azure SQL 数据库 - C# | Microsoft Docs"
description: "学习设计你的第一个 Azure SQL 数据库，并使用 ADO.NET 通过 C# 程序连接到它。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/20/2017
ms.author: genemi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 069656b495929d66f6a47b38141f56b6bf0ac92f
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>设计 Azure SQL 数据库，并使用 C# 和 ADO.NET 进行连接

Azure SQL 数据库是 Microsoft 云（“Azure”）中的关系数据库即服务 (DBaaS)。 本教程介绍如何使用 Azure 门户和 ADO.NET 来完成以下操作： 

> [!div class="checklist"]
> * 在 Azure 门户中创建数据库
> * 在 Azure 门户中设置服务器级别的防火墙规则
> * 使用 ADO.NET 连接到数据库
> * 使用 ADO.NET 创建表
> * 使用 ADO.NET 插入数据 
> * 查询数据 ADO.NET

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>后续步骤

可以使用以下后续步骤：

- [在 C# 程序中使用 LINQ 进行 SQL 查询](https://msdn.microsoft.com/library/bb425822.aspx)
- [将 SQL Server 数据库迁移至 Azure SQL 数据库](sql-database-migrate-your-sql-server-database.md)

