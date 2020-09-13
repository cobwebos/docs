---
title: 适用于 Azure SQL 数据库和数据仓库的连接设置
description: 本文档介绍传输层安全性 (TLS) 版本选择和代理，以及适用于 Azure SQL 数据库和 Azure Synapse Analytics 的重定向设置
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/06/2020
ms.openlocfilehash: f664ffbfc9aa38dcf8eb7736b28613efb95bde63
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438171"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 连接设置
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文介绍的设置可用于控制与 Azure SQL 数据库和 Azure Synapse Analytics 的服务器的连接。 这些设置应用于与服务器关联的所有 SQL 数据库和 Azure Synapse 数据库。

> [!IMPORTANT]
> 本文不适用于 Azure SQL 托管实例

可从“防火墙和虚拟网络”屏幕访问连接设置，如以下屏幕截图所示：

 ![连接设置的屏幕截图][1]

> [!NOTE]
> 应用这些设置后，它们立即生效。如果客户端不满足每个设置的要求，这些设置可能会导致客户端断开连接。

## <a name="deny-public-network-access"></a>拒绝公用网络访问

当 " **拒绝公共网络访问** " 设置设置为 **"是"** 时，只允许通过专用终结点进行连接。 如果将此设置设置为 " **无** (默认值) ，则客户端可以使用 (基于 IP 的防火墙规则、基于 VNET 的防火墙规则) 或专用终结点上的公共终结点进行连接， (使用" [网络访问概述](network-access-controls-overview.md)"中所述的私有链接) 。 

 ![具有拒绝公共网络访问权限的连接屏幕截图][2]

如果逻辑服务器上没有任何现有专用终结点，则任何将 " **拒绝公共网络访问** " 设置设置为 **"是"** 的尝试将失败，并出现如下错误消息：  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

当 " **拒绝公共网络访问** " 设置设置为 **"是"** 时，只允许通过专用终结点进行的连接，并且通过公共终结点的所有连接被拒绝，并出现类似于以下内容的错误消息：  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

当 " **拒绝公共网络访问** " 设置设置为 **"是"** 时，将拒绝任何添加或更新防火墙规则的尝试，并会出现如下错误消息：

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>通过 PowerShell 更改公用网络访问

> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

下面的 PowerShell 脚本展示了如何在服务器级别 `Get` 和 `Set`“公用网络访问”属性：

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled"
```

## <a name="change-public-network-access-via-cli"></a>通过 CLI 更改公用网络访问

> [!IMPORTANT]
> 本部分中的所有脚本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

以下 CLI 脚本演示如何更改 bash shell 中的“公用网络访问”：

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>最低 TLS 版本 

借助最低[传输层安全性 (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 版本设置，客户可以控制其 Azure SQL 数据库使用的 TLS 版本。

目前，我们支持 TLS 1.0、1.1 和 1.2。 设置最低 TLS 版本可确保支持后续更新的 TLS 版本。 例如，选择高于 1.1 的 TLS 版本。 这表示仅接受 TLS 1.1 和 1.2 的连接，并拒绝 TLS 1.0 的连接。 在测试并确认应用程序支持它后，我们建议将最低 TLS 版本设置为 1.2，因为它包括针对之前版本中发现的漏洞的修补程序，并且是 Azure SQL 数据库中受支持的最高 TLS 版本。

> [!IMPORTANT]
> 最低 TLS 版本的默认设置为允许使用所有版本。 但是，一旦强制执行某个 TLS 版本，就无法恢复为默认值。

对于使用依赖于较旧版本 TLS 的应用程序的客户，我们建议根据应用程序的要求设置最低 TLS 版本。 对于依赖于使用未加密连接进行连接的应用程序的客户，我们建议不要设置任何最低 TLS 版本。

有关详细信息，请参阅 [SQL 数据库连接的 TLS 注意事项](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

设置最低 TLS 版本后，如果客户端使用的服务器的 TLS 版本低于最低 TLS 版本，则其登录尝试将失败，并显示以下错误：

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>通过 PowerShell 设置最低 TLS 版本

> [!IMPORTANT]
> Azure SQL 数据库仍然支持 PowerShell Azure 资源管理器模块，但所有后续开发都针对 Az.Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

以下 PowerShell 脚本演示如何在逻辑服务器级别 `Get` 和 `Set`“最低 TLS 版本”属性：

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>通过 Azure CLI 设置最低 TLS 版本

> [!IMPORTANT]
> 本部分中的所有脚本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

以下 CLI 脚本演示如何更改 bash shell 中的“最低 TLS 版本”设置：

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>更改连接策略

[连接策略](connectivity-architecture.md#connection-policy)确定客户端连接到 Azure SQL 数据库的方式。


## <a name="change-connection-policy-via-powershell"></a>通过 PowerShell 更改连接策略

> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

以下 PowerShell 脚本展示了如何使用 PowerShell 更改连接策略：

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>通过 Azure CLI 更改连接策略

> [!IMPORTANT]
> 本部分中的所有脚本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

以下 CLI 脚本展示了如何在 bash shell 中更改连接策略：

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Windows 命令提示符中的 Azure CLI

以下 CLI 脚本演示如何从 Windows 命令提示符（已安装 Azure CLI）更改连接策略。

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>后续步骤

- 若要大致了解 Azure SQL 数据库中的连接工作原理，请参阅[连接体系结构](connectivity-architecture.md)
- 有关如何更改服务器的连接策略的信息，请参阅 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)。

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
