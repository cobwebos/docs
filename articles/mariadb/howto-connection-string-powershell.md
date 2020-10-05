---
title: 使用 PowerShell 生成连接字符串 - Azure Database for MariaDB
description: 本文提供一个 Azure PowerShell 示例，用于生成连接到 Azure Database for MariaDB 所用的连接字符串。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: b08a1c60b1225b2786ea869f96ac40cc651c82f3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88185598"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>如何使用 PowerShell 生成 Azure Database for MariaDB 连接字符串

本文演示如何为 Azure Database for MariaDB 服务器生成连接字符串。 可以使用连接字符串从许多不同的应用程序连接到 Azure Database for MariaDB。

## <a name="requirements"></a>要求

本文使用以下指南中创建的资源作为起点：

* [快速入门：使用 PowerShell 创建 Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>获取连接字符串

`Get-AzMariaDbConnectionString` cmdlet 用于生成连接字符串，以便将应用程序连接到 Azure Database for MariaDB。 下面的示例从 mydemoserver 返回 PHP 客户端的连接字符串。

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [使用 PowerShell 自定义 Azure Database for MariaDB 服务器参数](howto-configure-server-parameters-using-powershell.md)
