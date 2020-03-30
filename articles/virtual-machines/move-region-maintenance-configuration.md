---
title: 将维护配置移动到其他 Azure 区域
description: 了解如何将 VM 维护配置移动到其他 Azure 区域
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304454"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>将维护控制配置移到其他区域

请按照本文将维护控制配置移动到其他 Azure 区域。 出于多种原因，您可能需要移动配置。 例如，利用新区域、部署特定区域中可用的功能或服务、满足内部策略和治理要求或响应容量规划。

通过自定义维护配置进行维护控制，可以控制平台更新如何应用于[Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)和[Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VM 以及 Azure 专用主机。 有几个跨区域移动维护控制的方案：

- 要移动维护控制配置（而不是与配置关联的资源），请按照本文中的说明操作。
- 要移动与维护配置关联的资源，而不是配置本身，请按照[这些说明操作](move-region-maintenance-configuration-resources.md)。
- 要移动维护配置及其关联的资源，请先按照本文中的说明操作。 然后，按照[这些说明](move-region-maintenance-configuration-resources.md)。

## <a name="prerequisites"></a>先决条件

在开始移动维护控制配置之前：

- 维护配置与 Azure VM 或 Azure 专用主机相关联。 在开始之前，请确保 VM/主机资源存在于新区域中。
- “标识”： 
    - 现有的维护控制配置。
    - 现有配置当前驻留在其中的资源组。 
    - 移动到新区域后要向其添加配置的资源组。 
    - 与要移动的维护配置关联的资源。
    - 检查新区域中的资源与当前维护配置关联的资源相同。 配置在新区域中可以具有与旧区域相同的名称，但这不是必需的。

## <a name="prepare-and-move"></a>准备并移动 

1. 检索每个订阅中的所有维护配置。 运行 CLI [az 维护配置列表](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list)命令以执行此操作，将$subId替换为订阅 ID。

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 查看订阅中配置记录的返回表列表。 下面是一个示例。 您的列表将包含特定环境的值。

    **名称** | **位置** | **资源组**
    --- | --- | ---
    跳过维护 | eastus2 | 配置资源组
    点火演示 | eastus2 | 配置资源组
    默认维护配置 -eastus | eastus | 测试配置
    

3. 保存列表以供参考。 移动配置时，它可以帮助您验证所有内容是否已移动。
4. 作为参考，将每个配置/资源组映射到新区域中的新资源组。
5. 使用[PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)（或[CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)） 在新区域创建新的维护配置。
6. 使用[PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)将配置与新区域中的资源相关联。


## <a name="verify-the-move"></a>验证移动

移动配置后，将新区域中的配置和资源与您准备的表列表进行比较。


## <a name="clean-up-source-resources"></a>清理源资源

移动后，请考虑删除源区域[PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)中的移动维护配置。


## <a name="next-steps"></a>后续步骤

如果需要移动与维护配置关联的资源，请按照[这些说明](move-region-maintenance-configuration-resources.md)操作。 
