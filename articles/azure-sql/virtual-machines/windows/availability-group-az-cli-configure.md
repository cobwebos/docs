---
title: 使用 Azure CLI 配置可用性组
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
ms.openlocfilehash: 23667e8a50d2ef3a7a31aeb165c0b5d43bcf3eca
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219627"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>使用 Azure CLI 在 Azure VM 上为 SQL Server 配置 Always On 可用性组
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何使用 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) 部署 Windows 故障转移群集，将 SQL Server VM 添加到群集，以及为 Always On 可用性组创建内部负载均衡器和侦听器。 始终通过 SQL Server Management Studio (SSMS) 手动完成 Always On 可用性组的部署。 

## <a name="prerequisites"></a>先决条件
要使用 Azure CLI 自动设置 Always On 可用性组，必须满足以下先决条件： 
- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- Azure 中的一个或多个已加入域的 VM，它们[运行 SQL Server 2016 Enterprise 版本（或更高版本）](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)，位于[已注册到 SQL VM 资源提供程序](sql-vm-resource-provider-register.md)的同一个可用性集或不同可用性区域中。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 两个可用的（未被任何实体使用的）IP 地址。 一个用于内部负载均衡器。 另一个用于与可用性组位于同一子网中的可用性组侦听器。 如果使用现有的负载均衡器，则可用性组侦听器只需要一个可用的 IP 地址。 

## <a name="permissions"></a>权限
需要具有以下帐户权限才能使用 Azure CLI 配置 Always On 可用性组： 

- 在域中具有“创建计算机对象”权限的现有域用户帐户。 例如，域管理员帐户通常拥有足够的权限（如 account@domain.com）。 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 可控制 SQL Server 的域用户帐户。 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>步骤 1：创建存储帐户作为云见证
群集需要一个存储帐户来充当云见证。 可以使用现有存储帐户，也可以创建新的存储帐户。 如果要使用现有存储帐户，请跳转至下一部分。 

下面的代码片段将创建存储帐户： 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 如果使用的是过时的 Azure CLI 版本，可能会看到错误 `az sql: 'vm' is not in the 'az sql' command group`。 下载[最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)，以跳过此错误。

## <a name="step-2-define-windows-failover-cluster-metadata"></a>步骤 2：定义 Windows 故障转移群集元数据
Azure CLI [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) 命令组管理托管可用性组的 Windows Server 故障转移群集 (WSFC) 服务的元数据。 群集元数据包括 Active Directory 域、群集帐户、要用作云见证的存储帐户和 SQL Server 版本。 使用 [az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) 定义 WSFC 的元数据，以便在添加第一个 SQL Server VM 时，按定义创建群集。 

下面的代码片段定义群集的元数据：
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
将第一个 SQL Server VM 添加到群集将创建群集。 [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) 命令使用先前指定的名称创建群集，在 SQL Server VM 上安装群集角色，然后将其添加到群集中。 继续使用 `az sql vm add-to-group` 命令将更多 SQL Server VM 添加到新创建的群集中。 

以下代码片段会创建群集，并将第一个 SQL Server VM 添加到其中： 

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
使用此命令可将任何其他 SQL Server VM 添加到群集。 仅修改 SQL Server VM 名称的 `-n` 参数。 

## <a name="step-4-create-the-availability-group"></a>步骤 4：创建可用性组
像平时一样使用 [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、[PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) 或 [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 手动创建可用性组。 

>[!IMPORTANT]
> 此时不要创建侦听器，因为下面部分将通过 Azure CLI 完成创建。  

## <a name="step-5-create-the-internal-load-balancer"></a>步骤 5：创建内部负载均衡器

Always On 可用性组侦听器需要 Azure 负载均衡器的内部实例。 内部负载均衡器为可用性组侦听器提供“浮动”IP 地址，可以加快故障转移和重新连接的速度。 如果可用性组中的 SQL Server VM 属于同一个可用性集，则可以使用基本负载均衡器。 否则，需要使用标准负载均衡器。  

> [!NOTE]
> 内部负载均衡器应与 SQL Server VM 实例位于同一虚拟网络中。 

以下代码片段会创建内部负载均衡器：

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 每个 SQL Server VM 的公共 IP 资源应有一个与标准负载均衡器兼容的标准 SKU。 要确定 VM 公共 IP 资源的 SKU，请转到“资源组”，选择所需 SQL Server VM 的“公共 IP 地址”资源，并在“概述”窗格的“SKU”下面找到该值   。  

## <a name="step-6-create-the-availability-group-listener"></a>步骤 6：创建可用性组侦听器
手动创建可用性组后，可以使用 [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create) 命令创建侦听器。 

子网资源 ID 是 `/subnets/<subnetname>` 附加到虚拟网络资源的资源 ID 的值。 标识子网资源 ID：
   1. 转到 [Azure 门户](https://portal.azure.com)中的资源组。 
   1. 选择虚拟网络资源。 
   1. 在“设置”窗格中选择“属性” 。 
   1. 标识虚拟网络的资源 ID，并在其末尾附加 `/subnets/<subnetname>`，以创建子网资源 ID。 例如：
      - 虚拟网络资源 ID 为 `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 子网名称为 `default`
      - 因此，子网资源 ID 为 `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


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
将可用性组部署到 Azure 中托管的 SQL Server VM，这会增加复杂性。 现在可使用资源提供程序和虚拟机组管理资源。 因此，将副本添加到可用性组或从中删除副本时，还有一个附加步骤，即使用有关 SQL Server VM 的信息来更新侦听器元数据。 修改可用性组中的副本数时，还必须使用 [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) 命令，以使用 SQL Server VM 的元数据更新侦听器。 


### <a name="add-a-replica"></a>添加副本

将新副本添加到可用性组：

1. 将 SQL Server VM 添加到群集中：
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. 使用 SQL Server Management Studio 将 SQL Server 实例作为副本添加到可用性组中。
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
如果以后需要删除使用 Azure CLI 配置的可用性组侦听器，则必须通过 SQL VM 资源提供程序执行整个操作。 由于侦听器是通过 SQL VM 资源提供程序注册的，因此仅仅通过 SQL Server Management Studio 删除它是不够的。 

最佳方法是在 Azure CLI 中使用以下代码片段，通过 SQL VM 资源提供程序将其删除。 这样就会从 SQL VM 资源提供程序中删除可用性组侦听器元数据。 同时还会从根本上删除可用性组中的侦听器。 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [SQL Server VM 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 的常见问题解答](frequently-asked-questions-faq.md)
* [SQL Server VM 的发行说明](../../database/doc-changes-updates-release-notes.md)
* [切换 SQL Server VM 的许可模型](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [AlwaysOn 可用性组概述 (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [为 AlwaysOn 可用性组配置服务器实例 (SQL Server)](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [管理可用性组 (SQL Server)](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [监视可用性组 (SQL Server)](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [AlwaysOn 可用性组的 Transact-SQL 语句概述 (SQL Server)](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [AlwaysOn 可用性组的 PowerShell cmdlet 概述 (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
