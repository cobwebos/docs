---
title: 使用 Azure PowerShell 将 Azure 内部负载均衡器移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板，通过 Azure PowerShell 将 Azure 内部负载均衡器从一个 Azure 区域移到另一个 Azure 区域
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644267"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>使用 PowerShell 将 Azure 内部负载均衡器移到另一个区域

在多种情况下，可能需要将现有的内部负载均衡器从一个区域移到另一个区域。 例如，你可能想要创建另一个采用相同配置的内部负载均衡器进行测试。 你还可能想要在灾难恢复规划过程中将内部负载均衡器移到另一个区域。

无法将 Azure 内部负载均衡器从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板导出内部负载均衡器的现有配置和虚拟网络。  然后，可将资源暂存在另一区域，方法是：将负载均衡器和虚拟网络导出到某个模板，根据目标区域修改参数，然后将该模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>必备条件

- 确保 Azure 内部负载均衡器位于要从中移动的 Azure 区域。

- 无法在区域之间移动 Azure 内部负载均衡器。  必须将新的负载均衡器关联到目标区域中的资源。

- 若要导出内部负载均衡器配置并部署模板，以便在另一区域创建外部负载均衡器，需有“网络参与者”角色或更高级别的角色。
   
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组、虚拟机和虚拟网络。

- 请验证 Azure 订阅是否允许在所用的目标区域中创建内部负载均衡器。 请联系支持部门，启用所需配额。

- 确保订阅提供足够的资源，以便为此过程添加公共负载均衡器。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>准备并移动
以下步骤说明如何使用资源管理器模板准备好要移动的内部负载均衡器，并使用 Azure PowerShell 将内部负载均衡器配置移到目标区域。  在此过程中，必须包括内部负载均衡器的虚拟网络配置，并且必须在移动内部负载均衡器之前完成此配置。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>从 Azure PowerShell 导出虚拟网络模板并进行部署

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  使用 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0) 获取要移到目标区域的虚拟网络的资源 ID，并将其置于一个变量中：

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源虚拟网络导出到执行 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 命令时所在的目录中的某个 .json 文件：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 已下载的文件将根据从其导出了资源的资源组来命名。  找到通过名为 **\<resource-group-name>.json** 的命令导出的文件，在所选编辑器中将其打开：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要编辑虚拟网络名称的参数，请将源虚拟网络名称的属性 **defaultValue** 更改为目标虚拟网络的名称（请务必将名称括在引号中）：
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  若要编辑要将 VNET 移到的目标区域，请更改 resources 下的 **location** 属性：

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
  
7. 若要获取区域位置代码，可以通过运行以下命令来使用 Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  还可以选择更改 **\<resource-group-name>.json** 文件中的其他参数，这些参数是可选的，具体取决于你的要求：

    * **地址空间** - 在保存之前，可以通过在  resource-group-name>.json >  文件中修改 **resources**  addressSpace **节并更改 \<addressPrefixes** 属性，来更改 VNET 的地址空间：

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

    * **子网** - 可以通过修改  resource-group-name>.json **文件的 \<subnets** 节来更改子网名称和子网地址空间。 可以通过更改 **name** 属性来更改子网的名称。 可以通过更改  resource-group-name>.json **文件中的 \<addressPrefix** 属性来更改子网地址空间：

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

         若要在 **\<resource-group-name>.json** 文件中更改地址前缀，必须在两处进行编辑：上面列出的节，以及下面列出的 **type** 节。  将 **addressPrefix** 属性更改为与上述某个节相匹配：

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

9.  保存 **\<resource-group-name>.json** 文件。

10. 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 在目标区域中为要部署的目标 VNET 创建资源组
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 使用 **New-AzResourceGroupDeployment\< 将编辑的** [resource-group-name>.json](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) 文件部署到在上一步创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. 若要验证是否已在目标区域创建这些资源，请使用 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 和 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>从 Azure PowerShell 导出内部负载均衡器模板并进行部署

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 使用 [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0) 获取要移到目标区域的内部负载均衡器的资源 ID，并将其置于一个变量中：

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源内部负载均衡器配置导出到执行 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 命令时所在的目录中的某个 .json 文件：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. 已下载的文件将根据从其导出了资源的资源组来命名。  找到通过名为 **\<resource-group-name>.json** 的命令导出的文件，在所选编辑器中将其打开：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要编辑内部负载均衡器名称的参数，请将源内部负载均衡器名称的属性 **defaultValue** 更改为目标内部负载均衡器的名称（请务必将名称括在引号中）：

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. 若要编辑上面已移动的目标虚拟网络的值，必须先获取资源 ID，然后将其复制并粘贴到 **\<resource-group-name>.json** 文件中。  若要获取 ID，请使用 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    键入变量并按 Enter 显示资源 ID。  突出显示 ID 路径，并将其复制到剪贴板：

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  在 **\<resource-group-name>.json** 文件中，粘贴变量中的 **Resource ID**，并用它替换目标虚拟网络 ID 的第二个参数中的 **defaultValue**（请务必将路径括在引号中）：
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. 若要编辑内部负载均衡器配置将要移到的目标区域，请更改  resource-group-name>.json**文件中**resources **下的 \<location** 属性：

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. 若要获取区域位置代码，可以通过运行以下命令来使用 Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. 也可选择更改模板中的其他参数，这些参数是可选的，具体取决于你的要求：
    
    * **SKU** - 可以在配置中将内部负载均衡器的 SKU 从 standard 更改为 basic 或者从 basic 更改为 standard，只需在  resource-group-name>.json >  文件中更改 **sku** **\<name** 属性即可：

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      若要详细了解基本和标准 sku 负载均衡器之间的区别，请参阅 [Azure 标准负载均衡器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **负载均衡规则** - 可以通过在  resource-group-name>.json **文件的 \<loadBalancingRules** 节中添加或删除条目，在配置中添加或删除负载均衡规则：

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       有关负载均衡规则的详细信息，请参阅[什么是 Azure 负载均衡器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

    * **探测** - 可以通过在  resource-group-name>.json **文件的 \<probes** 节中添加或删除条目，在配置中添加或删除负载均衡器的探测：

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       有关 Azure 负载均衡器运行状况探测的详细信息，请参阅[负载均衡器运行状况探测](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **入站 NAT 规则** - 可以通过在  resource-group-name>.json **文件的 \<inboundNatRules** 节中添加或删除条目，来添加或删除负载均衡器的入站 NAT 规则：

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        若要完成某个入站 NAT 规则的添加或删除，该规则必须作为 **type** 属性出现在 **\<resource-group-name>.json** 文件的末尾，或者已被删除：

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        有关入站 NAT 规则的详细信息，请参阅[什么是 Azure 负载均衡器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。
    
13. 保存 **\<resource-group-name>.json** 文件。
    
10. 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 在目标区域为要部署的目标内部负载均衡器创建资源组。 在此过程中，也可以重复使用上述现有资源组：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用 **New-AzResourceGroupDeployment\< 将编辑的** [resource-group-name>.json](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) 文件部署到在上一步创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域创建这些资源，请使用 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 和 [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>弃用 

部署后，如果你想要重新开始部署或丢弃目标中的虚拟网络和负载均衡器，请删除在目标中创建的资源组，这样就会删除已移动的虚拟网络和负载均衡器。  若要删除资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>清除

若要提交所做的更改并完成 NSG 的移动，并删除源 NSG 或资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 或 [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) 和 [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>后续步骤

在本教程中，你已将一个 Azure 内部负载均衡器从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
