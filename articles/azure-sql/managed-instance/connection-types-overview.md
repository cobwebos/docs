---
title: 连接类型
titleSuffix: Azure SQL Managed Instance
description: 了解 Azure SQL 托管实例连接类型
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 6c6774fb462a21e721b19ae53d1d018d780b28ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85517314"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Azure SQL 托管实例连接类型
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍了客户端如何根据连接类型连接到 Azure SQL 托管实例。 下面提供了更改连接类型的脚本示例，以及与更改默认连接设置相关的注意事项。

## <a name="connection-types"></a>连接类型

Azure SQL 托管实例支持以下两种连接类型：

- **重定向（建议）：** 客户端直接与托管数据库的节点建立连接。 若要启用使用重定向的连接，必须启用防火墙和网络安全组 (NSG)，以允许在端口 1433 和 11000-11999 上进行访问。 数据包直接发送到数据库，因此，通过代理进行重定向，可以提高延迟和吞吐量性能。
- 代理（默认值）：在此模式下，所有连接都使用代理网关组件。 若要启用连接，需要打开专用网络的端口1433和公用连接的端口3342。 选择此模式可能导致延迟增大、吞吐量降低，具体取决于工作负荷的性质。 我们强烈建议通过代理连接策略重定向连接策略，以实现最低的延迟和最高的吞吐量。

## <a name="redirect-connection-type"></a>“重定向”连接类型

在重定向连接类型中，在为 SQL 引擎建立 TCP 会话之后，客户端会话将从负载均衡器获取虚拟群集节点的目标虚拟 IP。 后续数据包将绕过网关，直接传入虚拟群集节点。 下图演示了此流量流。

![redirect.png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> 重定向连接类型当前仅适用于专用终结点。 不管连接类型设置如何，通过公共端点的连接都将通过代理。

## <a name="proxy-connection-type"></a>“代理”连接类型

在代理连接类型中，使用网关建立 TCP 会话，并且所有后续数据包都流过它。 下图演示了此流量流。

![proxy.png](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>更改连接类型

- **使用门户：** 若要使用 Azure 门户更改连接类型，请打开 "虚拟网络" 页，并使用 "**连接类型**" 设置更改连接类型并保存更改。

- **使用 PowerShell 更改连接类型设置的脚本：**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

下面的 PowerShell 脚本演示如何将托管实例的连接类型更改为 `Redirect` 。

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>后续步骤

- [将数据库还原到 SQL 托管实例](restore-sample-database-quickstart.md)
- 了解如何[在 SQL 托管实例上配置公共终结点](public-endpoint-configure.md)
- 了解[SQL 托管实例连接体系结构](connectivity-architecture-overview.md)
