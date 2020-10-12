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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f1c4fe8268d24026609f55d76a102a5c9a4e8295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356292"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Azure SQL 托管实例连接类型
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍客户端如何根据连接类型连接到 Azure SQL 托管实例。 下面提供了更改连接类型的脚本示例，以及有关更改默认连接设置的注意事项。

## <a name="connection-types"></a>连接类型

Azure SQL 托管实例支持以下两种连接类型：

- **重定向（建议）：** 客户端直接与托管数据库的节点建立连接。 若要启用使用重定向的连接，必须打开防火墙和网络安全组以允许访问端口 1433 和 11000-11999。 数据包将直接发送到数据库，因此使用“通过代理重定向”可以改善延迟和吞吐量性能。
- **代理（默认值）：** 在此模式下，所有连接都使用代理网关组件。 若要启用连接，只需打开专用网络的端口 1433 和公共连接的端口 3342。 选择此模式可能导致延迟增大、吞吐量降低，具体取决于工作负荷的性质。 我们强烈建议使用“重定向”连接策略而不要使用“代理”连接策略，以最大程度地降低延迟和提高吞吐量。

## <a name="redirect-connection-type"></a>重定向连接类型

重定向连接类型指与 SQL 引擎建立 TCP 会话后，客户端会话从负载均衡器获取虚拟群集节点的目标虚拟 IP。 后续数据包会绕过网关直接流向虚拟群集节点。 下图演示了此流量流。

![关系图显示了一个本地网络，其中包含用于连接到 Azure 虚拟网络中的网关的重定向-db，以及一个连接到虚拟网络中的数据库主节点的重定向查询。](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> 重定向连接类型当前仅适用于专用终结点。 无论连接类型设置如何，通过公共终结点进行的连接都将通过代理。

## <a name="proxy-connection-type"></a>代理连接类型

代理连接类型指通过网关建立 TCP 会话，并且所有后续数据包通过网关传输。 下图演示了此流量流。

![关系图显示了一个本地网络，其中的代理连接到 Azure 虚拟网络中的网关，连接到虚拟网络中的数据库主节点旁。](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>更改连接类型

- **使用门户：** 若要使用 Azure 门户更改连接类型，请打开“虚拟网络”页，使用“连接类型”设置更改连接类型并保存更改。

- **使用 PowerShell 编写更改连接类型设置的脚本：**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

以下 PowerShell 脚本显示了如何将托管实例的连接类型更改为 `Redirect`。

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
- 了解 [SQL 托管实例连接体系结构](connectivity-architecture-overview.md)
