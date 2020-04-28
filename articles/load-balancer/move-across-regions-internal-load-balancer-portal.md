---
title: 使用 Azure 门户将 Azure 内部负载均衡器移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板，通过 Azure 门户将 Azure 内部负载均衡器从一个 Azure 区域移到另一个 Azure 区域
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638799"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>使用 Azure 门户将 Azure 内部负载均衡器移到另一个区域

在多种情况下，可能需要将现有的内部负载均衡器从一个区域移到另一个区域。 例如，你可能想要创建另一个采用相同配置的内部负载均衡器进行测试。 你还可能想要在灾难恢复规划过程中将内部负载均衡器移到另一个区域。

无法将 Azure 内部负载均衡器从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板导出内部负载均衡器的现有配置和虚拟网络。  然后，可将资源暂存在另一区域，方法是：将负载均衡器和虚拟网络导出到某个模板，根据目标区域修改参数，然后将该模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>先决条件

- 确保 Azure 内部负载均衡器位于要从中移动的 Azure 区域。

- 无法在区域之间移动 Azure 内部负载均衡器。  必须将新的负载均衡器关联到目标区域中的资源。

- 若要导出内部负载均衡器配置并部署模板，以便在另一区域创建外部负载均衡器，需有“网络参与者”角色或更高级别的角色。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组、虚拟机和虚拟网络。

- 请验证 Azure 订阅是否允许在所用的目标区域中创建内部负载均衡器。 请联系支持部门，启用所需配额。

- 确保订阅提供足够的资源，以便为此过程添加公共负载均衡器。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>准备并移动
以下步骤说明如何使用资源管理器模板准备好要移动的内部负载均衡器，并使用 Azure 门户将内部负载均衡器配置移到目标区域。  在此过程中，必须包括内部负载均衡器的虚拟网络配置，并且必须在移动内部负载均衡器之前完成此配置。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>从 Azure 门户导出虚拟网络模板并进行部署

1. 登录到 [Azure 门户](https://portal.azure.com)  >  选择“资源组”。 
2. 找到包含源虚拟网络的资源组并单击它。
3. 选择“设置” > “导出模板”。  
4. 在“导出模板”边栏选项卡中选择“部署”。  
5. 单击“模板” > “编辑参数”，在在线编辑器中打开 parameters.json 文件。   
6. 若要编辑虚拟网络名称的参数，请更改 **parameters** 下的 **value** 属性：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. 将编辑器中的源虚拟网络名称值更改为目标 VNET 的所选名称。 请务必将名称括在引号中。

8. 在编辑器中单击“保存”。 

9. 单击“模板” > “编辑模板”，在在线编辑器中打开 template.json 文件。   

10. 若要编辑要将 VNET 移到的目标区域，请更改 resources 下的 **location** 属性：

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

11. 若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不包含空格、**美国** = 中部**centralus**的区域名称。

12. 还可以选择更改 **template.json** 中的其他参数，这些参数是可选的，具体取决于你的要求：

    * **地址空间** - 在保存之前，可以通过在 **template.json** 文件中修改 **resources** > **addressSpace** 节并更改 **addressPrefixes** 属性，来更改 VNET 的地址空间：

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

    * **子网** - 可以通过修改 **template.json** 文件的 **subnets** 节来更改子网名称和子网地址空间。 可以通过更改 **name** 属性来更改子网的名称。 可以通过更改 **template.json** 文件中的 **addressPrefix** 属性来更改子网地址空间：

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

         若要在 **template.json** 文件中更改地址前缀，必须在两处进行编辑：上面列出的节，以及下面列出的 **type** 节。  将 **addressPrefix** 属性更改为与上述某个节相匹配：

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

13. 在在线编辑器中单击“保存”。 

14. 单击“基本信息” > “订阅”，以选择要将目标 VNET 部署到的订阅。  

15. 单击“基本信息” > “资源组”，以选择要将目标 VNET 部署到的资源组。    可以单击“新建”来为目标 VNET 创建新的资源组。   确保该名称不同于现有 VNET 的源资源组名称。

16. 确认“基本信息” > “位置”是否设置为要将 VNET 部署到的目标位置。  

17. 在“设置”下，确认名称是否与先前在 parameters 编辑器中输入的名称相匹配。 

18. 选中“条款和条件”下的框。 

19. 单击“购买”按钮部署目标虚拟网络。 

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>从 Azure PowerShell 导出内部负载均衡器模板并进行部署

1. 登录到 [Azure 门户](https://portal.azure.com)  >  选择“资源组”。 
2. 找到包含源内部负载均衡器的资源组并单击它。
3. 选择“设置” > “导出模板”。  
4. 在“导出模板”边栏选项卡中选择“部署”。  
5. 单击“模板” > “编辑参数”，在在线编辑器中打开 parameters.json 文件。   

6. 若要编辑内部负载均衡器名称的参数，请将源内部负载均衡器名称的属性 **defaultValue** 更改为目标内部负载均衡器的名称（请务必将名称括在引号中）：

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. 若要编辑上面已移动的目标虚拟网络的值，必须先获取资源 ID，然后将其复制并粘贴到 **parameters.json** 文件中。 若要获取 ID：

    1. 在另一个浏览器标签页或窗口中登录到 [Azure 门户](https://portal.azure.com)  >  选择“资源组”。 
    2. 找到通过上述步骤移动的虚拟网络所在的目标资源组，并单击它。
    3. 选择“设置” > “属性”。  
    4. 在右侧的边栏选项卡中，突出显示“资源 ID”并将其复制到剪贴板。   或者，可以单击“资源 ID”路径右侧的“复制到剪贴板”按钮。  
    5. 将资源 ID 粘贴到在另一个浏览器窗口或标签页中打开的“编辑参数”编辑器中的 **defaultValue** 属性： 

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. 在在线编辑器中单击“保存”。 

7. 单击“模板” > “编辑模板”，在在线编辑器中打开 template.json 文件。   
8. 若要编辑内部负载均衡器配置将要移到的目标区域，请更改 **template.json** 文件中 **resources** 下的 **location** 属性：

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

9.  若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不包含空格、**美国** = 中部**centralus**的区域名称。

10. 也可选择更改模板中的其他参数，这些参数是可选的，具体取决于你的要求：

    * **SKU** - 可以在配置中将内部负载均衡器的 SKU 从 standard 更改为 basic 或者从 basic 更改为 standard，只需在 **template.json** 文件中更改 **sku** > **name** 属性即可：

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

    * **负载均衡规则** - 可以通过在 **template.json** 文件的 **loadBalancingRules** 节中添加或删除条目，在配置中添加或删除负载均衡规则：

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

    * **探测** - 可以通过在 **template.json** 文件的 **probes** 节中添加或删除条目，在配置中添加或删除负载均衡器的探测：

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

    * **入站 NAT 规则** - 可以通过在 **template.json** 文件的 **inboundNatRules** 节中添加或删除条目，来添加或删除负载均衡器的入站 NAT 规则：

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
        若要完成某个入站 NAT 规则的添加或删除，该规则必须作为 **type** 属性出现在 **template.json** 文件的末尾，或者已被删除：

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

12. 在在线编辑器中单击“保存”。 

13. 单击“基本信息” > “订阅”，以选择要将目标内部负载均衡器部署到的订阅。  

15. 单击“基本信息” > “资源组”，以选择要将目标负载均衡器部署到的资源组。    可以单击“新建”来为目标内部负载均衡器创建新资源组，或选择前面为虚拟网络创建的现有资源组。   请确保所选名称与现有源内部负载均衡器的源资源组不同。

16. 确认“基本信息” > “位置”是否设置为要将内部负载均衡器部署到的目标位置。  

17. 在“设置”下，确认名称是否与先前在 parameters 编辑器中输入的名称相匹配。   确认是否为配置中的所有虚拟网络填充了资源 ID。

18. 选中“条款和条件”下的框。 

19. 单击“购买”按钮部署目标虚拟网络。 

## <a name="discard"></a>弃用

若要丢弃目标虚拟网络和内部负载均衡器，请删除包含目标虚拟网络和内部负载均衡器的资源组。  为此，请从门户上的仪表板中选择该资源组，然后选择概述页顶部的“删除”。 

## <a name="clean-up"></a>清理

若要提交更改并完成虚拟网络和内部负载均衡器的移动操作，请删除源虚拟网络和内部负载均衡器或资源组。 为此，请在门户上的仪表板中选择该虚拟网络和内部负载均衡器或资源组，然后选择每个页面顶部的“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已将一个 Azure 内部负载均衡器从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
