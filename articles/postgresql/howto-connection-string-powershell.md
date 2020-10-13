---
title: 使用 PowerShell 生成连接字符串 - Azure Database for PostgreSQL
description: 本文提供一个 Azure PowerShell 示例，用于生成连接到 Azure Database for PostgreSQL 所用的连接字符串。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708163"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>如何使用 PowerShell 生成 Azure Database for PostgreSQL 连接字符串

本文演示如何为 Azure Database for PostgreSQL 服务器生成连接字符串。 可以使用连接字符串从许多不同的应用程序连接到 Azure Database for PostgreSQL。

## <a name="requirements"></a>要求

本文使用以下指南中创建的资源作为起点：

* [快速入门：使用 PowerShell 创建 Azure Database for PostgreSQL 服务器](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>获取连接字符串

`Get-AzPostgreSqlConnectionString` cmdlet 用于生成连接字符串，以便将应用程序连接到 Azure Database for PostgreSQL。 下面的示例从 mydemoserver 返回 PHP 客户端的连接字符串。

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

`Client` 参数的有效值包括：

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 自定义 Azure Database for PostgreSQL 服务器参数](howto-configure-server-parameters-using-powershell.md)
