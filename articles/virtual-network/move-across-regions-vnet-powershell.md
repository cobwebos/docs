---
title: 使用 Azure PowerShell 将 Azure 虚拟网络移到另一个 Azure 区域
description: 使用资源管理器模板和 Azure PowerShell 将 Azure 虚拟网络从一个 Azure 区域移到另一个区域。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: e13164c3ec6049a8ae3954528a02d20e313dd883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711453"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>使用 Azure PowerShell 将 Azure 虚拟网络移到另一个区域

将现有 Azure 虚拟网络从一个区域移到另一个区域的方案有多种。 例如，可以创建一个与现有虚拟网络使用相同测试和可用性配置的虚拟网络。 或者，可以将生产虚拟网络移到另一个区域，作为灾难恢复计划的一部分。

可以使用 Azure 资源管理器模板来完成将虚拟网络移到另一个区域的过程。 为此，可将虚拟网络导出到某个模板，根据目标区域修改参数，然后将该模板部署到新区域。 有关资源管理器模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>先决条件

- 确保你的虚拟网络位于要从中进行移动的 Azure 区域中。

- 若要导出虚拟网络并部署模板，以便在另一区域创建虚拟网络，需要“网络参与者”角色或更高级别的角色。

- 不会重新创建虚拟网络对等互连，如果它们仍存在于模板中，它们将会失败。 在导出模板之前，必须删除所有虚拟网络对等互连。 然后，可以在移动虚拟网络后重新建立对等互连。
    
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组 (NSG) 和公共 IP。

- 验证 Azure 订阅是否允许在目标区域中创建虚拟网络。 若要启用所需配额，请联系支持部门。

- 确保订阅提供足够的资源，以支持在此过程中添加虚拟网络。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-for-the-move"></a>准备移动
在本部分，你将使用资源管理器模板来准备好要移动的虚拟网络。 然后，使用 Azure PowerShell 命令将虚拟网络移到目标区域。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 PowerShell 导出虚拟网络并部署目标虚拟网络，请执行以下操作：

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 使用 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0) 获取要移到目标区域的虚拟网络的资源 ID，然后将其置于一个变量中：

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. 将源虚拟网络导出到执行 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 命令时所在的目录中的某个 .json 文件：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. 下载文件的名称与从中导出资源的资源组的名称相同。 找到与命令一起导出的* \<resource-group-name> json*文件，然后在编辑器中将其打开：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. 若要编辑虚拟网络名称的参数，请将源虚拟网络名称的 **defaultValue** 属性更改为目标虚拟网络的名称。 请务必将名称括在引号中。
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. 若要编辑要将虚拟网络移到的目标区域，请更改 resources 下的 **location** 属性：

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
  
1. 若要获取区域位置代码，可以通过运行以下命令来使用 Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. 可有可无你还可以根据需要更改* \<resource-group-name> json*文件中的其他参数：

    * **地址空间**：在保存该文件之前，可以更改虚拟网络的地址空间，方法是修改 **resources** > **addressSpace** 节并更改 **addressPrefixes** 属性：

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

    * **子网**：可以通过更改该文件的 **subnets** 节来更改子网名称和子网地址空间或在其中添加内容。 可以通过更改 **name** 属性来更改子网名称。 可以通过更改 **addressPrefix** 属性来更改子网地址空间：

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

        若要在更改地址前缀，请编辑该文件中的两个位置：前一节中的代码，以及以下代码的 **type** 节。 更改以下代码中的 **addressPrefix** 属性，使之与前一节的代码中的 **addressPrefix** 属性相匹配。

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

1. 保存 \<resource-group-name>.json 文件。

1. 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 在目标区域中为要部署的目标虚拟网络创建资源组：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. 使用[AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)将编辑后的* \<resource-group-name> json*文件部署到你在上一步中创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. 若要验证是否已在目标区域创建这些资源，请使用 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 和 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>删除虚拟网络或资源组 

部署虚拟网络后，若要重新开始部署或丢弃目标区域中的虚拟网络，请删除在目标区域中创建的资源组，这样就会删除已移动的虚拟网络。 

若要删除资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>清理

若要提交更改并完成虚拟网络的移动，请执行以下操作之一：

* 使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 删除资源组：

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* 使用 [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) 删除源虚拟网络：  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 PowerShell 将虚拟网络从一个区域移到了另一个区域，然后清理了不再需要的源资源。 若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：

- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure 虚拟机移到另一个区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
