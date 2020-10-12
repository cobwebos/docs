---
title: 将与维护配置关联的资源移到另一区域
description: 了解如何将与 VM 维护配置关联的资源移到另一 Azure 区域
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 38532fba2be1fedd275ed2e7f9dfc1bf5752499d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501647"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>将维护控制配置中的资源移到另一区域

按照本文所述将与维护控制配置关联的资源移到另一 Azure 区域。 出于多种原因，你可能需要移动配置。 例如，利用新的区域、部署在特定区域中可用的功能或服务、满足内部策略和治理要求，或者应对容量规划。

使用带自定义维护配置的维护控制，你可以对如何将平台更新应用到 [Windows](./maintenance-control-cli.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) VM、[Linux](./maintenance-control-cli.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VM 和 Azure 专用主机进行控制。 跨区域移动维护控制有几种场景：

- 若要移动与维护配置关联的资源，但不移动配置本身，请按照本文中的说明操作。
- 若要移动维护控制配置，但不移动与配置关联的资源，请按照[这些说明](move-region-maintenance-configuration.md)操作。
- 若要同时移动维护配置及其关联的资源，请先按照[这些说明](move-region-maintenance-configuration.md)操作， 然后再按照本文中的说明操作。

## <a name="prerequisites"></a>先决条件

在开始移动与维护控制配置关联的资源之前，必须满足以下先决条件：

- 在开始之前，请确保新区域中存在要移动的资源。
- 验证与要移动的 Azure VM 和 Azure 专用主机关联的维护控制配置。 分别检查每个资源。 目前无法检索多个资源的配置。
- 检索资源的配置时，请遵循以下要求：
    - 确保使用帐户的订阅 ID，而不是使用 Azure 专用主机 ID。
    - CLI：--output table 参数仅用于提高可读性，可以删除或更改。
    - PowerShell：Format-Table Name 参数仅用于提高可读性，可以删除或更改。
    - 当使用 PowerShell 时，如果尝试列出某个没有任何关联配置的资源的配置，则会出现错误。 该错误类似于：“操作失败，状态:‘未找到’。 详细信息:404 客户端错误:找不到与 url 对应的项”。

    
## <a name="prepare-to-move"></a>准备移动

1. 在开始之前，请定义以下变量。 我们为每个变量提供了一个示例。

    **变量** | **详细信息** | **示例**
    --- | ---
    $subId | 包含维护配置的订阅的 ID | "our-subscription-ID"
    $rsrcGroupName | 资源组名称 (Azure VM) | "VMResourceGroup"
    $vmName | VM 资源名称 |  "myVM"
    $adhRsrcGroupName |  资源组（专用主机） | "HostResourceGroup"
    $adh | 专用主机名 | "myHost"
    $adhParentName | 父资源名称 | "HostGroup"
    
2. 使用 PowerShell [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment?view=azps-3.5.0) 命令检索维护配置：

    - 对于 Azure 专用主机，请运行：
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - 对于 Azure VM，请运行：

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. 使用 CLI [az maintenance assignment](/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest) 命令检索维护配置：

    - 对于 Azure 专用主机，请运行：

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - 对于 Azure VM，请运行：

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>移动 

1. [按照这些说明](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)将 Azure VM 移到新区域。
2. 移动资源后，根据需要将维护配置重新应用于新区域中的资源，具体取决于是否移动了维护配置。 可以使用 [PowerShell](../virtual-machines/maintenance-control-powershell.md) 或 [CLI](../virtual-machines/maintenance-control-cli.md) 将维护配置应用于资源。


## <a name="verify-the-move"></a>验证移动情况

验证新区域中的资源，以及新区域中资源的关联配置。 

## <a name="clean-up-source-resources"></a>清理源资源

移动之后，请考虑在源区域中删除已移动的资源。


## <a name="next-steps"></a>后续步骤

如果需要移动维护配置，请按照[这些说明](move-region-maintenance-configuration.md)操作。 
