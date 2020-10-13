---
title: 配置服务器参数 - Azure PowerShell - Azure Database for PostgreSQL
description: 本文介绍如何使用 PowerShell 在 Azure Database for PostgreSQL 中配置服务参数。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0fa49714365c00060ef7f11f5c9646141f707f4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707806"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>使用 PowerShell 自定义 Azure Database for PostgreSQL 服务器参数

可以使用 PowerShell 列出、显示和更新 Azure Database for PostgreSQL 服务器的配置参数。 在服务器级别会公开引擎配置的一个子集，并可以进行修改。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for PostgreSQL 服务器](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az.PostgreSql PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.PostgreSql -AllowPrerelease`。
> Az.PostgreSql PowerShell 模块正式版推出后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

如果选择在本地使用 PowerShell，请使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>列出 Azure Database for PostgreSQL 服务器的服务器配置参数

若要列出服务器中的所有可修改参数及其值，请运行 `Get-AzPostgreSqlConfiguration` cmdlet。

以下示例列出了资源组 myresourcegroup 中服务器 mydemoserver 的服务器配置参数。

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

有关每个列出参数的定义，请参阅[环境变量](https://www.postgresql.org/docs/12/libpq-envars.html)上的 PostgreSQL 引用部分。

## <a name="show-server-configuration-parameter-details"></a>显示服务器配置参数详细信息

若要显示有关服务器的特定配置参数的详细信息，请运行 `Get-AzPostgreSqlConfiguration` cmdlet 并指定 Name 参数。

本示例显示了资源组“myresourcegroup”下服务器 mydemoserver 的服务器配置参数 slow\_query\_log 的详细信息。

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>修改服务器配置参数值

还可以修改某个服务器配置参数的值，这会更新 PostgreSQL 服务器引擎的基础配置值。 若要更新配置，请使用 `Update-AzPostgreSqlConfiguration` cmdlet。

更新资源组“myresourcegroup”下服务器 mydemoserver 的服务器配置参数 slow\_query\_log。

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 实现 Azure Database for PostgreSQL 服务器存储的自动增长](howto-auto-grow-storage-powershell.md)。
