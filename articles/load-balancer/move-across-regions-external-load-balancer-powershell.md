---
title: 使用 Azure PowerShell 将 Azure 外部负载均衡器移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板，通过 Azure PowerShell 将 Azure 外部负载均衡器从一个 Azure 区域移到另一个 Azure 区域。
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644811"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 将外部负载均衡器移到另一个 Azure 区域

在多种情况下，可能需要将现有的外部负载均衡器从一个区域移到另一个区域。 例如，你可能想要创建另一个采用相同配置的外部负载均衡器进行测试。 你还可能想要在灾难恢复规划过程中将外部负载均衡器移到另一个区域。

无法将 Azure 外部负载均衡器从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板来导出外部负载均衡器的现有配置和公共 IP。  然后，可将资源暂存在另一区域，方法是：将负载均衡器和公共 IP 导出到某个模板，根据目标区域修改参数，然后将该模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>必备条件

- 确保 Azure 外部负载均衡器位于要从中移动的 Azure 区域。

- 无法在区域之间移动 Azure 外部负载均衡器。  必须将新的负载均衡器关联到目标区域中的资源。

- 若要导出外部负载均衡器配置并部署模板，以便在另一区域创建外部负载均衡器，需有“网络参与者”角色或更高级别的角色。
   
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组、公共 IP 和虚拟网络。

- 请验证 Azure 订阅是否允许在所用的目标区域中创建外部负载均衡器。 请联系支持部门，启用所需配额。

- 确保订阅提供足够的资源，以便为此过程添加公共负载均衡器。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>准备并移动
以下步骤说明如何使用资源管理器模板准备好要移动的外部负载均衡器，并使用 Azure PowerShell 将外部负载均衡器配置移到目标区域。  在此过程中，必须包括外部负载均衡器的公共 IP 配置，并且必须在移动外部负载均衡器之前完成此配置。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>从 Azure PowerShell 导出公共 IP 模板并进行部署

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. 使用 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0) 获取要移到目标区域的公共 IP 的资源 ID，将其置于一个变量中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源公共 IP 导出到执行 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 命令时所在的目录中的某个 .json 文件：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 已下载的文件将根据从其导出了资源的资源组来命名。  找到通过名为 **\<resource-group-name>.json** 的命令导出的文件，在所选编辑器中将其打开：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要编辑公共 IP 名称的参数，请将源公共 IP 名称的属性 **defaultValue** 更改为目标公共 IP 的名称，确保对名称使用引号：
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. 若要编辑要将公共 IP 移到其中的目标区域，请更改 resources 下的 **location** 属性：

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. 若要获取区域位置代码，可以通过运行以下命令来使用 Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 也可选择更改模板中的其他参数，这些参数是可选的，具体取决于你的要求：

    * **Sku** - 可以在配置中将公共 IP 的 sku 从“标准”更改为“基本”或从“基本”更改为“标准”，只需在  resource-group-name>.json >  文件中更改 **sku** **\<name** 属性即可：

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         若要详细了解基本的和标准的 sku 公共 IP 的区别，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公共 IP 分配方法**和**空闲超时** - 可以在模板中更改这两个选项，只需将 **publicIPAllocationMethod** 属性从 **Dynamic** 更改为 **Static** 或从 **Static** 更改为 **Dynamic** 即可。 若要更改空闲超时，可以将 **idleTimeoutInMinutes** 属性更改为所需时间。  默认为 **4**：

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        若要详细了解分配方法和空闲超时值，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


9. 保存 **\<resource-group-name>.json** 文件。

10. 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 在目标区域创建资源组，以便部署目标公共 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用 **New-AzResourceGroupDeployment\< 将编辑的** [resource-group-name>.json](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) 文件部署到在上一步创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域创建这些资源，请使用 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 和 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>从 Azure PowerShell 导出外部负载均衡器模板并进行部署

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 使用 [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0) 获取要移到目标区域的外部负载均衡器的资源 ID，并将其置于一个变量中：

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源外部负载均衡器配置导出到执行 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 命令时所在的目录中的某个 .json 文件：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. 已下载的文件将根据从其导出了资源的资源组来命名。  找到通过名为 **\<resource-group-name>.json** 的命令导出的文件，在所选编辑器中将其打开：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要编辑外部负载均衡器名称的参数，请将源外部负载均衡器名称的属性 **defaultValue** 更改为目标外部负载均衡器的名称（请务必将名称括在引号中）：

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  若要编辑上面已移动的目标公共 IP 的值，必须先获取资源 ID，然后将其复制并粘贴到 **\<resource-group-name>.json** 文件中。  若要获取该 ID，请使用 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    键入变量并按 Enter 显示资源 ID。  突出显示 ID 路径，并将其复制到剪贴板：

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  在 **\<resource-group-name>.json** 文件中，粘贴变量中的 **Resource ID**，并用它替换公共 IP 外部 ID 的第二个参数中的 **defaultValue**（请务必将路径括在引号中）：

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  如果为该负载均衡器配置了出站 NAT 和出站规则，则此文件中会显示第三个条目，该条目对应于出站公共 IP 的外部 ID。  在**目标区域**中重复上述步骤以获取出站公共 IP 的 ID，并将该条目粘贴到 **\<resource-group-name>.json** 文件中：

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. 若要编辑外部负载均衡器配置将要移到的目标区域，请更改  resource-group-name>.json**文件中**resources **下的 \<location** 属性：

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
    
    * **SKU** - 可以在配置中将外部负载均衡器的 SKU 从 standard 更改为 basic 或者从 basic 更改为 standard，只需在  resource-group-name>.json >  文件中更改 **sku** **\<name** 属性即可：

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
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

    * **出站规则** - 可以通过编辑  resource-group-name>.json **文件中的 \<outboundRules** 属性，在配置中添加或删除出站规则：

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         有关出站规则的详细信息，请参阅[负载均衡器出站规则](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. 保存 **\<resource-group-name>.json** 文件。
    
10. 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 在目标区域为要部署的目标外部负载均衡器创建资源组。 在此过程中，也可以重复使用上述现有资源组：
    
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

部署后，如果你想要重新开始部署或丢弃目标中的公共 IP 和负载均衡器，请删除在目标中创建的资源组，这样就会删除已移动的公共 IP 和负载均衡器。  若要删除资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>清除

若要提交所做的更改并完成 NSG 的移动，并删除源 NSG 或资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 或 [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) 和 [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>后续步骤

在本教程中，我们将 Azure 网络安全组从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
