---
title: 在 Azure 中更改 SQL VM 的许可模式
description: 了解如何使用 Azure 混合权益在 Azure 中将 SQL Server VM 的许可从即用即付许可切换为自带许可。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f02f31e0fc8943682af77ca6f506d15f36e88146
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668893"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>在 Azure 中更改 SQL 虚拟机的许可模式
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


本文介绍如何在 Azure 中使用新的 SQL Server VM 资源提供程序 Microsoft.SqlVirtualMachine 来更改 SQL Server 虚拟机 (VM) 的许可模式。

托管 SQL Server 的 VM 有三种许可模式：即用即付、Azure 混合权益 (AHB) 和灾难恢复 (DR)。 可以使用 Azure 门户、Azure CLI 或 PowerShell 来修改 SQL Server VM 的许可模式。 

- “即用即付”模型意味着 Azure VM 的每秒运行成本包括 SQL Server 许可的费用。
- 借助 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)，你可以将自己的 SQL Server 许可与运行 SQL Server 的 VM 一起使用。 
- “灾难恢复”许可类型用于 Azure 中的[免费 DR 副本](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)。 

Azure 混合权益允许在 Azure 虚拟机上使用带有软件保障（“合格许可”）的 SQL Server 许可。 使用 Azure 混合权益，客户无需支付在 VM 上使用 SQL Server 许可的费用， 但仍须支付基础云计算（即，基准费率）、存储和备份的费用， 还必须支付与使用服务（如果适用）相关的 I/O 的费用。

根据 Microsoft 产品条款：“在 Azure 上配置工作负载时，客户必须表明他们是在适用于 SQL Server 的 Azure 混合权益下使用 Azure SQL 数据库（托管实例、弹性池和单一数据库）、Azure 数据工厂、SQL Server Integration Services 或 SQL Server 虚拟机。”

要表明是在 Azure VM 上使用适用于 SQL Server 的 Azure 混合权益并符合标准，有以下三个选项可供选择：

- 使用 Azure 市场中的自带许可 SQL Server 映像预配虚拟机。 此选项仅适用于拥有企业协议的客户。
- 使用 Azure 市场中的即用即付 SQL Server 映像预配虚拟机，并激活 Azure 混合权益。
- 在 Azure VM 上自行安装 SQL Server，手动[注册到 SQL VM 资源提供程序](sql-vm-resource-provider-register.md)，并激活 Azure 混合权益。

可以在预配 VM 时或之后的任何时间配置 SQL Server 的许可类型。 在许可模式之间进行切换不会导致停机，不会重启 VM 或 SQL Server 服务，不会增加费用，并且会立即生效。 事实上，激活 Azure 混合权益可降低成本。

## <a name="prerequisites"></a>先决条件

更改 SQL Server VM 的许可模式具有以下要求： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 已注册到 [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 的 [SQL VM 资源提供程序](sql-vm-resource-provider-register.md)。
- [软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)是利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)的一项要求。 


## <a name="vms-already-registered-with-the-resource-provider"></a>已注册到资源提供程序的 VM 

# <a name="the-azure-portal"></a>[Azure 门户](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

可以直接从门户修改许可模式： 

1. 打开 [Azure 门户](https://portal.azure.com)并从 SQL Server VM 打开 [SQL 虚拟机资源](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)。 
1. 选择“设置”下的“配置”。 
1. 选择“Azure 混合权益”选项，然后选中该复选框以确认你拥有具有软件保障的 SQL Server 许可。 
1. 选择“配置”页底部的“应用”。 

![门户中的 Azure 混合权益](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以通过 Azure CLI 更改许可模式。  


**Azure 混合权益**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**即用即付**： 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**灾难恢复 (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

可以使用 PowerShell 更改许可模式。

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

## <a name="vms-not-registered-with-the-resource-provider"></a>未注册到资源提供程序的 VM

如果从即用即付 Azure 市场映像预配了 SQL Server VM，则 SQL Server 许可类型将为“即用即付”。 如果使用 Azure 市场中的自带许可映像预配了 SQL Server VM，则许可类型将为 AHUB。 从默认（即用即付）或自带许可 Azure 市场映像中预配的所有 SQL Server VM 将自动注册到 SQL VM 资源提供程序，因此，它们可以更改[许可类型](#vms-already-registered-with-the-resource-provider)。

仅可通过 Azure 混合权益在 Azure VM 上自行安装 SQL Server。 应[将这些 VM 注册到 SQL VM 资源提供程序](sql-vm-resource-provider-register.md)，方法是将 SQL Server 许可设置为 Azure 混合权益，以表明根据 Microsoft 产品条款使用 Azure 混合权益。

仅当 SQL Server VM 注册到 SQL VM 资源提供程序时，才能将 SQL Server VM 的许可类型更改为即用即付或 Azure 混合权益。

## <a name="remarks"></a>备注

- Azure 云解决方案提供商 (CSP) 客户可以通过先部署即用即付 VM，然后将其转换为自带许可（如果他们具有有效的软件保障）来使用 Azure 混合权益。
- 如果删除了 SQL Server VM 资源，则会回退到映像的硬编码许可设置。 
- 更改许可模式是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 Azure 市场映像会自动将 SQL Server VM 注册到资源提供程序。 但自行安装 SQL Server 的客户需要手动[注册其 SQL Server VM](sql-vm-resource-provider-register.md)。 
- 若要将 SQL Server VM 添加到可用性集，则需要重新创建 VM。 因此，添加到可用性集的任何 VM 都将恢复为默认的即用即付许可类型。 需要重新启用 Azure 混合权益。 


## <a name="limitations"></a>限制

更改许可模式的步骤如下：
   - 仅适用于具有[软件保障](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)的客户。
   - 仅支持 SQL Server 的 Standard 版和 Enterprise 版。 不支持更改 Express、Web 和 Developer 版本的许可。 
   - 仅支持通过 Azure 资源管理器模型部署的虚拟机。 不支持通过经典模型部署的虚拟机。 
   - 仅适用于公共云或 Azure 政府云。 
   - 仅支持具有单个网络接口 (NIC) 的虚拟机。 


## <a name="known-errors"></a>已知错误

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>找不到资源组“\<resource-group>”下的资源“Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>”。

尝试在尚未注册到 SQL VM 资源提供程序的 SQL Server VM 上更改许可模式时，将发生此错误：

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

需要先将订阅注册到资源提供程序，然后[将 SQL Server VM 注册到资源提供程序](sql-vm-resource-provider-register.md)。 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>虚拟机“\<vmname\>”与多个 NIC 相关联

此错误发生在具有多个 NIC 的虚拟机上。 请先删除其中一个 NIC，再更改许可模式。 虽然可以在更改许可模式后将该 NIC 再添加回 VM，但将不再支持 Azure 门户中的操作（如自动备份和修补）。 


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](../../database/doc-changes-updates-release-notes.md)


