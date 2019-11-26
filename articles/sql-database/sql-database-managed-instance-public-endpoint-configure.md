---
title: 配置公共终结点托管实例
description: 了解如何配置托管实例的公共终结点
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227984"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例中配置公共终结点

使用[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)的公共终结点可以从[虚拟网络](../virtual-network/virtual-networks-overview.md)外部对托管实例进行数据访问。 可以从多租户 Azure 服务（如 Power BI、Azure App Service 或本地网络）访问托管实例。 如果使用托管实例上的公共终结点，则无需使用 VPN，这有助于避免 VPN 吞吐量问题。

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> - 在 Azure 门户中为托管实例启用公共终结点
> - 使用 PowerShell 为托管实例启用公共终结点
> - 配置托管实例网络安全组，以允许将流量传送到托管实例公共终结点
> - 获取托管实例公共终结点的连接字符串

## <a name="permissions"></a>权限

由于托管实例中数据的敏感性，需要执行两个步骤才能完成启用托管实例公共终结点的配置。 这种安全措施遵守职责分离 (SoD) 的原则：

- 在托管实例上启用公共终结点需要由托管实例管理员完成。托管实例管理员可以在 SQL 托管实例资源的 "**概述**" 页中找到。
- 使用需要由网络管理员完成的网络安全组来允许流量。有关详细信息，请参阅[网络安全组权限](../virtual-network/manage-network-security-group.md#permissions)。

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>在 Azure 门户中为托管实例启用公共终结点

1. 启动 Azure 门户 (<https://portal.azure.com/.>)
1. 打开包含托管实例的资源组，然后选择要在其上配置公共终结点的 **SQL 托管实例**。
1. 在“安全性”设置中，选择“虚拟网络”选项卡。
1. 在虚拟网络配置页中选择“启用”，然后选择“保存”图标以更新配置。

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>使用 PowerShell 为托管实例启用公共终结点

### <a name="enable-public-endpoint"></a>启用公共终结点

运行以下 PowerShell 命令。 将 **subscription-id** 替换为你的订阅 ID。 将 **rg-name** 替换为托管实例的资源组，将 **mi-name** 替换为托管实例的名称。

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>禁用公共终结点

若要使用 PowerShell 禁用公共终结点，请执行以下命令（另外，如果为入站端口 3342 配置了 NSG，请记得关闭该 NSG）：

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>在网络安全组上允许公共终结点流量

1. 如果您的托管实例的 "配置" 页仍处于打开状态，请导航到 "**概述**" 选项卡。否则，请返回到**SQL 托管实例**资源。 选择“虚拟网络/子网”链接，转到虚拟网络配置页。

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. 在虚拟网络的左侧配置窗格中选择“子网”选项卡，并记下托管实例的**安全组**。

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. 返回包含你的托管实例的资源组。 应会看到上面记下的**网络安全组**名称。 请选择该名称转到网络安全组配置页。

1. 选择“入站安全规则”选项卡，并**添加**一个优先级高于 **deny_all_inbound** 规则且采用以下设置的规则： </br> </br>

    |设置  |建议的值  |说明  |
    |---------|---------|---------|
    |**源**     |任何 IP 地址或服务标记         |<ul><li>对于 Power BI 等 Azure 服务，请选择“Azure 云服务标记”</li> <li>对于你的计算机或 Azure VM，请使用 NAT IP 地址</li></ul> |
    |**源端口范围**     |*         |请将此字段保留为 *（任何），因为源端口通常是动态分配的，因而也是不可预测的 |
    |**目标**     |任意         |将目标保留为“任何”，以允许流量进入托管实例子网 |
    |**目标端口范围**     |3342         |将目标端口的范围限定为 3342，这是托管实例的公共 TDS 终结点 |
    |**协议**     |TCP         |托管实例对 TDS 使用 TCP 协议 |
    |**操作**     |Allow         |允许入站流量通过公共终结点传送到托管实例 |
    |**Priority**     |1300         |请确保此规则的优先级高于 **deny_all_inbound** 规则 |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > 端口 3342 用来与托管实例建立公共终结点连接，暂时不可更改。

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>获取托管实例公共终结点的连接字符串

1. 导航到为公共终结点启用的 SQL 托管实例配置页。 选择“设置”配置下的“连接字符串”选项卡。
1. 请注意，公用终结点主机名的格式 < mi_name >。**public**. < dns_zone >，并且用于连接的端口为3342。

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[在公共终结点中安全使用 Azure SQL 数据库托管实例](sql-database-managed-instance-public-endpoint-securely.md)。