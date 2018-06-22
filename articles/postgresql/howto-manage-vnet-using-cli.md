---
title: 使用 Azure CLI 创建和管理 Azure Database for PostgreSQL VNet 服务终结点和规则 | Microsoft Docs
description: 本文介绍如何使用 Azure CLI 命令行创建和管理 Azure Database for PostgreSQL VNet 服务终结点和规则。
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7312000d1f22af3eb0091b46caac2c9607231513
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736620"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for PostgreSQL VNet 服务终结点
虚拟网络 (VNet) 服务终结点和规则将虚拟网络的专用地址空间扩展到你的 Azure Database for PostgreSQL 服务器。 使用便捷的 Azure 命令行接口 (CLI) 命令，可创建、更新、删除、列出和显示 VNet 服务终结点和规则，用于管理服务器。 有关 Azure Database for PostgreSQL VNet 服务终结点（包括限制）的概述，请参阅 [Azure Database for PostgreSQL Server VNet 服务终结点](concepts-data-access-and-security-vnet.md)。 对所有支持 Azure Database for PostgreSQL 的区域提供 VNet 服务终结点的公共预览版。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 安装 [Azure CLI 2.0](/cli/azure/install-azure-cli) 命令行实用工具或使用浏览器中的 Azure Cloud Shell。
- [Azure Database for PostgreSQL 服务器和数据库](quickstart-create-server-database-azure-cli.md)。

> [!NOTE]
> 对 VNet 服务终结点的支持仅适用于常规用途和内存优化的服务器。

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>配置 Azure Database for PostgreSQL Vnet 服务终结点
[az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) 命令用于配置虚拟网络。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果在本地运行 CLI，需要使用 [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) 命令登录帐户。 记下与订阅名称相对应的命令输出中的 **id** 属性。
```azurecli-interactive
az login
```
使用 `az extension add --name rdbms-vnet` 命令安装 Azure Database for PostgreSQL VNet 服务终结点的 CLI 扩展。 
```azurecli-interactive
az extension add --name rdbms-vnet
```

运行 `az extension list` 命令来验证是否安装了 CLI 扩展。
```azurecli-interactive
az extension list
```
命令输出将列出所有已安装的扩展。 Azure Database for PostgreSQL CLI 扩展是：

 { "extensionType": "whl", "name": "rdbms-vnet", "version": "10.0.0" }

> [!NOTE]
> 若要卸载 CLI 扩展，运行 `az extension remove -n rdbms-vnet` 命令。 

如果有多个订阅，请选择应计费的资源所在的相应订阅。 使用 [az account set](/cli/azure/account#az_account_set) 命令选择帐户下的特定订阅 ID。 用订阅的 **az login** 输出中的 **id** 属性代替订阅 id 占位符。

- 该帐户必须拥有创建虚拟网络和服务终结点的必要权限。

对虚拟网络拥有写入访问权限的用户可在虚拟网络上单独配置服务终结点。

若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网拥有“Microsoft.Network/JoinServicetoaSubnet”权限。 此权限默认包含在内置的服务管理员角色中，可以通过创建自定义角色进行修改。

详细了解[内置角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)以及将特定的权限分配到[自定义角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)。

VNet 和 Azure 服务资源可以位于相同或不同的订阅中。 如果 VNet 和 Azure 服务资源位于不同的订阅中，在预览期，资源应在相同的 Active Directory (AD) 租户下。

> [!IMPORTANT]
> 强烈建议在运行下面的示例脚本或配置服务终结点前先阅读本文有关服务终结点配置和注意事项的内容。 虚拟网络服务终结点：[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 VNet 服务终结点使用 Microsoft.Sql 的服务类型名称，即名为“SQL 数据库”的 Azure 服务。 此服务标记也适用于 Azure SQL 数据库、Azure Database for PostgreSQL 和 MySQL 服务。 务必要注意的一点是，将 Microsoft.Sql 服务标记应用到 VNet 服务终结点时，它将为所有 Azure 数据库服务配置服务终结点流量（其中包括 Azure SQL 数据库、Azure Database for PostgreSQL 和子集上的 Azure Database for MySQL 服务器）。 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>示例脚本演示了如何创建 Azure Database for PostgreSQL 数据库，创建 VNet 和 VNet 服务终结点，以及如何使用 VNet 规则在子网中保护服务器
在此示例脚本中，更改突出显示的行，以自定义管理员用户名和密码。 将 `az account set --subscription` 命令中使用的 SubscriptionID 替换为你自己的订阅标识符。
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
