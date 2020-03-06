---
title: 将维护配置移动到另一个 Azure 区域
description: 了解如何将 VM 维护配置移动到另一个 Azure 区域
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304454"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>将维护控制配置移动到另一个区域

按照本文中的步骤，将维护控制配置转移到不同的 Azure 区域。 出于多种原因，你可能需要移动配置。 例如，若要利用新的区域，可以部署特定区域中可用的功能或服务，以满足内部策略和调控要求，或响应容量规划。

维护控制（带有自定义的维护配置）允许您控制将平台更新应用于[Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)和[Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) Vm 以及 Azure 专用主机的方式。 对于跨区域移动维护控制，有几种方案：

- 若要移动维护控制配置，而不是与配置关联的资源，请按照本文中的说明进行操作。
- 若要移动与维护配置关联的资源，而不是配置本身，请遵循[这些说明](move-region-maintenance-configuration-resources.md)。
- 若要同时移动维护配置和与其关联的资源，请先按照本文中的说明进行操作。 然后，请遵循[这些说明](move-region-maintenance-configuration-resources.md)。

## <a name="prerequisites"></a>先决条件

在开始移动维护控制配置之前：

- 维护配置与 Azure Vm 或 Azure 专用主机关联。 在开始之前，请确保虚拟机/主机资源存在于新区域中。
- “标识”： 
    - 现有的维护控制配置。
    - 现有配置当前所在的资源组。 
    - 将在移动到新区域后将配置添加到的资源组。 
    - 与要移动的维护配置关联的资源。
    - 检查新区域中的资源是否与当前维护配置关联。 这些配置在新区域中的名称可能与旧名称相同，但这不是必需的。

## <a name="prepare-and-move"></a>准备并移动 

1. 检索每个订阅中的所有维护配置。 运行 CLI [az 维护配置 list](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list)命令来执行此操作，并将 $subId 替换为你的订阅 ID。

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 查看订阅中的配置记录的返回表列表。 下面是一个示例。 你的列表将包含你的特定环境的值。

    **名称** | **位置** | **资源组**
    --- | --- | ---
    跳过维护 | eastus2 | 配置-资源组
    IgniteDemoConfig | eastus2 | 配置-资源组
    defaultMaintenanceConfiguration-eastus | eastus | 测试-配置
    

3. 保存列表以供参考。 当你移动配置时，它将帮助你验证是否已移动所有内容。
4. 作为参考，将每个配置/资源组映射到新区域中的新资源组。
5. 使用[PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)在新区域中创建新的维护配置。
6. 使用[PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)将配置与新区域中的资源相关联。


## <a name="verify-the-move"></a>验证移动

在移动配置后，将新区域中的配置和资源与已准备的表列表进行比较。


## <a name="clean-up-source-resources"></a>清理源资源

移动之后，请考虑在源区域、 [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)中删除移动的维护配置。


## <a name="next-steps"></a>后续步骤

如果需要移动与维护配置关联的资源，请遵循[这些说明](move-region-maintenance-configuration-resources.md)。 
