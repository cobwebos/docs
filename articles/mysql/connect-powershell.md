---
title: 使用 PowerShell 生成连接字符串 - Azure Database for MySQL
description: 本快速入门提供一个 Azure PowerShell 示例，用于生成连接字符串以便连接到 Azure Database for MySQL。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/3/2020
ms.openlocfilehash: 9de569e09e1b8bcf29f47077cbe15f0a6c40ddd1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544911"
---
# <a name="azure-database-for-mysql-generate-a-connection-string-with-powershell"></a>Azure Database for MySQL：使用 PowerShell 生成连接字符串

本文演示如何为 Azure Database for MySQL 服务器生成连接字符串。 可以使用连接字符串从许多不同的应用程序连接到 Azure Database for MySQL。

## <a name="requirements"></a>要求

本文使用以下指南中创建的资源作为起点：

* [快速入门：使用 PowerShell 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>获取连接字符串

`Get-AzMySqlConnectionString` cmdlet 用于生成连接字符串，以便将应用程序连接到 Azure Database for MySQL。 下面的示例从 mydemoserver 返回 PHP 客户端的连接字符串。

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [使用 PowerShell 设计 Azure Database for MySQL](tutorial-design-database-using-powershell.md)
