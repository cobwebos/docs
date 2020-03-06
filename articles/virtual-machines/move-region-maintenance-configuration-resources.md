---
title: 将与维护配置关联的资源移到另一个区域
description: 了解如何将与 VM 维护配置关联的资源移到另一个 Azure 区域
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304441"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>将维护控制配置中的资源移动到另一个区域

按照此文章将与维护控制配置关联的资源移到不同的 Azure 区域。 出于多种原因，你可能需要移动配置。 例如，若要利用新的区域，可以部署特定区域中可用的功能或服务，以满足内部策略和调控要求，或响应容量规划。

维护控制（带有自定义的维护配置）允许您控制将平台更新应用于[Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)和[Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) Vm 以及 Azure 专用主机的方式。 对于跨区域移动维护控制，有几种方案：

- 若要移动与维护配置关联的资源，而不是配置本身，请遵循此文。
- 若要移动维护控制配置，而不是与配置关联的资源，请遵循[这些说明](move-region-maintenance-configuration.md)。
- 若要同时移动维护配置和与其关联的资源，请先遵循[这些说明](move-region-maintenance-configuration.md)。 然后，按照本文中的说明进行操作。

## <a name="prerequisites"></a>先决条件

在开始移动与维护控制配置关联的资源之前：

- 在开始之前，请确保新区域中已存在要移动的资源。
- 验证与要移动的 Azure Vm 和 Azure 专用主机关联的维护控制配置。 单独检查每个资源。 目前没有任何方法可以检索多个资源的配置。
- 检索资源的配置时：
    - 请确保使用该帐户的订阅 ID，而不是 Azure 专用主机 ID。
    - CLI：--output table 参数仅用于可读性，可以删除或更改。
    - PowerShell： Format-Table Name 参数仅用于可读性，可以删除或更改。
    - 如果你使用 PowerShell，则如果你尝试列出没有任何关联配置的资源的配置，则会出现错误。 此错误将类似于： "操作失败，状态为： ' 找不到 '。 详细信息：404客户端错误：找不到 url "。

    
## <a name="prepare-to-move"></a>准备移动

1. 在开始之前，请定义这些变量。 我们为每个提供了一个示例。

    **变量** | **详细信息** | **示例**
    --- | ---
    $subId | 包含维护配置的订阅的 ID | "我们的订阅 ID"
    $rsrcGroupName | 资源组名称（Azure VM） | "VMResourceGroup"
    $vmName | VM 资源名称 |  MyVM
    $adhRsrcGroupName |  资源组（专用主机） | "HostResourceGroup"
    $adh | 专用主机名 | Myhost 代表
    $adhParentName | 父资源名称 | HostGroup
    
2. 使用 PowerShell [AZConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)命令检索维护配置：

    - 对于 Azure 专用主机，请运行：
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - 对于 Azure Vm，请运行：

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. 使用 CLI [az 维护分配](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)命令检索维护配置：

    - 对于 Azure 专用主机：

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - 对于 Azure Vm：

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>移动 

1. [按照以下说明](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)将 Azure vm 移到新区域。
2. 移动资源后，根据是否移动了维护配置，将维护配置重新应用到新区域中的资源。 可以使用[PowerShell](../virtual-machines/maintenance-control-powershell.md)或[CLI](../virtual-machines/maintenance-control-cli.md)将维护配置应用于资源。


## <a name="verify-the-move"></a>验证移动

验证新区域中的资源，并验证新区域中资源的关联配置。 

## <a name="clean-up-source-resources"></a>清理源资源

移动之后，请考虑在源区域中删除移动的资源。


## <a name="next-steps"></a>后续步骤

如果需要移动维护配置，请遵循[这些说明](move-region-maintenance-configuration.md)。 
