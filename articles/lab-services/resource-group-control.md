---
title: 在 Azure 开发测试实验室中为 VM 指定资源组 | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中为实验室中的 VM 指定资源组。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2019
ms.author: spelluru
ms.openlocfilehash: ddda9ef2b9bb716f7cdd33aa8fe9233f6c7d8e82
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55748994"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中为实验室虚拟机指定资源组
作为实验室所有者，你可以配置要在特定的资源组中创建的实验室虚拟机。 使用此功能，在 Azure 订阅中将不会达到资源组限制。 使用此功能还可以将实验室资源合并到单个资源组中。 它还简化了对这些资源进行跟踪以及应用[策略](../governance/policy/overview.md)以在资源组级别管理它们的操作。

使用此功能，你可以使用脚本来为所有实验室 VM 指定 Azure 订阅中的新资源组或现有资源组。 当前，开发测试实验室通过一个 API 来支持此功能。 

## <a name="api-to-configure-a-resource-group-for-labs-vms"></a>用于为实验室 VM 配置资源组的 API
现在，让我们看一下作为实验室所有者，你在使用此 API 时可以采用的选项： 

- 可以为所有虚拟机选择**实验室的资源组**。
- 可以为所有虚拟机选择不同于实验室的资源组的**现有资源组**。
- 可以为所有虚拟机输入一个**新资源组**名称。
- 你可以继续遵循现有的行为，即为实验室中的每个 VM 创建一个资源组。
 
此设置适用于在实验室中创建的新虚拟机。 实验室中已在相应资源组中创建的较旧 VM 将不受影响。 但是，你可以将这些虚拟机从其各自的资源组中迁移到公用资源组中，使所有实验室虚拟机位于一个公用资源组中。 有关详细信息，请参阅[将资源移动到新资源组](../azure-resource-manager/resource-group-move-resources.md)。 已在实验室中创建的环境将继续保留在其自己的资源组中。

### <a name="how-to-use-this-api"></a>如何使用此 API：
- 使用此 API 时请使用 API 版本 **2018_10_15_preview**。 
- 如果指定新资源组，请确保你**对订阅中的资源组具有写入权限**。 如果没有写入权限，则在指定的资源组中创建新虚拟机将导致失败。 
- 使用 API 时，请传入**完整的资源组 ID**。 例如：`/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`。 请确保资源组与实验室位于同一订阅中。 

## <a name="use-powershell"></a>使用 PowerShell 
下面的示例介绍了如何使用 PowerShell 脚本在新资源组中创建所有实验室虚拟机。

```PowerShell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

使用以下命令调用脚本（ResourceGroup.ps1 是包含上述脚本的文件）： 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
如果使用 Azure 资源管理器模板创建实验室，请在资源管理器模板的实验室属性部分中使用 **vmCreationResourceGroupId** 属性，如以下示例中所示：

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "6e6f668f-992b-435c-bac3-d328b745cd25"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [设置实验室的策略](devtest-lab-get-started-with-lab-policies.md)
- [常见问题](devtest-lab-faq.md)
