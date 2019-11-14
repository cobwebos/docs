---
title: 配置可用性组（Azure CLI）
description: 使用 Azure CLI 在 Azure 中的 SQL Server VM 上创建 Windows 故障转移群集、可用性组侦听器和内部负载均衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022364"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure CLI 为 Azure VM 上的 SQL Server 配置 Always On 可用性组
本文介绍如何使用[Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/)部署 Windows 故障转移群集、将 SQL Server vm 添加到群集，并为 Always On 可用性组创建内部负载均衡器和侦听器。 Always On 可用性组的部署仍通过 SQL Server Management Studio （SSMS）手动完成。 

## <a name="prerequisites"></a>先决条件
若要通过使用 Azure CLI 自动安装 Always On 可用性组，必须满足以下先决条件： 
- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- Azure 中一个或多个已加入域的[vm 正在运行 SQL Server 2016 （或更高版本） Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) ，在*同一可用性集或*已[注册到 SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)的不同可用性区域中。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 两个可用（不是由任何实体使用） IP 地址。 一个用于内部负载均衡器。 另一种用于与可用性组位于同一子网中的可用性组侦听器。 如果使用现有的负载均衡器，则可用性组侦听器只需要一个可用的 IP 地址。 

## <a name="permissions"></a>权限
你需要以下帐户权限才能使用 Azure CLI 配置 Always On 可用性组： 

- 在域中具有 "**创建计算机对象**" 权限的现有域用户帐户。 例如，域管理员帐户通常拥有足够的权限（例如： account@domain.com）。 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 控制 SQL Server 服务的域用户帐户。 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>步骤1：创建存储帐户作为云见证服务器
群集需要使用存储帐户作为云见证。 你可以使用任何现有的存储帐户，也可以创建新的存储帐户。 如果要使用现有存储帐户，请跳到下一部分。 

以下代码片段将创建存储帐户： 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 如果使用的是过期版本的 Azure CLI，可能会看到错误 `az sql: 'vm' is not in the 'az sql' command group`。 下载[最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)以获取此错误。

## <a name="step-2-define-windows-failover-cluster-metadata"></a>步骤2：定义 Windows 故障转移群集元数据
Azure CLI [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest)命令组管理托管可用性组的 Windows Server 故障转移群集（WSFC）服务的元数据。 群集元数据包括要用作云见证的 Active Directory 域、群集帐户、存储帐户和 SQL Server 版本。 使用[az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create)定义 WSFC 的元数据，以便在添加第一个 SQL Server VM 时，按定义创建群集。 

下面的代码段定义群集的元数据：
```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>步骤3：将 SQL Server Vm 添加到群集
向群集添加第一个 SQL Server VM 会创建群集。 [Az sql vm 的 "添加到组](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group)" 命令将创建名为先前给定的群集，在 SQL Server vm 上安装群集角色，并将其添加到群集。 以后使用 `az sql vm add-to-group` 命令将更多的 SQL Server Vm 添加到新创建的群集。 

以下代码片段创建群集，并将第一个 SQL Server VM 添加到其中： 

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
使用此命令可将任何其他 SQL Server Vm 添加到群集。 仅修改 SQL Server VM 名称的 `-n` 参数。 

## <a name="step-4-create-the-availability-group"></a>步骤4：创建可用性组
按常规方式手动创建可用性组，方法是使用[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、 [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)或[transact-sql](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)。 

>[!IMPORTANT]
> 此时*不要创建侦听器*，因为这是通过以下部分中的 Azure CLI 完成的。  

## <a name="step-5-create-the-internal-load-balancer"></a>步骤5：创建内部负载均衡器

Always On 可用性组侦听器需要 Azure 负载均衡器的内部实例。 内部负载均衡器为可用性组侦听器提供 "浮动" IP 地址，以便进行更快速的故障转移和重新连接。 如果可用性组中的 SQL Server Vm 是同一可用性集的一部分，则可以使用基本的负载均衡器。 否则，你需要使用标准负载均衡器。  

> [!NOTE]
> 内部负载均衡器应与 SQL Server VM 实例位于同一虚拟网络中。 

以下代码片段创建内部负载均衡器：

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 每个 SQL Server VM 的公共 IP 资源应具有标准 SKU，才能与标准负载均衡器兼容。 若要确定 VM 的公共 IP 资源的 SKU，请参阅 "**资源组**"，选择所需 SQL Server VM 的**公共 IP 地址**资源，然后在 "**概述**" 窗格中的 " **SKU** " 下找到该值。  

## <a name="step-6-create-the-availability-group-listener"></a>步骤6：创建可用性组侦听器
手动创建可用性组后，可以使用[az sql vm ag-侦听器](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)创建侦听器。 

*子网资源 id*是附加到虚拟网络资源的资源 id `/subnets/<subnetname>` 的值。 标识子网资源 ID：
   1. 在[Azure 门户](https://portal.azure.com)中转到资源组。 
   1. 选择虚拟网络资源。 
   1. 在 "**设置**" 窗格中选择 "**属性**"。 
   1. 确定虚拟网络的资源 ID 并将 `/subnets/<subnetname>` 追加到该虚拟网络的末尾，以创建子网资源 ID。 例如：
      - 虚拟网络资源 ID 为： `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 子网名称为： `default`
      - 因此，子网资源 ID 为： `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


以下代码片段将创建可用性组侦听器：

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>修改可用性组中的副本数
将可用性组部署到 Azure 中托管的 SQL Server Vm 时，增加了复杂性。 资源提供程序和虚拟机组现在管理资源。 因此，在可用性组中添加或删除副本时，有一个额外的步骤使用 SQL Server Vm 的相关信息更新侦听器元数据。 在修改可用性组中的副本数时，还必须使用[az sql vm group ag-侦听器 update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update)命令来使用 SQL Server vm 的元数据来更新侦听器。 


### <a name="add-a-replica"></a>添加副本

将新副本添加到可用性组：

1. 将 SQL Server VM 添加到群集：
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. 使用 SQL Server Management Studio 将 SQL Server 实例添加为可用性组中的副本。
1. 将 SQL Server VM 元数据添加到侦听器：
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>删除副本

从可用性组中删除副本：

1. 使用 SQL Server Management Studio 从可用性组中删除副本。 
1. 从侦听器中删除 SQL Server VM 元数据：
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 从群集中删除 SQL Server VM：
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>删除可用性组侦听器
如果以后需要删除使用 Azure CLI 配置的可用性组侦听器，则必须通过 SQL VM 资源提供程序。 由于侦听器是通过 SQL VM 资源提供程序注册的，因此只需通过 SQL Server Management Studio 来删除它就会不足。 

最佳方法是使用 Azure CLI 中的以下代码片段，通过 SQL VM 资源提供程序删除它。 这样做将从 SQL VM 资源提供程序中删除可用性组侦听器元数据。 它还会从可用性组中实际删除侦听器。 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [SQL Server Vm 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Vm 的常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server Vm 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切换 SQL Server VM 的许可模型](virtual-machines-windows-sql-ahb.md)
* [Always On 可用性组&#40;SQL Server 概述&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Always On 可用性组&#40;SQL Server 的服务器实例的配置&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [管理可用性组&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [监视可用性组&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Always On 可用性组&#40;SQL Server 的 transact-sql 语句概述&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [适用于 Always On 可用性组&#40;SQL Server 的 PowerShell cmdlet 概述&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
