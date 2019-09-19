---
title: 使用 Azure 门户将 Azure 内部负载均衡器移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板将 Azure 内部负载均衡器从一个 Azure 区域移到另一个 Azure 区域，使用 Azure 门户
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 2e8f1cd32bc0b57faf7b2365e100064be78a37a2
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106250"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>使用 Azure 门户将 Azure 内部负载均衡器移到另一个区域

在各种情况下，你想要将现有的内部负载均衡器从一个区域移到另一个区域。 例如，你可能想要创建具有相同配置的内部负载均衡器进行测试。 在灾难恢复规划过程中，你可能还需要将内部负载均衡器移到另一个区域。

Azure 内部负载均衡器不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板导出内部负载均衡器的现有配置和虚拟网络。  然后，你可以通过将负载均衡器和虚拟网络导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域，从而在另一个区域中暂存资源。  有关资源管理器和模板的详细信息，请[参阅快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>先决条件

- 确保 Azure 内部负载均衡器在要移动的 Azure 区域中。

- 无法在区域之间移动 Azure 内部负载均衡器。  需要将新的负载均衡器关联到目标区域中的资源。

- 若要导出内部负载均衡器配置并部署模板以在另一个区域中创建内部负载均衡器，你将需要网络参与者角色或更高版本。
   
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组、虚拟机和虚拟网络。

- 验证 Azure 订阅是否允许在使用的目标区域中创建内部负载均衡器。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持添加此进程的负载均衡器。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>准备并移动
以下步骤演示了如何使用资源管理器模板准备用于移动的内部负载均衡器，并使用 Azure 门户将内部负载均衡器配置移动到目标区域。  在此过程中，必须包含内部负载均衡器的虚拟网络配置，并且必须先完成此配置，然后才能移动内部负载均衡器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>导出虚拟网络模板并从 Azure 门户部署

1. 登录到[Azure 门户](http://portal.azure.com) > **资源组**。
2. 找到包含源虚拟网络的资源组，然后单击它。
3. 选择 >**设置** > ""**导出模板**"。
4. 在 "**导出模板**" 边栏选项卡中选择 "**部署**"。
5. 单击 "**模板** > " "**编辑参数**"，在联机编辑器中打开 "参数文件" **。**
6. 若要编辑虚拟网络名称的参数，请在 "**参数**" 下更改 "**值**" 属性：
    
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
7. 将编辑器中的 "源虚拟网络名称" 值更改为目标 VNET 所选的名称。 确保将名称括在引号中。

8. 在编辑器中单击 "**保存**"。

9. 单击 "**模板** > " "**编辑模板**"，在联机编辑器中打开**模板**文件。 
    
10. 若要编辑 VNET 将移动到的目标区域，请更改 "资源" 下的 "**位置**" 属性：

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
  
11. 若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不包含空格、**美国** = 中部**centralus**的区域名称。
    
12. 你还可以根据你的要求，更改**模板 json**文件中的其他参数：

    * **地址空间**-可以在保存之前更改 VNET 的地址空间，方法是修改**resources** > **addressSpace**部分，**并在 addressPrefixes 文件中**更改属性：

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

    * **子网**-子网名称和子网地址空间可以通过修改**模板 json**文件中的**子网**部分来更改或添加。 可以通过更改 "**名称**" 属性更改子网的名称。 可以通过更改**addressPrefix** **文件中的 "** 属性" 属性来更改子网地址空间：

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

         在**模板 json**文件中，若要更改地址前缀，必须在两个位置中进行编辑，上面列出的部分和下面列出的**类型**部分。  更改**addressPrefix**属性，使其与上面的属性匹配：

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

13. 在联机编辑器中单击 "**保存**"。

14. 单击 "**基本** > **订阅**" 以选择将在其中部署目标 VNET 的订阅。

15. 单击 "**基本** > **资源组**" 以选择将在其中部署目标 VNET 的资源组。  可以单击 "**新建**" 为目标 VNET 创建新的资源组。  确保该名称与现有 VNET 的源资源组不相同。 

16. 验证**基本** > **位置**是否设置为要在其中部署 VNET 的目标位置。

17. 在 "**设置**" 下验证名称是否与上述 "参数编辑器" 中输入的名称相匹配。

18. 选中 "**条款和条件**" 下面的框。

19. 单击 "**购买**" 按钮部署目标虚拟网络。

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>导出内部负载均衡器模板并从 Azure PowerShell 部署

1. 登录到[Azure 门户](http://portal.azure.com) > **资源组**。
2. 找到包含源内部负载均衡器的资源组，然后单击它。
3. 选择 >**设置** > ""**导出模板**"。
4. 在 "**导出模板**" 边栏选项卡中选择 "**部署**"。
5. 单击 "**模板** > " "**编辑参数**"，在联机编辑器中打开 "参数文件" **。**

6. 若要编辑内部负载均衡器名称的参数，请将源内部负载均衡器名称的属性**默认**值更改为目标内部负载均衡器的名称，并确保名称为引号：

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
 
6. 若要编辑上面移动的目标虚拟网络的值，你必须首先获取资源 ID，然后将其复制并粘贴到**参数 json**文件中。 获取 ID：
    
    1. 在另一个浏览器选项卡或窗口中登录到[Azure 门户](http://portal.azure.com) > **资源组**。
    2. 从上述步骤中找到包含已移动的虚拟网络的目标资源组，然后单击它。
    3. 选择 >**设置** > **属性**。
    4. 在右侧的边栏选项卡中，突出显示**资源 ID**并将其复制到剪贴板。  或者，可以单击**资源 ID**路径右侧的 "**复制到剪贴板**" 按钮。
    5. 将资源 ID 粘贴到 "**默认**值" 属性中，然后在其他浏览器窗口或选项卡中打开**编辑参数**编辑器：
   
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
    6. 在联机编辑器中单击 "**保存**"。

7. 单击 "**模板** > " "**编辑模板**"，在联机编辑器中打开**模板**文件。
8. 若要编辑将在其中移动内部负载均衡器配置的目标区域，请在模板的 "**资源**" 下更改 "**位置**" 属性 **。 json**文件：

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

9.  若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不包含空格、**美国** = 中部**centralus**的区域名称。
    
10. 你还可以根据需要更改模板中的其他参数，并根据需要进行选择：
    
    * **Sku** -可以通过更改**模板 json**文件中的 " **Sku** > **名称**" 属性，将配置中的内部负载均衡器的 sku 从标准版更改为基本版或基本版。

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
      有关基本和标准 sku 负载平衡器之间的差异的详细信息，请参阅[Azure 标准负载均衡器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **负载均衡规则**-你可以在配置中添加或删除负载均衡规则，**方法是在 loadBalancingRules 文件的**" " 部分中添加或删除条目：

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

    * **探测器**-可以通过在模板的 "**探测**" 部分中添加或删除条目，在配置中添加或删除负载均衡器的探测 **。**

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

    * **入站 nat 规则**-可以添加或删除负载均衡器的入站 nat 规则，方法**是在 loadbalancer.inboundnatrules 文件的**" " 部分中添加或删除条目：

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
        若要完成添加或删除入站 NAT 规则，该规则必须在模板的结尾处作为**类型**属性存在或删除 **。 json**文件：

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
    
12. 在联机编辑器中单击 "**保存**"。
    
13. 单击 "**基本** > **订阅**" 以选择将在其中部署目标内部负载均衡器的订阅。

15. 单击 "**基本** > **资源组**" 以选择将在其中部署目标负载均衡器的资源组。  你可以单击 "**新建**" 以创建目标内部负载均衡器的新资源组，或选择上面为虚拟网络创建的现有资源组。  确保名称与现有源内部负载均衡器的源资源组相同。 

16. 验证**基本** > **位置**是否设置为要在其中部署内部负载均衡器的目标位置。

17. 在 "**设置**" 下验证名称是否与上述 "参数编辑器" 中输入的名称相匹配。  验证是否为配置中的任何虚拟网络填充了资源 Id。

18. 选中 "**条款和条件**" 下面的框。

19. 单击 "**购买**" 按钮部署目标虚拟网络。

## <a name="discard"></a>弃用 

如果要丢弃目标虚拟网络和内部负载均衡器，请删除包含目标虚拟网络和内部负载均衡器的资源组。  为此，请在门户的 "仪表板" 中选择资源组，并选择 "概述" 页顶部的 "**删除**"。

## <a name="clean-up"></a>清理

若要提交更改并完成虚拟网络和内部负载均衡器的移动，请删除源虚拟网络和内部负载均衡器或资源组。 为此，请从门户中的仪表板中选择虚拟网络和内部负载均衡器或资源组，并在每个页面顶部选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 内部负载均衡器从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
