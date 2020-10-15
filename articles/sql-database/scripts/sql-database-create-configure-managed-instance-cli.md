---
title: Azure CLI：创建托管实例
description: 用于在 Azure SQL 托管实例中创建托管实例的 Azure CLI 示例脚本
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497267"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>使用 CLI 创建 Azure SQL 托管实例

此 Azure CLI 脚本示例在新虚拟网络的专用子网中创建 Azure SQL 托管实例。 它还为虚拟网络配置路由表和网络安全组。 成功运行脚本后，即可从虚拟网络或本地环境访问托管实例。 请参阅 [将 Azure VM 配置为连接到 Azure SQL 托管实例]../../azure-sql/managed-instance/connect-vm-instance-configure.md) 和[配置从本地到 Azure SQL 托管实例的点到站点连接](../../azure-sql/managed-instance/point-to-site-p2s-configure.md)。

> [!IMPORTANT]
> 有关限制，请参阅[支持的区域](../../azure-sql/managed-instance/resource-limits.md#supported-regions)和[支持的订阅类型](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types)。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

### <a name="sign-in-to-azure"></a>登录 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>运行脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>示例参考

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 虚拟网络命令。 |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | 虚拟网络子网命令。 |
| [az network route-table](/cli/azure/network/route-table) | 网络路由表命令。 |
| [az sql mi](/cli/azure/sql/mi) | SQL 托管实例命令。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到。
