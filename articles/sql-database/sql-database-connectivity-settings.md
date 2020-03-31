---
title: Azure SQL 数据库和数据仓库的连接设置
description: 本文档介绍 TLS 版本选择和代理与 Azure SQL 重定向设置
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366081"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 连接设置
> [!NOTE]
> 本文适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。

> [!IMPORTANT]
> 本文*不适用于* **Azure SQL 数据库托管实例**

本文介绍了在服务器级别控制与 Azure SQL 数据库的连接的设置。 这些设置适用于与服务器关联的**所有**SQL 数据库和 SQL 数据仓库数据库。

> [!NOTE]
> 应用这些设置后，它们**将立即生效**，如果客户端不符合每个设置的要求，则可能导致连接丢失。

可通过**防火墙和虚拟网络**边栏选项卡访问连接设置，如下图所示：

 ![连接设置的屏幕截图][1]


## <a name="deny-public-network-access"></a>拒绝公共网络访问
在 Azure 门户中，当 **"拒绝公共网络访问**"设置设置为 **"是**"时，只允许通过专用终结点进行连接。 当此设置设置为 **"否"** 时，客户端可以使用私有终结点或公共终结点进行连接。

设置 **"拒绝公用网络访问****为是**"后，使用公共终结点的客户端登录尝试将失败，出现以下错误：

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>通过 PowerShell 更改公共网络访问
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

以下 PowerShell 脚本显示了如何在`Get`逻辑`Set`服务器级别使用**公共网络访问**属性：

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>通过 CLI 更改公共网络访问
> [!IMPORTANT]
> 本节中的所有脚本都需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>bash shell 中的 Azure CLI
以下 CLI 脚本演示如何在 bash shell 中更改**公共网络访问**：

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>连接策略
[连接策略](sql-database-connectivity-architecture.md#connection-policy)确定客户端如何连接到 Azure SQL Server。 

## <a name="change-connection-policy-via-powershell"></a>通过 PowerShell 更改连接策略
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

以下 PowerShell 脚本演示如何使用 PowerShell 更改连接策略：

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>通过 Azure CLI 更改连接策略
> [!IMPORTANT]
> 本节中的所有脚本都需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>bash shell 中的 Azure CLI
以下 CLI 脚本演示如何在 bash shell 中更改连接策略： 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>从 Windows 命令提示符运行 Azure CLI
以下 CLI 脚本演示如何从 Windows 命令提示符（安装了 Azure CLI）更改连接策略。

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>后续步骤
- 有关连接在 Azure SQL 数据库中工作原理的概述，请参阅[Azure SQL 连接体系结构](sql-database-connectivity-architecture.md)
- 有关如何更改 Azure SQL 数据库服务器的 Azure SQL 数据库连接策略的信息，请参阅 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)。

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
