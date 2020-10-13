---
title: Azure CLI：将托管实例添加到故障转移组
description: 了解如何创建两个托管实例、将它们添加到故障转移组，然后测试故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: afefc556bcac096d67051f3014c31e449dbbca32
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323560"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>使用 CLI 在故障转移组创建 Azure SQL 托管实例

此 Azure CLI 示例创建两个托管实例，将其添加到故障转移组，然后测试从主托管实例到辅助托管实例的故障转移。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-scripts"></a>示例脚本

### <a name="sign-in-to-azure"></a>登录 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>运行脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。 需要删除资源组两次。 第一次删除资源组将删除托管实例和虚拟群集，但随后会失败，并显示错误消息 `az group delete : Long running operation failed with status 'Conflict'.`。 再次运行 az group delete 命令，删除所有残留资源以及资源组。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>示例参考

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 虚拟网络命令。  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | 虚拟网络子网命令。 |
| [az network nsg](/cli/azure/network/nsg) | 网络安全组命令。 |
| [az network nsg rule](/cli/azure/network/nsg/rule)| 网络安全规则命令。 |
| [az network route-table](/cli/azure/network/route-table) | 路由表命令。 |
| [az sql mi](/cli/azure/sql/mi) | SQL 托管实例命令。 |
| [az network public-ip](/cli/azure/network/public-ip) | 网络公共 IP 地址命令。 |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | 虚拟网络网关命令 |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | SQL 托管实例故障转移组命令。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../../azure-sql/database/az-cli-script-samples-content-guide.md)中找到。
