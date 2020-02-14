---
title: 更改 Azure 中 SQL Server VM 的许可证型号
description: 了解如何使用 Azure 混合权益在 Azure 中切换 SQL Server 虚拟机的许可，使其从即用即付许可。
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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201810"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>更改 Azure 中 SQL Server 虚拟机的许可证型号
本文介绍如何使用新的 SQL VM 资源提供程序**SqlVirtualMachine**在 Azure 中更改 SQL Server 虚拟机（VM）的许可证模型。

承载 SQL Server 的 VM 有三个许可证模型：即用即付、Azure 混合权益和灾难恢复（DR）。 您可以使用 Azure 门户、Azure CLI 或 PowerShell 来修改您的 SQL Server VM 的许可证模型。 

- 即**用即付**模型意味着运行 Azure VM 的每秒费用包括 SQL Server 许可证的成本。
- [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)允许你将自己的 SQL Server 许可证与运行 SQL SERVER 的 VM 一起使用。 
- **灾难恢复**许可证类型用于 Azure 中的[免费 DR 副本](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。 

Azure 混合权益允许在 Azure 虚拟机上使用带有软件保障（"合格许可证"）的 SQL Server 许可证。 使用 Azure 混合权益，客户无需支付对 VM 使用 SQL Server 许可证的费用。 但仍必须支付基础云计算（即，基础费率）、存储和备份的成本。 他们还必须为与服务的使用相关的 i/o 付费（如果适用）。

根据 Microsoft 产品条款： "客户必须指出他们正在使用 Azure SQL 数据库（托管实例、弹性池和单一数据库）、Azure 数据工厂、SQL Server Integration Services 或 SQL Server Azure 中的虚拟机在 Azure 上配置工作负荷时 SQL Server 的混合权益。

若要指示在 Azure VM 上使用 SQL Server 的 Azure 混合权益，并符合以下三个选项：

- 使用 Azure Marketplace 中的自带许可证 SQL Server 映像预配虚拟机。 此选项仅适用于具有企业协议的客户。
- 使用 Azure Marketplace 中的即用即付 SQL Server 映像预配虚拟机，并激活 Azure 混合权益。
- 在 Azure VM 上自行安装 SQL Server，[使用 SQL VM 资源提供程序手动注册](virtual-machines-windows-sql-register-with-resource-provider.md)，并激活 Azure 混合权益。

预配 VM 时，将设置 SQL Server 的许可证类型。 您可以在以后随时对其进行更改。 在许可证模型之间切换不会造成停机，也不会重新启动 VM 或 SQL Server 服务，不会添加任何额外的费用，并且立即生效。 事实上，激活 Azure 混合权益*降低了*成本。

## <a name="prerequisites"></a>先决条件

更改 SQL Server VM 的许可模式具有以下要求： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 向[SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)注册的[SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 。
- [软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)是利用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)的必要条件。 


## <a name="vms-already-registered-with-the-resource-provider"></a>已向资源提供程序注册的 Vm 

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

可以直接从门户修改许可证模型： 

1. 打开[Azure 门户](https://portal.azure.com)并打开 SQL Server VM 的[SQL 虚拟机资源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)。 
1. 选择 "**设置**" 下的 "**配置**"。 
1. 选择 " **Azure 混合权益**" 选项，然后选中该复选框以确认你具有具有软件保障的 SQL Server 许可证。 
1. 选择 "**配置**" 页面底部的 "**应用**"。 

![门户中的 Azure 混合权益](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

你可以使用 Azure CLI 来更改你的许可证模型。  


**Azure 混合权益**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

即**付即用**： 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**灾难恢复（DR）**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

你可以使用 PowerShell 更改你的许可证模型。

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

## <a name="vms-not-registered-with-the-resource-provider"></a>未向资源提供程序注册的 Vm

如果你从即用即付 Azure Marketplace 映像预配了 SQL Server VM，则 SQL Server 许可证类型将为 "即用即付"。 如果使用 Azure Marketplace 中的自带许可证映像预配了 SQL Server VM，则许可证类型将为 AHUB。 默认情况下（即用即付）或自带许可 Azure Marketplace 映像中预配的所有 SQL Server Vm 将自动注册到 SQL VM 资源提供程序，因此它们可以更改[许可证类型](#vms-already-registered-with-the-resource-provider)。

仅可通过 Azure 混合权益在 Azure VM 上自行安装 SQL Server。 你应将[这些 vm 注册到 SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)，方法是将 SQL Server 许可证设置为 Azure 混合权益，以根据 Microsoft 产品条款指示 Azure 混合权益的使用情况。

仅当 SQL Server VM 注册到 SQL VM 资源提供程序时，才能将 SQL Server VM 的许可证类型更改为即用即付或 Azure 混合权益。

## <a name="remarks"></a>备注

- Azure 云解决方案提供商（CSP）客户可以通过先部署即用即付 VM，然后将其转换为自带许可（如果他们具有有效的软件保障）来使用 Azure 混合权益。
- 如果删除 SQL Server VM 资源，则会返回到映像的硬编码许可证设置。 
- 更改许可模式的功能是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 Azure Marketplace 映像会自动向资源提供程序注册 SQL Server VM。 但自行安装 SQL Server 的客户需要手动[注册其 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 
- 若要将 SQL Server VM 添加到可用性集，则需要重新创建 VM。 因此，添加到可用性集的任何 Vm 都将恢复为默认的即用即付许可证类型。 需要重新启用 Azure 混合权益。 


## <a name="limitations"></a>限制

更改许可模式的步骤如下：
   - 仅适用于具有[软件保障](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)的客户。
   - 仅 SQL Server 标准版和企业版支持。 不支持速成版、Web 版和开发人员版的许可证更改。 
   - 仅支持通过 Azure 资源管理器模型部署的虚拟机。 不支持通过经典模型部署的虚拟机。 
   - 仅适用于公共或 Azure 政府云。 
   - 仅在具有单个网络接口（NIC）的虚拟机上受支持。 


## <a name="known-errors"></a>已知错误

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>找不到资源组 "\<资源组 >" 下的资源 "SqlVirtualMachine/SqlVirtualMachines/\<资源组 >"。

当你尝试在尚未注册到 SQL VM 资源提供程序的 SQL Server VM 上更改许可证模型时，将发生此错误：

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

你需要向资源提供程序注册你的订阅，然后向[资源提供程序注册你的 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>虚拟机 "\<vmname\>" 与多个 NIC 关联

此错误发生在具有多个 NIC 的虚拟机上。 在更改许可模式之前，请删除其中一个 Nic。 虽然你可以在更改许可证模型后将 NIC 添加回 VM，但将不再支持 Azure 门户中的操作（如自动备份和修补）。 


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


