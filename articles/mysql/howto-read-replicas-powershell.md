---
title: 管理读取副本-Azure PowerShell-Azure Database for MySQL
description: 了解如何使用 PowerShell 在 Azure Database for MySQL 中设置和管理读取副本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/29/2020
ms.openlocfilehash: 9ac85299311c1fd233988c6472d6325934dd42dd
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614533"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>如何使用 PowerShell 在 Azure Database for MySQL 中创建和管理读取副本

本文介绍如何使用 PowerShell 在 Azure Database for MySQL 服务中创建和管理读取副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

## <a name="azure-powershell"></a>Azure PowerShell

可以使用 PowerShell 创建和管理读取副本。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az PowerShell 模块处于预览阶段，但你必须使用以下命令从 Az PowerShell 模块单独安装它： `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az PowerShell 模块公开发布后，它将成为未来 Az PowerShell 模块版本的一部分，并从 Azure Cloud Shell 中的本机提供。

如果选择在本地使用 PowerShell，请使用[AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 服务器。 请确保主服务器位于其中一个定价层中。

### <a name="create-a-read-replica"></a>创建只读副本

可以使用以下命令创建只读副本服务器：

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMySqlServerReplica` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  在其中创建副本服务器的资源组。  |
| 名称 | mydemoreplicaserver | 所创建的新副本服务器的名称。 |

若要创建跨区域读取副本，请使用**Location**参数。 以下示例在**美国西部**区域创建一个副本。

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。

默认情况下，将使用与主副本相同的服务器配置来创建读取副本，除非指定了**Sku**参数。

> [!NOTE]
> 建议副本服务器的配置应保持在与主服务器相同或更大的值，以确保副本能够跟上主服务器。

### <a name="list-replicas-for-a-master-server"></a>列出主服务器的副本

若要查看给定的主服务器的所有副本，请运行以下命令：

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMySqlReplica` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  要在其中创建副本服务器的资源组。  |
| ServerName | mydemoserver | 主服务器的名称或 ID。 |

### <a name="delete-a-replica-server"></a>删除副本服务器

可以通过运行`Remove-AzMySqlServer` cmdlet 来删除读取副本服务器。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>删除主服务器

> [!IMPORTANT]
> 删除主服务器会停止复制到所有副本服务器，并删除主服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要删除主服务器，你可以运行`Remove-AzMySqlServer` cmdlet。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 重新启动 Azure Database for MySQL 服务器](howto-restart-server-powershell.md)
