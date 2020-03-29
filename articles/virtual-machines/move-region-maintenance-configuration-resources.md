---
title: 将与维护配置关联的资源移到其他区域
description: 了解如何将与 VM 维护配置关联的资源移动到其他 Azure 区域
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304441"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>将维护控制配置中的资源移动到其他区域

请按照本文将与维护控制配置关联的资源移动到其他 Azure 区域。 出于多种原因，您可能需要移动配置。 例如，利用新区域、部署特定区域中可用的功能或服务、满足内部策略和治理要求或响应容量规划。

通过自定义维护配置进行维护控制，可以控制平台更新如何应用于[Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)和[Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VM 以及 Azure 专用主机。 有几个跨区域移动维护控制的方案：

- 要移动与维护配置关联的资源，而不是配置本身，请按照本文操作。
- 要移动维护控制配置，但不移动与配置关联的资源，请按照[这些说明操作](move-region-maintenance-configuration.md)。
- 要移动维护配置和与其关联的资源，请首先按照[这些说明操作](move-region-maintenance-configuration.md)。 然后，按照本文中的说明操作。

## <a name="prerequisites"></a>先决条件

在开始移动与维护控制配置关联的资源之前：

- 在开始之前，请确保要移动的资源存在于新区域中。
- 验证与要移动的 Azure VM 和 Azure 专用主机关联的维护控制配置。 单独检查每个资源。 当前无法检索多个资源的配置。
- 检索资源的配置时：
    - 请确保对帐户使用订阅 ID，而不是使用 Azure 专用主机 ID。
    - CLI：--输出表参数仅用于可读性，可以删除或更改。
    - PowerShell：格式表名称参数仅用于可读性，可以删除或更改。
    - 如果使用 PowerShell，则如果尝试列出没有任何关联配置的资源的配置，则会收到错误。 该错误将类似于："操作失败状态：'未找到'。 详细信息：404 客户端错误：未找到 url"。

    
## <a name="prepare-to-move"></a>准备移动

1. 在开始之前，定义这些变量。 我们为每个公司提供了一个示例。

    **变量** | **详细信息** | **示例**
    --- | ---
    $subId | 包含维护配置的订阅 ID | "我们的订阅 ID"
    $rsrcGroupName | 资源组名称（Azure VM） | "VM 资源组"
    $vmName | VM 资源名称 |  "myVM"
    $adhRsrcGroupName |  资源组（专用主机） | "主机资源组"
    $adh | 专用主机名 | "我的主机"
    $adhParentName | 父资源名称 | "东道主群"
    
2. 要使用 PowerShell[获取-AZ 配置分配](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)命令检索维护配置，请使用以下命令：

    - 对于 Azure 专用主机，运行：
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - 对于 Azure VM，运行：

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. 要使用 CLI [az 维护分配](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)命令检索维护配置，请使用以下命令：

    - 对于 Azure 专用主机：

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - 对于 Azure VM：

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>移动 

1. [按照这些说明](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)将 Azure VM 移动到新区域。
2. 移动资源后，根据需要将维护配置重新应用于新区域中的资源，具体取决于您是否移动了维护配置。 您可以使用[PowerShell](../virtual-machines/maintenance-control-powershell.md)或[CLI](../virtual-machines/maintenance-control-cli.md)将维护配置应用于资源。


## <a name="verify-the-move"></a>验证移动

验证新区域中的资源，并验证新区域中的资源的关联配置。 

## <a name="clean-up-source-resources"></a>清理源资源

移动后，请考虑删除源区域中的移动资源。


## <a name="next-steps"></a>后续步骤

如果需要移动维护配置，请按照[这些说明](move-region-maintenance-configuration.md)操作。 
