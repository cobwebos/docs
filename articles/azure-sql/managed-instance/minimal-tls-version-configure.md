---
title: 配置最低 TLS 版本 - 托管实例
description: 了解如何为托管实例配置最低 TLS 版本
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 2dbd4b9af3db122703a7f2b4e0140ec3305f1c3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620046"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>在 Azure SQL 托管实例中配置最低 TLS 版本
借助最低[传输层安全性 (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 版本设置，客户可以控制其 Azure SQL 托管实例使用的 TLS 版本。

目前，我们支持 TLS 1.0、1.1 和 1.2。 设置最低 TLS 版本可确保支持后续更新的 TLS 版本。 例如，选择高于 1.1 的 TLS 版本。 这表示仅接受 TLS 1.1 和 1.2 的连接，并拒绝 TLS 1.0 的连接。 在测试并确认应用程序支持它后，我们建议将最低 TLS 版本设置为 1.2，因为它包括针对之前版本中发现的漏洞的修补程序，并且是 Azure SQL 托管实例中受支持的最高 TLS 版本。

对于使用依赖于较旧版本 TLS 的应用程序的客户，我们建议根据应用程序的要求设置最低 TLS 版本。 对于依赖于使用未加密连接进行连接的应用程序的客户，我们建议不要设置任何最低 TLS 版本。 

有关详细信息，请参阅 [SQL 数据库连接的 TLS 注意事项](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

设置最低 TLS 版本后，如果客户端使用的服务器的 TLS 版本低于最低 TLS 版本，则其登录尝试将失败，并显示以下错误：

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>通过 PowerShell 设置最低 TLS 版本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 数据库仍然支持 PowerShell Azure 资源管理器模块，但所有后续开发都针对 Az.Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中命令的参数大体相同。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

以下 PowerShell 脚本演示如何在实例级别 `Get` 和 `Set`“最低 TLS 版本”属性：

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>通过 Azure CLI 设置最低 TLS 版本

> [!IMPORTANT]
> 本部分中的所有脚本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

以下 CLI 脚本演示如何更改 bash shell 中的“最低 TLS 版本”设置：

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```
