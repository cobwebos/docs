---
title: 配置服务器参数-Azure PowerShell-Azure Database for MySQL
description: 本文介绍如何使用 PowerShell 在 Azure Database for MySQL 中配置服务参数。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 4/29/2020
ms.openlocfilehash: 0de816d25bbc1563885413d8dbd52dc7bda7d538
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615079"
---
# <a name="customize-azure-database-for-mysql-server-parameters-using-powershell"></a>使用 PowerShell 自定义 Azure Database for MySQL 服务器参数

可以使用 PowerShell 列出、显示和更新 Azure Database for MySQL 服务器的配置参数。 在服务器级别会公开引擎配置的一个子集，并可以进行修改。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az PowerShell 模块处于预览阶段，但你必须使用以下命令从 Az PowerShell 模块单独安装它： `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az PowerShell 模块公开发布后，它将成为未来 Az PowerShell 模块版本的一部分，并从 Azure Cloud Shell 中的本机提供。

如果选择在本地使用 PowerShell，请使用[AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>列出 Azure Database for MySQL 服务器的服务器配置参数

若要列出服务器中的所有可修改参数及其值，请`Get-AzMySqlConfiguration`运行 cmdlet。

以下示例列出了资源组**myresourcegroup**中服务器**mydemoserver**的服务器配置参数。

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

有关每个列出参数的定义，请参阅[服务器系统变量](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)上的 MySQL 引用部分。

## <a name="show-server-configuration-parameter-details"></a>显示服务器配置参数详细信息

若要显示服务器的某个特定配置参数的详细信息，请`Get-AzMySqlConfiguration`运行 cmdlet 并指定**Name**参数。

此示例显示了 "资源组**myresourcegroup**" 下服务器**mydemoserver**的**\_慢速查询\_日志**服务器配置参数的详细信息。

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>修改服务器配置参数值

此外，你还可以修改某个服务器配置参数的值，这会更新 MySQL 服务器引擎的基础配置值。 若要更新配置，请使用`Update-AzMySqlConfiguration` cmdlet。

若要更新资源组**myresourcegroup**下的服务器**mydemoserver**的**\_慢速查询\_日志**服务器配置参数。

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 在 Azure Database for MySQL 服务器中自动增长存储](howto-auto-grow-storage-powershell.md)。
