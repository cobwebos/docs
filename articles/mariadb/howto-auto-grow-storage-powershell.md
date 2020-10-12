---
title: 自动增长存储 - Azure PowerShell - Azure Database for MariaDB
description: 本文介绍如何使用 PowerShell 实现 Azure Database for MariaDB 存储的自动增长。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2d03a67fc8a8172573598662ad9770b28493e9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497097"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>使用 PowerShell 实现 Azure Database for MariaDB 服务器存储的自动增长

本文介绍如何将 Azure Database for MariaDB 服务器存储配置为在不影响工作负荷的情况下增长。

存储自动增长可防止服务器[达到存储限制](/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)并变为只读。 对于预配存储为 100 GB 或更低的服务器，当可用空间低于 10% 时，大小将增加 5 GB。 对于预配存储超过 100 GB 的服务器，当可用空间低于 10 GB 时，大小将增加 5%。 最大存储限制的应用方式遵循 [Azure Database for MariaDB 定价层](/azure/mariadb/concepts-pricing-tiers#storage)的存储部分内容中所指定的方式。

> [!IMPORTANT]
> 请记住，存储只能增加，不能减少。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az.MariaDb PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.MariaDb -AllowPrerelease`。
> Az.MariaDb PowerShell 模块正式版推出后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

如果选择在本地使用 PowerShell，请使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。

## <a name="enable-mariadb-server-storage-auto-grow"></a>启用 MariaDB 服务器存储自动增长

使用以下命令在现有服务器上启用服务器自动增长存储：

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

使用以下命令创建新服务器时启用服务器自动增长存储：

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用 PowerShell 在 Azure Database for MariaDB 中创建和管理只读副本](howto-read-replicas-powershell.md)。
