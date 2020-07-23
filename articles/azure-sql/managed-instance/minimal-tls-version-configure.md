---
title: 配置最小 TLS 版本-托管实例
description: 了解如何为托管实例配置最小 TLS 版本
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 7982cb80c253ec3966319528e39390920c653e38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974256"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>在 Azure SQL 托管实例中配置最小 TLS 版本
最小[传输层安全性（TLS）](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)版本设置允许客户控制其 Azure SQL 托管实例使用的 TLS 版本。

目前，我们支持 TLS 1.0、1.1 和1.2。 设置最小 TLS 版本可确保支持后续的更新 TLS 版本。 例如，选择一个大于1.1 的 TLS 版本。 表示仅接受 TLS 1.1 和1.2 的连接，并拒绝 TLS 1.0。 在测试以确认应用程序支持该版本后，我们建议将最小 TLS 版本设置为1.2，因为它包括对以前版本中的漏洞的修补程序，以及 Azure SQL 托管实例中支持的最高 TLS 版本。

对于具有依赖于较旧版本 TLS 的应用程序的客户，我们建议根据应用程序的要求设置最小 TLS 版本。 对于依赖于使用未加密连接进行连接的应用程序的客户，我们建议不要设置任何最低的 TLS 版本。 

有关详细信息，请参阅[SQL 数据库连接的 TLS 注意事项](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

设置最小 TLS 版本后，使用低于最小 TLS 版本服务器的 TLS 版本的客户端的登录尝试将失败，并出现以下错误：

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>通过 PowerShell 设置最小 TLS 版本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

下面的 PowerShell 脚本演示如何 `Get` 和 `Set` 实例级的**最小 TLS 版本**属性：

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>通过 Azure CLI 设置最小 TLS 版本

> [!IMPORTANT]
> 本部分中的所有脚本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>bash shell 中的 Azure CLI

以下 CLI 脚本演示了如何更改 bash shell 中的**最小 TLS 版本**设置：

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```
