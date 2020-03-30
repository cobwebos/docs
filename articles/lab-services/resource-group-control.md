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
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134531"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中为实验室虚拟机指定资源组

作为实验室所有者，你可以配置要在特定的资源组中创建的实验室虚拟机。 此功能在以下场景中很有用处：

- 订阅中的实验室创建的资源组较少。
- 让实验室在您配置的一组固定资源组中运行。
- 处理在 Azure 订阅中创建资源组所需的限制和批准。
- 将所有实验室资源整合到单个资源组中，以简化跟踪这些资源并应用[策略](../governance/policy/overview.md)来管理资源组级别的资源。

使用此功能，可以使用脚本为所有实验室 VM 在 Azure 订阅中指定新的资源组或现有资源组。 目前，Azure 开发人员测试实验室通过 API 支持此功能。

> [!NOTE]
> 在 DevTest 实验室中创建实验室时，所有订阅限制都适用。 将实验室视为订阅中的任何其他资源。 对于资源组，限制[为每个订阅 980 个资源组](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 

## <a name="use-azure-portal"></a>使用 Azure 门户
按照以下步骤为实验室中创建的所有 VM 指定资源组。 

1. 登录到 Azure[门户](https://portal.azure.com)。
2. 选择左侧导航菜单上**的所有服务**。 
3. 从列表中选择**开发人员测试实验室**。
4. 从实验室列表中，选择您的**实验室**。  
5. 在左侧菜单的 **"设置"** 部分中选择 **"配置"和"策略**"。 
6. 选择左侧菜单上的**实验室设置**。 
7. 选择**一个资源组中的所有虚拟机**。 
8. 在下拉列表中选择现有资源组（或）选择 **"创建新**"，输入资源组**的名称**，然后选择 **"确定**"。 

    ![为所有实验室 VM 选择资源组](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>使用 PowerShell 
下面的示例演示如何使用 PowerShell 脚本创建新资源组中的所有实验室虚拟机。

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

使用以下命令调用脚本。 ResourceGroup.ps1 是包含上述脚本的文件：

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
如果使用 Azure 资源管理器模板创建实验室，请使用模板的实验室属性部分中的**vmCreateResourceGroupId**属性，如以下示例所示：

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>为实验室 VM 配置资源组的 API
使用此 API 时，您具有以下作为实验室所有者的选项：

- 为所有虚拟机选择**实验室的资源组**。
- 为所有虚拟机选择实验室资源组以外的**现有资源组**。
- 输入所有虚拟机**的新资源组**名称。
- 继续使用现有行为，其中为实验室中的每个 VM 创建一个资源组。
 
此设置适用于在实验室中创建的新虚拟机。 在自己的资源组中创建的实验室中较旧的 VM 不受影响。 在实验室中创建的环境将继续保留在自己的资源组中。

如何使用此 API：
- 使用 API 版本**2018_10_15_preview**。
- 如果指定新的资源组，请确保对订阅中**的资源组具有写入权限**。 如果缺少写入权限，请在指定的资源组中创建新虚拟机将失败。
- 使用 API 时，请传入**完整的资源组 ID**。 例如：`/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`。 确保资源组与实验室处于同一订阅中。 


## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [设置实验室的策略](devtest-lab-get-started-with-lab-policies.md)
- [常见问题](devtest-lab-faq.md)
