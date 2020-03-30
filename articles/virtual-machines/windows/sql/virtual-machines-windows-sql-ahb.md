---
title: 更改 Azure 中 SQL Server VM 的许可证模型
description: 了解如何使用 Azure 混合权益将 Azure 中的 SQL Server 虚拟机的许可从即用即付切换到自带许可证。
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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201810"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>更改 Azure 中 SQL Server 虚拟机的许可证模型
本文介绍如何使用新的 SQL VM 资源提供程序**Microsoft.SqlVirtualItit**，更改 Azure 中的 SQL Server 虚拟机 （VM） 的许可证模型。

托管 SQL Server 的 VM 有三个许可证模型：即即付即用、Azure 混合权益和灾难恢复 （DR）。 可以使用 Azure 门户、Azure CLI 或 PowerShell 修改 SQL Server VM 的许可证模型。 

- 即**用即付**模型意味着运行 Azure VM 的每秒成本包括 SQL Server 许可证的成本。
- [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)允许您将自己的 SQL Server 许可证与正在运行的 SQL Server 的 VM 一起使用。 
- **灾难恢复**许可证类型用于 Azure 中的[免费 DR 副本](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。 

Azure 混合权益允许在 Azure 虚拟机上使用具有软件保障（"合格许可证"）的 SQL Server 许可证。 使用 Azure 混合权益，客户在 VM 上使用 SQL Server 许可证不收取费用。 但是，他们仍必须支付基础云计算（即基本速率）、存储和备份的成本。 他们还必须支付与使用服务相关的 I/O（如适用）。

根据 Microsoft 产品术语："客户必须指示他们在 Azure AZURE 下使用 Azure SQL 数据库（托管实例、弹性池和单个数据库）、Azure 数据工厂、SQL 服务器集成服务或 SQL Server 虚拟机在 Azure 上配置工作负荷时，SQL Server 的混合优势。

要指示在 Azure VM 上对 SQL Server 使用 Azure 混合权益并符合要求，可以使用三个选项：

- 使用 Azure 应用商店中自带许可证 SQL Server 映像预配虚拟机。 此选项仅适用于具有企业协议的客户。
- 使用 Azure 应用商店中的即付 SQL Server 映像预配虚拟机，并激活 Azure 混合权益。
- 在 Azure VM 上自行安装 SQL 服务器，手动[向 SQL VM 资源提供程序注册](virtual-machines-windows-sql-register-with-resource-provider.md)，并激活 Azure 混合权益。

在预配 VM 时设置 SQL Server 的许可证类型。 之后您可以随时更改它。 在许可证模型之间切换不会导致停机，不会重新启动 VM 或 SQL Server 服务，不会增加任何额外费用，并立即生效。 事实上，激活 Azure 混合优势*会降低成本*。

## <a name="prerequisites"></a>先决条件

更改 SQL Server VM 的许可模型具有以下要求： 

- [Azure 订阅](https://azure.microsoft.com/free/)。
- 在[SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)中注册的[SQL Server VM。](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)
- [软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)是利用[Azure 混合优势的要求](https://azure.microsoft.com/pricing/hybrid-benefit/)。 


## <a name="vms-already-registered-with-the-resource-provider"></a>已注册到资源提供程序的 VM 

# <a name="portal"></a>[门户](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

可以直接从门户修改许可证模型： 

1. 打开[Azure 门户](https://portal.azure.com)并打开 SQL Server VM 的[SQL 虚拟机资源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)。 
1. 选择 **"在设置**下**配置**"。 
1. 选择**Azure 混合权益**选项，然后选中"复选框以确认您拥有具有软件保障的 SQL Server 许可证。 
1. 在 **"配置"** 页的底部选择 **"应用**"。 

![门户中的 Azure 混合优势](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用 Azure CLI 更改许可证模型。  


**Azure 混合优势**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**付你而去**： 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**灾难恢复 （DR）**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

您可以使用 PowerShell 更改许可证型号。

**Azure 混合权益**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**即用即付**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**灾难恢复** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>未在资源提供程序注册的 VM

如果从即用即付 Azure 应用商店映像预配 SQL Server VM，则 SQL Server 许可证类型将为即用即付。 如果使用 Azure 应用商店中的自带许可证映像预配 SQL Server VM，则许可证类型将为 AHUB。 从默认（即用即付）或自带许可证 Azure 应用商店映像预配的所有 SQL Server VM 将自动注册到 SQL VM 资源提供程序，以便他们可以更改[许可证类型](#vms-already-registered-with-the-resource-provider)。

您仅有资格通过 Azure 混合权益在 Azure VM 上自行安装 SQL 服务器。 应通过将 SQL Server 许可证设置为 Azure 混合权益来将这些[VM 注册到 SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)，以根据 Microsoft 产品条款指示 Azure 混合权益使用情况。

仅当 SQL Server VM 注册到 SQL VM 资源提供程序时，才能将 SQL Server VM 的许可证类型更改为即用即付或 Azure 混合权益。

## <a name="remarks"></a>备注

- Azure 云解决方案提供商 （CSP） 客户可以使用 Azure 混合权益，首先部署即用即付 VM，然后将其转换为自带许可证（如果他们具有有效的软件保障）。
- 如果删除 SQL Server VM 资源，将返回映像的硬编码许可证设置。 
- 更改许可证模型的功能是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 Azure 应用商店映像会自动向资源提供程序注册 SQL Server VM。 但是，自行安装 SQL Server 的客户将需要手动[注册其 SQL Server VM。](virtual-machines-windows-sql-register-with-resource-provider.md) 
- 将 SQL Server VM 添加到可用性集需要重新创建 VM。 因此，添加到可用性集的任何 VM 都将回到默认的即用即付许可证类型。 需要再次启用 Azure 混合权益。 


## <a name="limitations"></a>限制

更改许可证模型是：
   - 仅适用于具有[软件保障的客户](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)。
   - 仅适用于 SQL Server 的标准和企业版。 不支持 Express、Web 和开发人员的许可证更改。 
   - 仅支持通过 Azure 资源管理器模型部署的虚拟机。 不支持通过经典模型部署的虚拟机。 
   - 仅适用于公共或 Azure 政府云。 
   - 仅在具有单个网络接口 （NIC） 的虚拟机上受支持。 


## <a name="known-errors"></a>已知错误

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>找不到资源组"资源组>"下的资源"Microsoft.SqlVirtual\<\<计算机/SqlVirtual计算机/SqlVirtualMachines/资源组>"。

当您尝试更改尚未向 SQL VM 资源提供程序注册的 SQL Server VM 上的许可证模型时，将发生此错误：

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

您需要向资源提供程序注册订阅，然后[向资源提供程序注册 SQL Server VM。](virtual-machines-windows-sql-register-with-resource-provider.md) 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>虚拟机\<"vmname"\>有多个 NIC 关联

此错误发生在具有多个 NIC 的虚拟机上。 在更改许可模型之前，请删除其中一个 NIC。 尽管在更改许可证模型后可以将 NIC 添加回 VM，但 Azure 门户中的操作（如自动备份和修补）将不再受支持。 


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL 服务器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 服务器常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 服务器定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 服务器的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


