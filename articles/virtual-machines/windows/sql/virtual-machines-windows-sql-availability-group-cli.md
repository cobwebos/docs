---
title: 使用 Azure SQL VM CLI 为 Azure VM 上的 SQL Server 中配置 Always On 可用性组
description: '使用 Azure CLI 在 Azure 中的 SQL Server VM 上创建 Windows 故障转移群集和可用性组侦听器时，内部负载均衡器。 '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c5c5f4c8125f801edc89d47851871d8eb06a2f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762865"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure SQL VM CLI 为 Azure VM 上的 SQL Server 中配置 Always On 可用性组
本文介绍如何使用[Azure SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-latest/)来部署 Windows 故障转移群集 (WSFC) 和 SQL Server 虚拟机添加到群集，以及创建内部负载均衡器和 Always On 可用性组侦听程序。  Always On 可用性组的实际部署仍可手动通过 SQL Server Management Studio (SSMS)。 

## <a name="prerequisites"></a>必备组件
若要自动执行的 Always On 可用性组使用 Azure SQL VM CLI 安装程序，必须已具备以下先决条件： 
- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- 一个或多个已加入域的[Azure 正在运行 SQL Server 2016 （或更高版本） Enterprise edition 中的 Vm](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)中*同一个可用性集或多个不同的可用性区域*已[注册与 SQL 虚拟机资源提供程序](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 两个可用 （未由任何实体） IP 地址，一个内部负载均衡器，一个用于可用性组位于同一子网中的可用性组侦听器。 如果正在使用现有的负载均衡器，则只有一个可用的 IP 地址需要为可用性组侦听器。 

## <a name="permissions"></a>权限
以下帐户权限所需配置 Always On 可用性组使用 Azure SQL VM CLI。 

- 现有的域用户帐户的域中有权创建计算机对象。  例如，域管理员帐户通常有足够的权限（如 account@domain.com）。 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 域用户帐户控制 SQL Server 服务。 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>步骤 1-创建云见证的存储帐户
群集需要一个存储帐户来作为云见证服务器。 可以使用任何现有的存储帐户，也可以创建新的存储帐户。 如果你想要使用现有的存储帐户，跳到下一节。 

下面的代码段将创建存储帐户： 
```azurecli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > 可能会看到错误`az sql: 'vm' is not in the 'az sql' command group`如果使用 Azure CLI 版本已过期。 下载[最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)即可解决此错误。

## <a name="step-2---define-windows-failover-cluster-metadata"></a>步骤 2-定义 Windows 故障转移群集的元数据
Azure SQL VM CLI [az sql vm 组](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest)命令组管理的托管可用性组的 Windows 故障转移群集 (WSFC) 服务的元数据。 群集的元数据包括 AD 域、 群集帐户、 存储帐户以用作云见证服务器和 SQL Server 版本。 使用[az sql vm 组创建](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create)以 wsfc 中定义的元数据，以便添加第一个 SQL Server VM 时，创建群集时定义。 

下面的代码段定义群集的元数据：
```azurecli
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

## <a name="step-3---add-sql-server-vms-to-cluster"></a>步骤 3-将 SQL Server 虚拟机添加到群集
将第一个 SQL Server 虚拟机添加到群集创建群集。 [Az sql vm 添加到组](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group)命令创建包含以前提供的名称的群集、 SQL Server Vm 上安装群集角色并将它们添加到群集。 以后使用`az sql vm add-to-group`命令将更多 SQL Server 虚拟机添加到新创建的群集。 

下面的代码段创建的群集，并向其添加第一个 SQL Server 虚拟机： 

```azurecli
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
使用此命令将任何其他 SQL Server Vm 添加到群集，仅修改`-n`SQL Server 虚拟机名称参数。 

## <a name="step-4---create-availability-group"></a>步骤 4-创建可用性组
像通常那样，使用手动创建可用性组[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)， [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)，或[TRANSACT-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)。 

  >[!IMPORTANT]
  > 不要**不**这一次创建一个侦听器，因为这是通过在以下各节中的 Azure CLI。  

## <a name="step-5---create-internal-load-balancer"></a>步骤 5-创建内部负载均衡器

Always On 可用性组 (AG) 侦听器都需要内部 Azure 负载均衡器 (ILB)。 ILB 为 AG 侦听程序提供“浮动”IP 地址，可以加快故障转移和重新连接的速度。 如果可用性组中的 SQL Server VM 属于同一个可用性集，则你可以使用基本负载均衡器；否则，需要使用标准负载均衡器。  **ILB 应与 SQL Server VM 实例位于同一 vNet 中。** 

下面的代码段将创建内部负载均衡器：

```azurecli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > 每个 SQL Server VM 的公共 IP 资源应有一个与标准负载均衡器兼容的标准 SKU。 若要确定 VM 公共 IP 资源的 SKU，请导航到你的**资源组**，选择所需 SQL Server VM 的“公共 IP 地址”资源，并在“概述”窗格的“SKU”下面找到该值。  

## <a name="step-6---create-availability-group-listener"></a>步骤 6-创建可用性组侦听器
一旦手动创建可用性组后，你可以使用创建侦听器[az sql vm ag 侦听器](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)。 


- **子网资源 ID**的值`/subnets/<subnetname>`追加到的 vNet 资源的资源 ID。 若要标识的子网资源 ID，请执行以下操作：
   1. 导航到中的资源组[Azure 门户](https://portal.azure.com)。 
   1. 选择 vNet 的资源。 
   1. 选择**属性**中**设置**窗格。 
   1. 确定在 vNet 的资源 ID，并追加`/subnets/<subnetname>`到末尾，创建子网的资源 id。 例如：
        - 我的 vNet 资源 ID 是： `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - 我的子网名称是`default`。
        - 因此，我的子网资源 ID 是： `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


下面的代码段将创建可用性组侦听器：

```azurecli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>修改可用性组中的副本数
没有附加的复杂性层部署到 Azure 中托管的 SQL Server Vm 的可用性组，因为由资源提供程序，并通过现在处于托管资源时`virtual machine group`。 在这种情况下，当添加或删除副本到可用性组时，是侦听器元数据更新 SQL Server Vm 的相关信息的一个额外的步骤。 因此，当修改可用性组中的副本数，您还必须使用[az sql vm 组 ag 侦听器更新](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update)命令以使用 SQL Server 虚拟机的元数据更新侦听器。 


### <a name="add-a-replica"></a>添加副本

将新副本添加到可用性组，请执行以下操作：

1. 将 SQL Server 虚拟机添加到群集：
   ```azurecli
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. 使用 SQL Server Management Studio (SSMS) 将 SQL Server 实例添加为可用性组中的副本。
1. 将 SQL Server 虚拟机元数据添加到侦听器：
   ```azurecli
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>删除副本

若要从可用性组中删除副本，请执行以下操作：

1. 从使用 SQL Server Management Studio (SSMS) 的可用性组中删除该副本。 
1. 从侦听器中删除 SQL Server 虚拟机元数据：
   ```azurecli
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 从群集中删除 SQL Server 虚拟机：
   ```azurecli
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>删除可用性组侦听程序
如果需要更高版本中删除可用性组侦听器配置使用 Azure CLI，则必须经过 SQL 虚拟机资源提供程序。 因为侦听程序是通过 SQL VM 资源提供程序注册的，仅仅通过 SQL Server Management Studio 删除它是不够的。 它实际上应通过使用 Azure CLI 的 SQL VM 资源提供程序删除。 这样会从 SQL VM 资源提供程序中删除 AG 侦听程序元数据，并将侦听程序从可用性组中实际删除。 

以下代码片段同时从 SQL 资源提供程序和可用性组中删除 SQL 可用性组侦听程序： 

```azurecli
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [SQL Server VM 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM 发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切换 SQL Server VM 的许可模型](virtual-machines-windows-sql-ahb.md)
* [Always On 可用性组概述&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [配置 Always On 可用性组的服务器实例的&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [管理可用性组&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [监视可用性组&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [为 Always On 可用性组的 TRANSACT-SQL 语句概述&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Always On 可用性组的 PowerShell Cmdlet 概述&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
