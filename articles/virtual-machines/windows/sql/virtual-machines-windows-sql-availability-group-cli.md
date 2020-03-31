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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022364"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure CLI 为 Azure VM 上的 SQL Server 配置始终打开的可用性组
本文介绍如何使用[Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/)部署 Windows 故障转移群集、向群集添加 SQL Server VM 以及为"始终打开"可用性组创建内部负载均衡器和侦听器。 始终处于打开的可用性组的部署仍通过 SQL 服务器管理工作室 （SSMS） 手动完成。 

## <a name="prerequisites"></a>先决条件
要使用 Azure CLI 自动设置"始终打开"可用性组，必须具备以下先决条件： 
- [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- Azure 中的一个或多个域加入[的 VM 运行 SQL Server 2016（或更高版本）中的](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)*同一可用性集或*[已注册到 SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)的不同可用性区域。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 两个可用的（任何实体不使用）IP 地址。 一个用于内部负载平衡器。 另一个用于可用性组与可用性组位于同一子网中的可用性组侦听器。 如果使用现有负载均衡器，则可用性组侦听器只需要一个可用的 IP 地址。 

## <a name="permissions"></a>权限
使用 Azure CLI 配置"始终打开"可用性组需要以下帐户权限： 

- 在域中具有 **"创建计算机对象"** 权限的现有域用户帐户。 例如，域管理员帐户通常具有足够的权限（例如： account@domain.com __ 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 控制 SQL Server 服务的域用户帐户。 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>第 1 步：创建存储帐户作为云见证
群集需要一个存储帐户来充当云见证人。 您可以使用任何现有的存储帐户，也可以创建新的存储帐户。 如果要使用现有存储帐户，请跳到下一节。 

以下代码段创建存储帐户： 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 如果使用过时的 Azure `az sql: 'vm' is not in the 'az sql' command group` CLI 版本，可能会看到错误。 下载[最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)以克服此错误。

## <a name="step-2-define-windows-failover-cluster-metadata"></a>第 2 步：定义 Windows 故障转移群集元数据
Azure CLI [az sql vm 组](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest)命令组管理承载可用性组的 Windows 服务器故障转移群集 （WSFC） 服务的元数据。 群集元数据包括活动目录域、群集帐户、用作云见证的存储帐户和 SQL Server 版本。 使用[az sql vm 组创建](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create)来定义 WSFC 的元数据，以便在添加第一个 SQL Server VM 时，群集创建为定义。 

以下代码段定义群集的元数据：
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>步骤 3：将 SQL Server VM 添加到群集
将第一个 SQL Server VM 添加到群集将创建群集。 [az sql vm 添加到组](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group)命令创建具有以前给定名称的群集，在 SQL Server VM 上安装群集角色，并将它们添加到群集。 命令的`az sql vm add-to-group`后续使用将更多的 SQL Server VM 添加到新创建的群集中。 

以下代码段创建群集并将第一个 SQL Server VM 添加到群集： 

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
使用此命令向群集添加任何其他 SQL Server VM。 仅修改`-n`SQL Server VM 名称的参数。 

## <a name="step-4-create-the-availability-group"></a>第 4 步：创建可用性组
通过使用[SQL 服务器管理工作室](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)[、PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)或[Transact-SQL，](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)按照通常方式手动创建可用性组。 

>[!IMPORTANT]
> 此时*不要*创建侦听器，因为此操作通过以下部分中的 Azure CLI 完成。  

## <a name="step-5-create-the-internal-load-balancer"></a>第 5 步：创建内部负载均衡器

始终打开可用性组侦听器需要 Azure 负载均衡器的内部实例。 内部负载均衡器为可用性组侦听器提供"浮动"IP 地址，以便更快地进行故障转移和重新连接。 如果可用性组中的 SQL Server VM 属于同一可用性集的一部分，则可以使用基本负载均衡器。 否则，您需要使用标准负载均衡器。  

> [!NOTE]
> 内部负载均衡器应与 SQL Server VM 实例位于同一虚拟网络中。 

以下代码段创建内部负载均衡器：

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 每个 SQL Server VM 的公共 IP 资源应具有与标准负载均衡器兼容的标准 SKU。 要确定 VM 公共 IP 资源的 SKU，请转到**资源组**，为所需的 SQL Server VM 选择**公共 IP 地址**资源，并在 **"概述"** 窗格中查找**SKU**下的值。  

## <a name="step-6-create-the-availability-group-listener"></a>步骤 6：创建可用性组侦听器
手动创建可用性组后，可以使用[az sql vm ag-侦听器](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)创建侦听器。 

*子网资源 ID*是`/subnets/<subnetname>`追加到虚拟网络资源的资源 ID 的值。 要标识子网资源 ID，
   1. 转到[Azure 门户](https://portal.azure.com)中的资源组。 
   1. 选择虚拟网络资源。 
   1. 在 **"设置"** 窗格中选择 **"属性**"。 
   1. 标识虚拟网络的资源 ID，并将其追加`/subnets/<subnetname>`到虚拟网络的末尾以创建子网资源 ID。 例如：
      - 您的虚拟网络资源 ID 是：`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 您的子网名称为：`default`
      - 因此，您的子网资源 ID 是：`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


以下代码段创建可用性组侦听器：

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
将可用性组部署到 Azure 中托管的 SQL Server VM 时，会增加复杂性层。 资源提供程序和虚拟机组现在管理资源。 因此，当您在可用性组中添加或删除副本时，还有一个附加步骤，即使用有关 SQL Server VM 的信息更新侦听器元数据。 修改可用性组中的副本数时，还必须使用[az sql vm 组 ag-侦听器更新](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update)命令使用 SQL Server VM 的元数据更新侦听器。 


### <a name="add-a-replica"></a>添加副本

要向可用性组添加新副本，可以：

1. 将 SQL Server VM 添加到群集：
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. 使用 SQL 服务器管理工作室将 SQL Server 实例添加为可用性组中的副本。
1. 将 SQL Server VM 元数据添加到侦听器：
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>删除副本

要从可用性组中删除副本，请：

1. 使用 SQL 服务器管理工作室从可用性组中删除副本。 
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
如果以后需要删除使用 Azure CLI 配置的可用性组侦听器，则必须通过 SQL VM 资源提供程序。 由于侦听器是通过 SQL VM 资源提供程序注册的，因此只需通过 SQL 服务器管理工作室删除侦听器是不够的。 

最好的方法是通过使用 Azure CLI 中的以下代码段通过 SQL VM 资源提供程序将其删除。 这样做会从 SQL VM 资源提供程序中删除可用性组侦听器元数据。 它还从可用性组物理上删除侦听器。 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [SQL 服务器 VM 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL 服务器 VM 的常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL 服务器 VM 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切换 SQL Server VM 的许可模型](virtual-machines-windows-sql-ahb.md)
* [始终打开可用性组&#40;SQL Server&#41;概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [&#40;SQL 服务器&#41;，为始终处于可用性组配置服务器实例](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [管理可用性组&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [监视 &#40;SQL Server&#41;的可用性组](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [始终打开可用性组&#40;SQL 服务器&#41;的 Transact-SQL 语句概述](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [始终打开可用性组&#40;SQL Server&#41;的 PowerShell cmdlet 概述](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
