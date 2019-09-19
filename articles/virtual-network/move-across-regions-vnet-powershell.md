---
title: 使用 Azure PowerShell 将 Azure 虚拟网络移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板将 Azure 虚拟网络从一个 Azure 区域移到另一个 Azure 区域，使用 Azure PowerShell。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fe4c3fe131eb763ef8875cced91ab3ae22abca08
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077654"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>使用 Azure Powershell 将 Azure 虚拟网络移到另一个区域

在各种情况下，你想要将现有的 Azure 虚拟网络（Vnet）从一个区域移到另一个区域。 例如，你可能想要使用相同的配置来创建虚拟网络，以便测试和现有虚拟网络的可用性。 你可能还希望将生产虚拟网络作为灾难恢复计划的一部分转移到另一个区域。

你可以使用 Azure 资源管理器模板来完成将虚拟网络移动到另一个区域。 为此，可将虚拟网络导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 虚拟网络位于要移动的 Azure 区域中。

- 若要导出虚拟网络并部署模板以在另一个区域中创建虚拟网络，你将需要网络参与者角色或更高版本。

- 虚拟网络对等互连不会重新创建，如果仍存在于模板中，它将失败。  在导出模板之前，必须删除所有虚拟网络对等方，然后在移动虚拟网络后重新建立对等节点。
    
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组（Nsg）和公共 Ip。

- 验证 Azure 订阅是否允许在使用的目标区域中创建虚拟网络。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持为此过程添加虚拟网络。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>准备并移动
以下步骤说明如何使用资源管理器模板为移动准备虚拟网络，并使用 Azure PowerShell 命令将虚拟网络移动到目标区域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>导出虚拟网络并通过 PowerShell 部署目标虚拟网络

1. 通过[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登录到 Azure 订阅，并按照屏幕上的说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 获取要移动到目标区域的虚拟网络的资源 ID，并使用[AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)将其放在变量中：

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源虚拟网络导出到 json 文件，以执行命令[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下载的文件将命名为从中导出资源的资源组。  找到从名为 **\<"资源组-名称 >** " 的命令中导出的文件，并在所选的编辑器中将其打开：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要编辑虚拟网络名称的参数，请将源虚拟网络名称的属性**默认**值更改为目标虚拟网络的名称，并确保名称为引号：
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  若要编辑 VNET 将移动到的目标区域，请更改 "资源" 下的 "**位置**" 属性：

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
  
7. 若要获取区域位置代码，可以通过运行以下命令使用 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  你还可以根据你的要求，更改 **\<资源组名称 > json**文件中的其他参数：

    * **地址空间**-可以在保存之前更改 VNET 的地址空间，方法是修改**resources** > **addressSpace**  **\<节并更改资源组-名称 >** 文件：

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

    * **子网**-子网名称和子网地址空间可以通过修改 **\<资源组-名称 >** 文件的**子网**部分来更改或添加。 可以通过更改 "**名称**" 属性更改子网的名称。 可以通过更改 **\<资源组 >** 文件中的**addressPrefix**属性来更改子网地址空间：

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

         在**资源组名称 > json 文件中，若要更改地址前缀，必须在两个位置中进行编辑，上面列出的部分和下面列出的 type 部分。 \<**  更改**addressPrefix**属性，使其与上面的属性匹配：

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

9.  将资源**组名称保存>json文件。\<**

10. 在目标区域中创建一个资源组，以使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目标 VNET
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 使用[AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)将已编辑 **\<的资源组名称 > json**文件部署到上一步骤中创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域中创建资源，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>弃用 

部署后，如果你想要在目标中重新开始或放弃虚拟网络，则删除在目标中创建的资源组，已移动的虚拟网络将被删除。  若要删除资源组，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清理

若要提交更改并完成虚拟网络的移动，请删除源虚拟网络或资源组，并使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 虚拟网络从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
