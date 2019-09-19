---
title: 使用 Azure PowerShell 将 Azure 外部负载均衡器移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板将 Azure 外部负载均衡器从一个 Azure 区域移到另一个 Azure 区域，使用 Azure PowerShell。
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: d8dfbf3f86a2233571a99c4ad832ef7bd3c3ed48
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077576"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 将 Azure 外部负载均衡器移到另一个区域

在各种情况下，你想要将现有的外部负载均衡器从一个区域移到另一个区域。 例如，你可能想要使用相同的测试配置来创建外部负载均衡器。 在灾难恢复规划过程中，你可能还需要将外部负载均衡器移到另一个区域。

不能将 Azure 外部负载均衡器从一个区域移到另一个区域。 不过，你可以使用 Azure 资源管理器模板来导出现有的配置和外部负载均衡器的公共 IP。  然后，你可以通过将负载均衡器和公共 IP 导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域，从而在另一个区域中暂存资源。  有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 外部负载均衡器位于要移动的 Azure 区域中。

- 无法在区域之间移动 Azure 外部负载均衡器。  需要将新的负载均衡器关联到目标区域中的资源。

- 若要导出外部负载平衡器配置并部署模板以在其他区域中创建外部负载均衡器，需要 "网络参与者" 角色或更高版本。
   
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组、公共 Ip 和虚拟网络。

- 验证 Azure 订阅是否允许在使用的目标区域中创建外部负载均衡器。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持添加此进程的负载均衡器。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>准备并移动
以下步骤演示了如何使用资源管理器模板为移动准备外部负载均衡器，并使用 Azure PowerShell 将外部负载均衡器配置移动到目标区域。  在此过程中，必须包含外部负载均衡器的公共 IP 配置，并且必须先完成此配置，然后才能移动外部负载均衡器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>导出公共 IP 模板并从 Azure PowerShell 部署

1. 通过[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登录到 Azure 订阅，并按照屏幕上的说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. 获取要移动到目标区域的公共 IP 的资源 ID，并使用[AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)将其放在变量中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源公共 IP 导出到 json 文件，以执行命令[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下载的文件将命名为从中导出资源的资源组。  找到从名为 **\<"资源组-名称 >** " 的命令中导出的文件，并在所选的编辑器中将其打开：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要编辑公共 IP 名称的参数，请将 "源公共 IP 名称" 的属性 "**默认**值" 更改为目标公共 ip 的名称，确保名称为引号：
    
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

6. 若要编辑将移动公共 IP 的目标区域，请在 "资源" 下更改 "**位置**" 属性：

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
  
7. 若要获取区域位置代码，可以通过运行以下命令使用 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 你还可以根据需要更改模板中的其他参数，并根据需要进行选择：

    * **Sku** -可以 > 通过**更改\<资源组名称 > json**文件中的**sku** **名称**属性，将配置中的公共 IP 的 sku 从标准更改为基本或基本到标准：

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

         有关基本和标准 sku 公共 ip 之间的差异的详细信息，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公共 IP 分配方法**和**空闲超时**-可以通过将**publicIPAllocationMethod**属性从**动态**改为**静态**或**静态**更改为动态，来更改模板中的这两个选项. 可以通过将**idleTimeoutInMinutes**属性更改为所需的量来更改空闲超时。  默认值为**4**：

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

        有关分配方法和空闲超时值的详细信息，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


9. 将资源**组名称保存>json文件。\<**

10. 在目标区域中创建一个资源组，以使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目标公共 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)将已编辑 **\<的资源组名称 > json**文件部署到上一步骤中创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域中创建资源，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>导出外部负载均衡器模板并从 Azure PowerShell 部署

1. 通过[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登录到 Azure 订阅，并按照屏幕上的说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 获取要移动到目标区域的外部负载平衡器的资源 ID，并使用[AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)将其放在变量中：

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源外部负载均衡器配置导出到 json 文件，以执行命令[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. 下载的文件将命名为从中导出资源的资源组。  找到从名为 **\<"资源组-名称 >** " 的命令中导出的文件，并在所选的编辑器中将其打开：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要编辑外部负载均衡器名称的参数，请将源外部负载均衡器名称的属性**defaultValue**更改为目标外部负载均衡器的名称，并确保名称为引号：

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

6.  若要编辑上面移动的目标公共 IP 的值，你必须首先获取资源 ID，然后将其复制并粘贴到 **\<资源组名称 > json**文件中。  若要获取 ID，请使用[AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    键入变量并按 enter 显示资源 ID。  突出显示 ID 路径，并将其复制到剪贴板：

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  在资源组 **名称 > json 文件中，从变量中粘贴资源 ID 以替代公共 IP 外部 ID 的第二个参数中的 defaultValue，确保将路径括在引号\<** 中：

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

8.  如果已为负载均衡器配置出站 NAT 和出站规则，则此文件中将显示第三个条目作为出站公共 IP 的外部 ID。  在**目标区域**中重复上述步骤，获取出站公共 iP 的 ID，并将该条目粘贴到 **\<资源组名称 > json**文件中：

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

10. 若要编辑外部负载平衡器配置将移动到的目标区域，请在 **\<资源组名称 > json**文件中的 "**资源**" 下更改 "**位置**" 属性：

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

11. 若要获取区域位置代码，可以通过运行以下命令使用 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. 你还可以根据需要更改模板中的其他参数，并根据需要进行选择：
    
    * **Sku** -你可以通过更改 >   **\<资源组-名称 > 中的 "Sku 名称" 属性，将配置中的外部负载均衡器的 sku 从标准版更改为基本版或基本版。** 文件：

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
      有关基本和标准 sku 负载平衡器之间的差异的详细信息，请参阅[Azure 标准负载均衡器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **负载均衡规则**-你可以在配置中添加或删除负载均衡规则，方法是将条目添加到**loadBalancingRules**  **\<文件的资源组 > 名称**，或从中删除条目：

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
       有关负载均衡规则的详细信息，请参阅[什么是 Azure 负载均衡器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **探测器**-可以在配置中添加或删除负载均衡器的探测，方法是在 **\<资源组名称 > json**文件的 "**探测**" 部分中添加或删除条目：

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

    * **入站 nat 规则**-可以添加或删除负载均衡器的入站 nat 规则，方法是在 **\<资源组 >** 的**loadbalancer.inboundnatrules**文件中添加或删除条目：

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
        若要完成添加或删除入站 NAT 规则，该规则必须在 **\<资源组名称 > json**文件的末尾提供或删除为**类型**属性：

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
        有关入站 NAT 规则的详细信息，请参阅[什么是 Azure 负载均衡器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **出站规则**-可以在配置中添加或删除出站规则，方法是在**outboundRules**文件中 **\<编辑资源组 > 名称**：

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

13. 将资源**组名称保存>json文件。\<**
    
10. 在目标区域中创建或资源组，以使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目标外部负载均衡器。 在此过程中，还可以重复使用上面的现有资源组：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)将已编辑 **\<的资源组名称 > json**文件部署到上一步骤中创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域中创建资源，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>弃用 

部署后，如果你想要在目标中重新开始或丢弃公共 IP 和负载均衡器，则删除在目标中创建的资源组，移动的公共 IP 和负载均衡器将被删除。  若要删除资源组，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>清理

若要提交更改并完成 NSG 的移动，请删除源 NSG 或资源组，使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) ，并[删除-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 网络安全组从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
