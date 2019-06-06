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
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 574cc0c41ce645c71302178afcf6e7deaec69d8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476081"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中为实验室虚拟机指定资源组

作为实验室所有者，你可以配置要在特定的资源组中创建的实验室虚拟机。 此功能在以下场景中很有用处：

- 订阅中的实验室创建的资源组较少。
- 具有一组固定的配置的资源组范围内使用实验室。
- 处理在 Azure 订阅中创建资源组所需的限制和批准。
- 整合在单个资源组以简化跟踪这些资源和应用内的所有实验室资源[策略](../governance/policy/overview.md)来管理资源组级别的资源。

借助此功能，可以使用脚本来为所有实验室 Vm 在 Azure 订阅中指定新的或现有资源组。 目前，Azure 开发测试实验室支持此功能通过一个 API。

> [!NOTE]
> 在开发测试实验室中创建实验室时，将应用所有订阅限制。 将实验室视为您的订阅中的任何其他资源。 如果资源组的限制[980 每个订阅的资源组](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)。 

## <a name="use-azure-portal"></a>使用 Azure 门户
按照这些步骤适用于在实验室中创建的所有 Vm 指定资源组。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**所有服务**在左侧导航菜单中。 
3. 选择**开发测试实验室**从列表中。
4. 从实验室列表中，选择你**实验室**。  
5. 选择**配置和策略**中**设置**在左侧菜单中的部分。 
6. 选择**实验室设置**在左侧菜单中。 
7. 选择**一个资源组中的所有虚拟机**。 
8. 选择现有资源组中的下拉列表 （或） 选择**新建**，输入**名称**资源组，然后选择**确定**。 

    ![选择适用于所有实验室 Vm 的资源组](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>使用 PowerShell 
下面的示例演示如何使用 PowerShell 脚本来创建新的资源组中的所有实验室虚拟机。

```powershell
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

使用以下命令来调用脚本。 ResourceGroup.ps1 是包含前面的脚本中的文件：

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
如果你使用 Azure 资源管理器模板创建实验室，使用**vmCreationResourceGroupId**模板，如下面的示例中所示的实验室属性部分中的属性：

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
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API 来配置实验室 Vm 的资源组
使用此 API 时作为实验室所有者具有以下选项：

- 选择**实验室的资源组**的所有虚拟机。
- 选择**现有的资源组**以外的所有虚拟机的实验室的资源组。
- 输入**新的资源组**名称的所有虚拟机。
- 继续使用现有的行为，为每个 VM 在实验室中创建资源组。
 
此设置适用于在实验室中创建的新虚拟机。 在实验室中创建其自己的资源组中较旧的 Vm 不会影响。 在实验室中创建的环境继续保留在其自己的资源组中。

如何使用此 API：
- 使用 API 版本**2018_10_15_preview**。
- 如果指定新的资源组，请确保您有**资源组的写权限**在订阅中。 如果您没有写入权限，在指定的资源组中创建新的虚拟机将失败。
- 使用 API 时，请传入**完整的资源组 ID**。 例如：`/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`。 请确保资源组是与实验室在同一订阅中。 


## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [设置实验室的策略](devtest-lab-get-started-with-lab-policies.md)
- [常见问题](devtest-lab-faq.md)
