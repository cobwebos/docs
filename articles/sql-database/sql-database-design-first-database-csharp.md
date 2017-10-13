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
ms.custom: develop databases, mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 08/25/2017
ms.author: genemi
ms.openlocfilehash: 1deffdd05c195597f4c63e3b5570ed94566b1f7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>设计 Azure SQL 数据库，并使用 C# 和 ADO.NET 进行连接

Azure SQL 数据库与 Microsoft 云（“Azure”）中的数据库即服务 (DBaaS) 相关。 本教程介绍如何将 Azure 门户、ADO.NET 与 Visual Studio 结合使用来完成以下操作： 

> [!div class="checklist"]
> * 在 Azure 门户中创建数据库
> * 在 Azure 门户中设置服务器级防火墙规则
> * 使用 ADO.NET 和 Visual Studi 连接至数据库
> * 使用 ADO.NET 创建表
> * 使用 ADO.NET 插入、更新和删除数据 
> * 查询数据 ADO.NET

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

已安装 [Visual Studio Community 2017、Visual Studio Professional 2017 或 Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/)。

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>后续步骤

本教程介绍了基本数据库任务，例如创建数据库和表、负载和查询数据，以及将数据库还原到以前的时间点。 你已了解如何：
> [!div class="checklist"]
> * 创建数据库
> * 设置防火墙规则。
> * 使用 [Visual Studio 和 C#](sql-database-connect-query-dotnet-visual-studio.md) 连接至数据库
> * 创建表
> * 插入、更新和删除数据
> * 查询数据

请转到下一教程，了解数据迁移。

> [!div class="nextstepaction"]
>[将 SQL Server 数据库迁移至 Azure SQL 数据库](sql-database-migrate-your-sql-server-database.md)

