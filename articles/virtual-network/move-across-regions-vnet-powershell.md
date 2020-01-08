---
title: 使用 Azure PowerShell 将 Azure 虚拟网络移到另一个 Azure 区域
description: 使用资源管理器模板并 Azure PowerShell，将 Azure 虚拟网络从一个 Azure 区域移到另一个区域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646704"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>使用 Azure PowerShell 将 Azure 虚拟网络移到另一个区域

将现有的 Azure 虚拟网络从一个区域移到另一个区域的方案有多种。 例如，你可能希望使用与现有虚拟网络相同的测试和可用性配置来创建虚拟网络。 或者，您可能希望将生产虚拟网络作为灾难恢复计划的一部分转移到另一个区域。

你可以使用 Azure 资源管理器模板来完成将虚拟网络移动到另一个区域。 为此，可将虚拟网络导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域。 有关资源管理器模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>必备组件

- 确保你的虚拟网络位于要移动的 Azure 区域中。

- 若要导出虚拟网络并部署模板以在另一个区域中创建虚拟网络，需要具有网络参与者角色或更高版本。

- 虚拟网络对等互连不会重新创建，如果仍然存在于模板中，它们将会失败。 导出模板之前，必须删除任何虚拟网络对等节点。 然后，你可以在虚拟网络移动后重新建立它们。
    
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组（Nsg）和公共 Ip。

- 验证 Azure 订阅是否允许在目标区域中创建虚拟网络。 若要启用所需的配额，请联系支持人员。

- 请确保订阅中有足够的资源，以支持为此过程添加虚拟网络。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-for-the-move"></a>准备移动
在本部分中，将使用资源管理器模板为移动准备虚拟网络。 然后，使用 Azure PowerShell 命令将虚拟网络移动到目标区域。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要导出虚拟网络并使用 PowerShell 部署目标虚拟网络，请执行以下操作：

1. 通过[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登录到 Azure 订阅，然后按照屏幕上的说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 获取要移动到目标区域的虚拟网络的资源 ID，然后使用[AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)将其放在变量中：

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. 将源虚拟网络导出到你在其中执行命令[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目录中的一个 json 文件：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. 下载的文件的名称与从中导出资源的资源组的名称相同。 找到 *\<的资源组名称 > json*文件，该文件是用命令导出的，然后在编辑器中打开它：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. 若要编辑虚拟网络名称的参数，请将源虚拟网络名称的**defaultValue**属性更改为目标虚拟网络的名称。 请确保将该名称括在引号中。
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. 若要编辑要将虚拟网络移动到的目标区域，请更改 "资源" 下的 "**位置**" 属性：

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. 若要获取区域位置代码，可以通过运行以下命令使用 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. 可有可无你还可以根据你的要求，更改 *\<资源组名称 > json*文件中的其他参数：

    * **地址空间**：在保存文件之前，可以通过修改**addressSpace**部分的 > **资源**并更改**addressPrefixes**属性来更改虚拟网络的地址空间：

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **子网**：可以通过更改文件的**子网**部分来更改或添加到子网名称和子网地址空间。 可以通过更改 "**名称**" 属性来更改子网的名称。 您可以通过更改**addressPrefix**属性来更改子网地址空间：

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        若要更改地址前缀，请在以下两个位置中编辑该文件：在上一节中的代码中，在以下代码的 "**类型**" 部分中。 更改以下代码中的**addressPrefix**属性，以匹配上一部分代码中的**addressPrefix**属性。

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. 将 *\<的资源组名称保存 > json*文件。

1. 在目标区域中创建一个资源组，以使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目标虚拟网络：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. 使用[AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)将已编辑 *\<资源组名称 > json*文件部署到在上一步中创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. 若要验证是否已在目标区域中创建资源，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>删除虚拟网络或资源组 

部署虚拟网络后，若要在目标区域中重新开始或放弃虚拟网络，请删除在目标区域中创建的资源组，移动的虚拟网络将被删除。 

若要删除资源组，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>清除

若要提交更改并完成虚拟网络移动，请执行以下操作之一：

* 使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)删除资源组：

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* 使用[AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)删除源虚拟网络：  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>后续步骤

在本教程中，使用 PowerShell 将虚拟网络从一个区域移到另一个区域，然后清理不需要的源资源。 若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：

- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure 虚拟机移动到另一个区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
