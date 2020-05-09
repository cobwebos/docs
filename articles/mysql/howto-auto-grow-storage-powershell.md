---
title: 自动增长存储-Azure PowerShell-Azure Database for MySQL
description: 本文介绍如何在 Azure Database for MySQL 中使用 PowerShell 启用自动增长存储。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: c8a19fe338af14f97e0eb191d7b57e840c71e400
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612718"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>使用 PowerShell 自动增长 Azure Database for MySQL server 中的存储

本文介绍如何将 Azure Database for MySQL 服务器存储配置为在不影响工作负荷的情况下增长。

存储自动增长会阻止服务器[达到存储限制](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)，使其变为只读。 对于预配存储为 100 GB 或更低的服务器，当可用空间低于10% 时，大小将增加 5 GB。 对于预配存储超过 100 GB 的服务器，当可用空间低于 10 GB 时，大小将增加5%。 最大存储限制适用于 " [Azure Database for MySQL 定价层](/azure/mysql/concepts-pricing-tiers#storage)的" 存储 "部分中指定的限制。

> [!IMPORTANT]
> 请记住，存储只能增加，不能减少。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az PowerShell 模块处于预览阶段，但你必须使用以下命令从 Az PowerShell 模块单独安装它： `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az PowerShell 模块公开发布后，它将成为未来 Az PowerShell 模块版本的一部分，并从 Azure Cloud Shell 中的本机提供。

如果选择在本地使用 PowerShell，请使用[AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。

## <a name="enable-mysql-server-storage-auto-grow"></a>启用 MySQL server 存储自动增长

使用以下命令在现有服务器上启用服务器自动增长存储：

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

使用以下命令创建新服务器时启用服务器自动增长存储：

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用 PowerShell 创建和管理 Azure Database for MySQL 中的读取副本](howto-read-replicas-powershell.md)。
