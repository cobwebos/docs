---
title: 将维护配置移动到另一个 Azure 区域
description: 了解如何将 VM 维护配置移动到另一个 Azure 区域
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4cff7eb4a69005f2e74747b6e58447f100c69b60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501596"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>将维护控制配置移动到另一个区域

按照本文将维护控制配置移动到另一个 Azure 区域。 出于多种原因，你可能需要移动配置。 例如，利用新的区域、部署在特定区域中可用的功能或服务、满足内部策略和治理要求，或者响应容量规划。

使用带自定义维护配置的维护控制，你可以对如何将平台更新应用到 [Windows](./maintenance-control-cli.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) VM、[Linux](./maintenance-control-cli.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VM 和 Azure 专用主机进行控制。 跨区域移动维护控制有几种场景：

- 若要移动维护控制配置，但不移动与配置关联的资源，请按本文中的说明进行操作。
- 若要移动与维护配置关联的资源，但不移动配置本身，请按照[这些说明](move-region-maintenance-configuration-resources.md)进行操作。
- 若要同时移动维护配置及其关联的资源，请先按照本文中的说明进行操作， 然后按照[这些说明](move-region-maintenance-configuration-resources.md)进行操作。

## <a name="prerequisites"></a>先决条件

在开始移动维护控制配置之前，必须满足以下先决条件：

- 维护配置与 Azure VM 或 Azure 专用主机相关联。 在开始之前，请确保新区域中存在 VM/主机资源。
- 识别： 
    - 现有的维护控制配置。
    - 现有配置当前所在的资源组。 
    - 在移动到新区域后要将配置添加到其中的资源组。 
    - 与要移动的维护配置关联的资源。
    - 检查新区域中的资源是否是与当前的维护配置关联的资源。 这些配置在新区域中的名称可以与旧区域中的相同，但这不是必需的。

## <a name="prepare-and-move"></a>准备并移动 

1. 检索每个订阅中的所有维护配置。 运行 CLI 命令 [az maintenance configuration list](/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) 来执行此操作，将 $subId 替换为你的订阅 ID。

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 查看所返回的、订阅中的配置记录的表列表。 下面是一个示例。 你的列表会包含你的特定环境的值。

    **名称** | **位置** | **资源组**
    --- | --- | ---
    跳过维护 | eastus2 | configuration-resource-group
    IgniteDemoConfig | eastus2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | eastus | test-configuration
    

3. 保存你的列表供参考。 当你移动配置时，它可帮助你验证是否已移动所有内容。
4. 请将用作参考的每个配置/资源组映射到新区域中的新资源组。
5. 使用 [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration) 或 [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration) 在新区域中创建新的维护配置。
6. 使用 [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration) 或 [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration) 将配置与新区域中的资源相关联。


## <a name="verify-the-move"></a>验证移动情况

在移动配置后，将新区域中的配置和资源与你准备的表列表进行比较。


## <a name="clean-up-source-resources"></a>清理源资源

移动之后，请考虑使用 [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration) 或 [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration) 删除源区域中已移动的维护配置。


## <a name="next-steps"></a>后续步骤

如果需要移动与维护配置关联的资源，请按照[这些说明](move-region-maintenance-configuration-resources.md)进行操作。 
