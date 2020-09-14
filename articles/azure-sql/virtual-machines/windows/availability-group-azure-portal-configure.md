---
title: '配置可用性组 (Azure 门户) '
description: 使用 Azure 门户在 Azure 中的 SQL Server VM 上创建 Windows 故障转移群集、可用性组侦听器和内部负载均衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4020f47184e141a69586fc958f641547d7bde94d
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482774"
---
# <a name="configure-an-availability-group-for-sql-server-on-azure-vm-azure-portal---preview"></a>在 Azure VM 上配置 SQL Server 的可用性组 (Azure 门户-预览) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何使用 [Azure 门户](https://portal.azure.com) 为 Azure vm 上的 SQL Server 配置可用性组。 

使用 Azure 门户创建新群集或加入现有群集，然后创建可用性组、侦听器和内部负载均衡器。 

   > [!NOTE]
   > 此功能目前处于预览状态且正在部署，因此，如果所需的区域不可用，请稍后再查看。 


## <a name="prerequisites"></a>先决条件

若要使用 Azure 门户配置 Always On 可用性组，必须满足以下先决条件： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- Azure 中一个或多个已加入域的 [vm 正在运行 SQL Server 2016 (或) 更高版本](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 在 *同一* 可用性集或 *不同* 的可用性区域中，这些虚拟 [机已在完全可管理性模式下注册到 SQL VM 资源提供程序](sql-vm-resource-provider-register.md) ，并在每个 VM 上为 SQL Server 服务使用同一域帐户。
- 两个可用的（未被任何实体使用的）IP 地址。 一个用于内部负载均衡器。 另一个用于与可用性组位于同一子网中的可用性组侦听器。 如果使用现有的负载均衡器，则只需一个可用性组侦听器的可用 IP 地址。 

## <a name="permissions"></a>权限

你需要以下帐户权限才能使用 Azure 门户配置可用性组： 

- 在域中具有“创建计算机对象”权限的现有域用户帐户。 例如，域管理员帐户通常拥有足够的权限（如 account@domain.com）。 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 可控制 SQL Server 的域用户帐户。 这应该是要添加到可用性组中的每个 SQL Server VM 的相同帐户。 

## <a name="configure-cluster"></a>配置群集

使用 Azure 门户配置群集。 你可以创建新群集，或者，如果你已有一个现有群集，则可以将其载入 SQL VM 资源提供程序以实现门户可管理性。


### <a name="create-a-new-cluster"></a>创建新群集

如果已有群集，请跳过此部分，转到 [内置的群集](#onboard-existing-cluster) 。 

如果还没有现有群集，请使用 Azure 门户执行以下步骤来创建它：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到 [SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 资源。 
1. 选择 "**设置**" 下的 "**高可用性**"。 
1. 选择 " **+ 新建 Windows Server 故障转移群集** "，打开 " **配置 Windows 故障转移群集** " 页面。  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="通过在门户中选择 + 新建 群集来创建新群集":::

1. 命名群集并提供要用作云见证的存储帐户。 使用现有存储帐户，或选择 " **新建** " 以创建新的存储帐户。 存储帐户名称长度必须为 3 到 24 个字符，并且只能使用数字和小写字母。

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="提供群集的名称、存储帐户和凭据":::

1. 展开 " **Windows Server 故障转移群集凭据** " 以提供 SQL Server 服务帐户的 [凭据](https://docs.microsoft.com/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) ，以及群集操作员和启动帐户（如果它们与用于 SQL Server 服务的帐户不同）。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="提供 SQL 服务帐户、群集操作员帐户和群集启动帐户的凭据":::

1. 选择要添加到群集的 SQL Server Vm。 请注意是否需要重新启动，并在一定的情况下继续操作。 只有在完全可管理性模式下注册到 SQL VM 资源提供程序且位于同一位置、域以及与主 SQL Server VM 位于同一虚拟网络上的 Vm 才会可见。 
1. 选择 " **应用** " 以创建群集。 可以在活动日志中检查部署的状态，可从顶部导航栏中的电铃图标访问该 **活动日志** 。 
1. 要使故障转移群集受 Microsoft 支持，它必须通过群集验证。 使用首选方法连接到 VM (例如远程桌面协议 (RDP) # A3，并验证群集是否通过验证，然后再继续。 否则，群集将处于不受支持状态。 你可以使用故障转移群集管理器 (FCM) 或以下 PowerShell 命令来验证群集：

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>加入现有群集

如果已在 SQL Server VM 环境中配置了群集，则可以将其从 Azure 门户上架起来。

为此，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到 [SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 资源。 
1. 选择 "**设置**" 下的 "**高可用性**"。 
1. 选择 "载入 **现有 Windows Server 故障转移群集** "，打开 " **板载 Windows Server 故障转移群集** " 页面。 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="从 SQL 虚拟机资源上的 高可用性 页中载入现有群集":::

1. 查看群集的设置。 
1. 选择 " **应用** " 以加入群集，然后在提示时选择 **"是"** 以继续。




## <a name="create-availability-group"></a>创建可用性组

创建或载入群集后，使用 Azure 门户创建可用性组。 为此，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到 [SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 资源。 
1. 选择 "**设置**" 下的 "**高可用性**"。 
1. 选择 " **+ 新建 Always On 可用性组** " 以打开 " **创建可用性组** " 页。

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="选择 新建 always on 可用性组 以打开 创建可用性组 页。":::

1. 输入可用性组的名称。 
1. 选择 " **配置侦听器** " 以打开 " **配置可用性组侦听器** " 页。 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="提供可用性组的名称并配置侦听器":::

1. 填写值，并使用现有的负载均衡器，或选择 " **新建** " 以创建新的负载均衡器。  选择 " **应用** " 以保存设置，并创建侦听器和负载均衡器。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="填写表单中的值以创建新的侦听器和负载均衡器":::

1. 选择 " **+ 选择副本** "，打开 " **配置可用性组副本** " 页。
1. 选择要添加到可用性组的虚拟机，并选择最适合你的业务需求的可用性组设置。 选择 " **应用** " 以保存设置。 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="选择要添加到可用性组的 Vm，并配置适用于你的业务的设置":::

1. 验证可用性组设置，然后选择 " **应用** " 以创建可用性组。 

可以在活动日志中检查部署的状态，可从顶部导航栏中的电铃图标访问该 **活动日志** 。 

  > [!NOTE]
  > 在将数据库添加到可用性组之前，Azure 门户的 "**高可用性**" 页上的**同步运行状况**将显示为 "**不正常**"。 


## <a name="add-database-to-availability-group"></a>将数据库添加到可用性组

部署完成后，将数据库添加到可用性组。 以下步骤使用 SQL Server Management Studio (SSMS) ，但也可以使用 [transact-sql 或 PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) 。 

若要使用 SQL Server Management Studio 将数据库添加到可用性组，请执行以下步骤：

1. 使用你的首选方法连接到你的 SQL Server Vm 之一，例如远程桌面连接 (RDP) 。 
1. 打开 SQL Server Management Studio (SSMS)。
1. 连接 SQL Server 实例。 
1. 展开**对象资源管理器** **Always On 高可用性**。
1. 展开 " **可用性组**"，右键单击可用性组，然后选择 " **添加数据库 ...**"。

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="在对象资源管理器中右键单击可用性组，然后选择 添加数据库":::

1. 按照提示选择要添加到可用性组的数据库 () 。 
1. 选择 **"确定"** 以保存设置并将数据库添加到可用性组。 
1. 添加数据库后，刷新 **对象资源管理器** 以确认数据库的状态 `synchronized` 。 

添加数据库后，可以在 Azure 门户中查看可用性组的状态： 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="在数据库同步后，从 Azure 门户中的 高可用性 页查看可用性组的状态":::

## <a name="add-more-vms"></a>添加更多 Vm

若要将更多 SQL Server Vm 添加到群集，请执行以下步骤： 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到 [SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 资源。 
1. 选择 "**设置**" 下的 "**高可用性**"。 
1. 选择 " **配置 Windows Server 故障转移群集** "，打开 " **配置 Windows Server 故障转移群集** " 页面。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="选择 配置 Windows Server 故障转移群集，将 Vm 添加到群集。":::

1. 展开 " **Windows Server 故障转移群集凭据** "，并输入用于 SQL Server 服务、群集操作员和群集启动帐户的帐户。 
1. 选择要添加到群集的 SQL Server Vm。 
1. 选择“应用”。  

可以在活动日志中检查部署的状态，可从顶部导航栏中的电铃图标访问该 **活动日志** 。 


## <a name="modify-availability-group"></a>修改可用性组 


您可以通过选择可用性组旁边的省略号 ( ... ) ，**将更多副本添加**到可用性组，**配置侦听器**，或从 Azure 门户中的 "**高可用性**" 页**删除侦听器**： 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="选择可用性组旁边的省略号，然后选择 添加副本，将更多副本添加到可用性组。":::

## <a name="remove-cluster"></a>删除群集

从群集中删除所有 SQL Server Vm 以销毁该群集，然后从 SQL VM 资源提供程序中删除该群集元数据。 为此，可以使用最新版本的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 或 PowerShell。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，从群集中删除所有 SQL Server Vm。 这会在物理上从群集中删除节点并销毁群集：  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

如果这些是群集中的唯一 Vm，则该群集将被销毁。 如果群集中的任何其他 Vm 除了删除的 SQL Server Vm，则不会删除其他 Vm，并且不会销毁群集。 

接下来，从 SQL VM 资源提供程序中删除群集元数据： 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，从群集中删除所有 SQL Server Vm。 这会在物理上从群集中删除节点并销毁群集： 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

如果这些是群集中的唯一 Vm，则该群集将被销毁。 如果群集中的任何其他 Vm 除了删除的 SQL Server Vm，则不会删除其他 Vm，并且不会销毁群集。 


接下来，从 SQL VM 资源提供程序中删除群集元数据： 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="troubleshooting"></a>疑难解答

如果遇到问题，可以检查部署历史记录，并查看常见错误及其解决方法。 

### <a name="check-deployment-history"></a>检查部署历史记录

通过门户对群集和可用性组进行的更改是通过部署来完成的。 如果创建、或加入群集或创建可用性组出现问题，部署历史记录可以提供更多详细信息。

若要查看部署的日志，并检查部署历史记录，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到资源组。
1. 选择“设置”下面的“部署”。********
1. 选择所需的部署以了解有关部署的详细信息。 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="选择你想要了解的有关的部署。" :::

### <a name="common-errors"></a>常见错误

查看以下常见错误及其解决方法。 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>用于启动 sql 服务的帐户不是域帐户

这表示资源提供程序无法使用提供的凭据访问 SQL Server 服务。 一些常见解决方法：
- 确保域控制器正在运行。
- 验证门户中提供的凭据与 SQL Server 服务的凭据匹配。 


## <a name="next-steps"></a>后续步骤


有关可用性组的详细信息，请参阅：

- [可用性组概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [管理可用性组](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [监视可用性组 (SQL Server)](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [可用性组 Transact-sql 语句 ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [可用性组 PowerShell 命令](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

有关 SQL Server Vm 的详细信息，请参阅： 

* [SQL Server VM 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 的发行说明](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM 的常见问题解答](frequently-asked-questions-faq.md)
