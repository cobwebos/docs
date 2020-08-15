---
title: " (DNN 配置分布式网络名称) "
description: 了解如何配置分布式网络名称 (DNN) ，以将流量路由到 Azure VM 故障转移群集实例上的 SQL Server (FCI) 。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8eb9caf466148e43266c4be9cf1308da15fb67f2
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245530"
---
# <a name="configure-a-distributed-network-name-for-an-fci"></a>配置 FCI 的分布式网络名称 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 虚拟机上，分布式网络名称 (DNN) 用于将流量路由到相应的群集资源。 它提供一种更简单的方法来连接到 SQL Server 故障转移群集实例 (FCI) ，而不是虚拟网络名称 (VNN) ，无需使用 Azure 负载均衡器。 此功能目前以预览版提供，仅适用于 SQL Server 2019 CU2 和更高版本以及 Windows Server 2016 及更高版本。 

本文介绍如何配置 DNN，以使用 Azure Vm 上的 SQL Server 将流量路由到 Fci，以实现高可用性和灾难恢复 (HADR) 。 

## <a name="prerequisites"></a>先决条件

在完成本文中的步骤之前，应已做好以下准备：

- 确定分布式网络名称是适用 [于 HADR 解决方案的连接选项](hadr-cluster-best-practices.md#connectivity)。
- 已配置 [故障转移群集实例](failover-cluster-instance-overview.md)。 
- 已安装最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 

## <a name="create-dnn-resource"></a>创建 DNN 资源 

DNN 资源是在与 SQL Server FCI 相同的群集组中创建的。 使用 PowerShell 在 FCI 群集组中创建 DNN 资源。 

以下 PowerShell 命令将 DNN 资源添加到资源名称为的 SQL Server FCI 群集组 `<dnnResourceName>` 。 资源名称用于唯一标识资源。 使用对您有意义的，并且在整个群集中是唯一的。 资源类型必须为 `Distributed Network Name` 。 

`-Group`该值必须是对应于要添加分布式网络名称的 SQL SERVER FCI 的群集组的名称。 对于默认实例，典型的格式为 `SQL Server (MSSQLSERVER)` 。 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

例如，若要为 `dnn-demo` 默认 SQL SERVER FCI 创建 DNN 资源，请使用以下 PowerShell 命令：

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>设置群集 DNN DNS 名称

在群集中设置 DNN 资源的 DNS 名称。 然后，群集使用此值将流量路由到当前承载 SQL Server FCI 的节点。 
 
客户端使用 DNS 名称连接到 SQL Server FCI。 您可以选择一个唯一值。 或者，如果你已有现有的 FCI 并且不想更新客户端连接字符串，则可以将 DNN 配置为使用客户端已在使用的当前 VNN。 为此，需要先 [重命名 VNN](#rename-the-vnn) ，然后才能在 DNS 中设置 DNN。


使用此命令设置 DNN 的 DNS 名称： 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName`值是客户端用来连接到 SQL SERVER FCI 的客户端。 例如，对于要连接到的客户端 `FCIDNN` ，请使用以下 PowerShell 命令：

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

客户端将 `FCIDNN` 在连接到 SQL SERVER FCI 时输入其连接字符串。 

   > [!WARNING]
   > 请勿删除当前虚拟网络名称 (VNN) ，因为它是 FCI 基础结构的必需组件。 


### <a name="rename-the-vnn"></a>重命名 VNN 

如果你有现有的虚拟网络名称，并且希望客户端继续使用该值连接到 SQL Server FCI，则必须将当前 VNN 重命名为占位符值。 重命名当前的 VNN 后，可以将 DNN 的 DNS 名称值设置为 VNN。 

某些限制适用于重命名 VNN。 有关详细信息，请参阅 [重命名 FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)。

如果不需要使用当前 VNN，请跳过此部分。 重命名 VNN 后，请 [设置群集 DNN DNS 名称](#set-cluster-dnn-dns-name)。 

   
## <a name="set-dnn-resource-online"></a>设置 DNN 资源联机

正确命名 DNN 资源后，在群集中设置 DNS 名称值后，使用 PowerShell 在群集中设置 DNN 资源 online： 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

例如，若要启动 DNN 资源 `dnn-demo` ，请使用以下 PowerShell 命令： 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>配置可能的所有者

默认情况下，该群集会将 DNN DNS 名称绑定到群集中的所有节点。 但是，群集中不属于 SQL Server FCI 的节点应从 DNN 可能的所有者列表中排除。 

若要更新可能的所有者，请按照下列步骤操作：

1. 在故障转移群集管理器中转到 DNN 资源。 
1. 右键单击 "DNN" 资源，然后选择 " **属性**"。 
   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="DNN 资源的快捷菜单，其中突出显示了 "属性" 命令。":::
1. 清除不参与故障转移群集实例的任何节点的复选框。 DNN 资源的可能所有者列表应与 SQL Server 实例资源的可能所有者列表匹配。 例如，假设 Data3 不参与 FCI，下图是从 DNN 资源的可能所有者列表中删除 Data3 的示例： 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="对于 DNN 资源的可能所有者，清除不参与 FCI 的节点旁边的复选框":::

1. 选择“确定”保存设置****。 


## <a name="restart-sql-server-instance"></a>重新启动 SQL Server 实例 

使用故障转移群集管理器重启 SQL Server 实例。 执行以下步骤：

1. 在故障转移群集管理器中转到 SQL Server 资源。
1. 右键单击 SQL Server 资源，并使其脱机。 
1. 所有关联资源脱机后，右键单击 SQL Server 资源并再次使其联机。 

## <a name="update-connection-string"></a>更新连接字符串

若要确保在故障转移时快速连接，请在 `MultiSubnetFailover=True` SQL 客户端版本早于4.6.1 时添加到连接字符串。 

此外，如果 DNN 未使用原始 VNN，则连接到 SQL Server FCI 的 SQL 客户端需要将其连接字符串更新为 DNN DNS 名称。 若要避免这种要求，你可以将 DNS 名称值更新为 VNN 的名称。 但首先需要将 [现有的 VNN 替换为占位符](#rename-the-vnn) 。 

## <a name="test-failover"></a>测试故障转移

测试群集资源的故障转移以验证群集功能。 

若要测试故障转移，请执行以下步骤： 

1. 使用 RDP 连接到 SQL Server 群集节点之一。
1. 打开“故障转移群集管理器”。 选择“角色”。 观察哪个节点拥有 SQL Server FCI 角色。
1. 右键单击“SQL Server FCI”角色。 
1. 选择“移动”，再选择“最佳节点” 。

“故障转移群集管理器”显示该角色，其资源脱机。 然后资源会移动，并在另一个节点上恢复联机。

## <a name="test-connectivity"></a>测试连接

若要测试连接，请登录到同一虚拟网络中的另一个虚拟机。 打开 **SQL Server Management Studio** ，并使用 DNN DNS 名称连接到 SQL Server FCI。

如果需要，可以[下载 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。

## <a name="avoid-ip-conflict"></a>避免 IP 冲突

这是一个可选步骤，用于阻止将 FCI 资源使用的虚拟 IP (VIP) 地址分配给 Azure 中的其他资源。 

尽管客户现在使用 DNN 连接到 SQL Server FCI，但无法删除虚拟网络名称 (VNN) 和虚拟 IP，因为它们是 FCI 基础结构的必要组件。 但是，由于不再有负载均衡器在 Azure 中保留虚拟 IP 地址，因此虚拟网络上的另一资源会被分配与 FCI 使用的虚拟 IP 地址相同的 IP 地址。 这可能会导致重复的 IP 冲突问题。 

配置 APIPA 地址或专用网络适配器以保留 IP 地址。 

### <a name="apipa-address"></a>APIPA 地址

若要避免使用重复的 IP 地址，请将 APIPA 地址配置 (也称为链路本地地址) 。 为此，请运行以下命令：

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

在此命令中，"虚拟 IP 地址" 是群集 VIP 地址资源的名称，"169.254.1.1 等" 是为 VIP 地址选择的 APIPA 地址。 选择最适合你的业务的地址。 设置 `OverrideAddressMatch=1` 为允许 IP 地址在任何网络上，包括 APIPA 地址空间。 

### <a name="dedicated-network-adapter"></a>专用网络适配器

或者，将 Azure 中的网络适配器配置为保留虚拟 IP 地址资源使用的 IP 地址。 但是，这会在子网地址空间中使用地址，而且还会产生额外的开销，确保网络适配器不用于任何其他目的。

## <a name="limitations"></a>限制

- 目前，DNN 仅在 Windows Server 2016 上的 SQL Server 2019 CU2 和更高版本中受支持。 
- 目前，DNN 仅支持 Azure Vm 上 SQL Server 的故障转移群集实例。 将虚拟网络名称用于可用性组侦听器的 Azure 负载均衡器。
- 使用其他 SQL Server 功能时，可能会有更多的注意事项，并且 FCI DNN。 有关详细信息，请参阅 [FCI WITH DNN 互操作性](failover-cluster-instance-dnn-interoperability.md)。 

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 中 SQL Server HADR 功能的详细信息，请参阅 [可用性组](availability-group-overview.md) 和 [故障转移群集实例](failover-cluster-instance-overview.md)。 你还可以了解配置环境以实现高可用性和灾难恢复的 [最佳实践](hadr-cluster-best-practices.md) 。 

与 DNN 和 FCI 一起使用时，某些特定 SQL Server 功能可能还有其他配置要求。 有关详细信息，请参阅 [FCI WITH DNN 互操作性](failover-cluster-instance-dnn-interoperability.md) 。 

