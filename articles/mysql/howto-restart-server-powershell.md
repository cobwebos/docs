---
title: 重新启动服务器-Azure PowerShell-Azure Database for MySQL
description: 本文介绍如何使用 PowerShell 重新启动 Azure Database for MySQL 服务器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 935459a398c07d3b4f61c76dec75b083a2354720
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608977"
---
# <a name="restart-azure-database-for-mysql-server-using-powershell"></a>使用 PowerShell 重新启动 Azure Database for MySQL 服务器

本主题介绍如何重启 Azure Database for MySQL 服务器。 出于维护原因，可能需要重新启动服务器，这会导致在操作过程中发生短暂的中断。

如果服务忙，则服务器重新启动被阻止。 例如，服务可以处理缩放 vCores 等先前请求的操作。

完成重新启动所需的时间取决于 MySQL 恢复过程。 为了减少重新启动时间，我们建议在重新启动之前最大程度地减少服务器上发生的活动量。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az PowerShell 模块处于预览阶段，但你必须使用以下命令从 Az PowerShell 模块单独安装它： `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az PowerShell 模块公开发布后，它将成为未来 Az PowerShell 模块版本的一部分，并从 Azure Cloud Shell 中的本机提供。

如果选择在本地使用 PowerShell，请使用[AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>重启服务器

使用以下命令重启服务器：

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)
