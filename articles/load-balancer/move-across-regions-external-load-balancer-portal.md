---
title: 使用 Azure 门户将 Azure 外部负载均衡器移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板通过使用 Azure 门户将外部负载均衡器从一个 Azure 区域移到另一个区域。
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 9358d99c66b3b8e3d6988b1881e51c11848ad97b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300637"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>使用 Azure 门户将外部负载均衡器移到另一个区域

在各种情况下，你想要将外部负载均衡器从一个区域移到另一个区域。 例如，你可能想要创建另一个具有相同配置的外部负载均衡器进行测试。 你还可能想要在灾难恢复规划过程中将外部负载均衡器移到另一个区域。

在文本意义上，不能将 Azure 外部负载均衡器从一个区域移到另一个区域。 但你可以使用 Azure 资源管理器模板来导出现有的配置和外部负载均衡器的公共 IP 地址。 然后，你可以通过将负载均衡器和公共 IP 导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域，从而在另一个区域中暂存资源。 有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 外部负载均衡器位于要移动的 Azure 区域中。

- 无法在区域之间移动 Azure 外部负载均衡器。 需要将新的负载均衡器关联到目标区域中的资源。

- 若要导出外部负载平衡器配置并部署模板以在其他区域中创建外部负载均衡器，需要为你分配 "网络参与者" 角色或更高版本。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组、公共 Ip 和虚拟网络。

- 验证 Azure 订阅是否允许在目标区域中创建外部负载均衡器。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持添加负载均衡器。 请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)。

## <a name="prepare-and-move"></a>准备并移动
下面的过程演示如何使用资源管理器模板为移动准备外部负载均衡器，并使用 Azure 门户将外部负载平衡器配置移动到目标区域。 必须首先导出外部负载均衡器的公共 IP 配置。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>导出公共 IP 模板并从门户部署公共 IP

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。
2. 找到包含源公共 IP 的资源组并选择它。
3. 选择 "**设置** > " "**导出模板**"。
4. 选择 "**导出模板**" 下的 "**部署**"。
5. 选择 "**模板** > " "**编辑参数**"，在联机编辑器中打开 "参数文件"。
8. 若要编辑公共 IP 名称的参数，请将源公共 IP 名称的**参数**中的**值**属性更改为目标公共 ip 的名称。 用引号将该名称引起来。

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    在编辑器中选择 "**保存**"。

9.  选择 "**模板** > " "**编辑模板**"，在联机编辑器中打开模板文件。

10. 若要编辑公共 IP 将移动到的目标区域，请更改 "**资源**" 下的 "**位置**" 属性：

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
  
    若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码为区域名称，不含空格。 例如，美国中部的代码为**centralus**。
    
12. 如果需要，还可以根据需要更改模板中的其他参数：

    * **SKU**。 可以通过在模板中的 " **SKU** " 下更改 "**名称**" 属性，在 "标准" 和 "基本" 或 "基本" 到 "标准" 中将公共 IP 的 SKU 更改为 "基本"。

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

        有关基本和标准 SKU 公共 Ip 之间的差异的信息，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公共 IP 分配方法**和**空闲超时**。 可以通过将**publicIPAllocationMethod**属性从**动态**更改为**静态**或从**静态**更改为**动态**，来更改公共 IP 分配方法。 可以通过将**idleTimeoutInMinutes**属性更改为所需的值来更改空闲超时值。 默认值为**4**。

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

        ```

        有关分配方法和空闲超时值的信息，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

 
13. 在联机编辑器中选择 "**保存**"。

14. 选择 "**基本** > " "**订阅**"，选择将在其中部署目标公共 IP 的订阅。

15. 选择 "**基本** > **资源组**" 以选择将在其中部署目标公共 IP 的资源组。 可以选择 "**新建**" 为目标公共 IP 创建新的资源组。 请确保名称与现有源公共 IP 的源资源组不同。

16. 验证 "**基本** > **位置**" 是否已设置为要在其中部署公共 IP 的目标位置。

17. 在 "**设置**" 下，验证名称是否与先前在参数编辑器中输入的名称相匹配。

18. 选中 "**条款和条件**" 复选框。

19. 选择 "**购买**" 以部署目标公共 IP。

20. 如果有另一个公共 IP 正在用于要移动的负载均衡器的出站 NAT，请重复上述步骤将第二个出站公共 IP 导出并部署到目标区域。

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>导出外部负载平衡器模板，并从 Azure 门户部署负载均衡器

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。
2. 找到包含源外部负载均衡器的资源组，然后选择它。
3. 选择 "**设置** > " "**导出模板**"。
4. 选择 "**导出模板**" 下的 "**部署**"。
5. 选择 "**模板** > " "**编辑参数**"，在联机编辑器中打开 "参数文件"。

5. 若要编辑外部负载平衡器名称的参数，请将源外部负载均衡器名称的**值**属性更改为目标外部负载均衡器的名称。 用引号将该名称引起来。

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  若要编辑在前面的步骤中移动的目标公共 IP 的值，你必须首先获取资源 ID，然后将其粘贴到参数 json 文件中。 获取 ID：

    1. 在另一个浏览器选项卡或窗口中，登录到[Azure 门户](https://portal.azure.com)并选择 "**资源组**"。
    2. 找到包含你在前面的步骤中移动的公共 IP 的目标资源组。 选择它。
    3. 选择 "**设置** > " "**属性**"。
    4. 在右侧的边栏选项卡中，突出显示**资源 ID**并将其复制到剪贴板。 或者，你可以选择 "**复制到剪贴板**"，然后选择 "**资源 ID**路径"。
    5. 将资源 ID 粘贴到在另一个浏览器窗口或选项卡中打开的 "**编辑参数**编辑器" 的 "**值**" 属性中：

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. 在联机编辑器中选择 "**保存**"。


7.  如果已为负载均衡器配置出站 NAT 和出站规则，则会在此文件中看到出站公共 IP 的外部 ID 的第三个条目。 在**目标区域**中重复前面的步骤，以获取出站公共 IP 的 ID。 将该 ID 粘贴到参数 json 文件中：

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  选择 "**模板** > " "**编辑模板**"，在联机编辑器中打开模板文件。
9.  若要编辑外部负载平衡器配置将移动到的目标区域，请在模板的 "**资源**" 下更改 "**位置**" 属性。 json 文件：

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

10. 若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码为区域名称，不含空格。 例如，美国中部的代码为**centralus**。

11. 如果需要，还可以根据需要更改模板中的其他参数：

    * **SKU**。 可以通过在模板中的 " **SKU** " 下更改 "**名称**" 属性，在 "标准" 和 "基本" 或 "基本" 到 "标准" 中将外部负载均衡器的 SKU 更改为 "标准"。

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
      有关基本和标准 SKU 负载平衡器之间的差异的信息，请参阅[Azure 标准负载均衡器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

    * **负载均衡规则**。 可以通过添加或删除 LoadBalancingRules 文件的 " " 部分中的条目，在配置中添加或删除负载均衡规则：

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

    * **探测器**。 通过在模板的 "**探测**" 部分中添加或删除项，可以在配置中添加或删除负载均衡器的探测。

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
       有关详细信息，请参阅[负载均衡器运行状况探测](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

    * **入站 NAT 规则**。 可以添加或删除负载均衡器的入站 NAT 规则，方法是添加或删除 Loadbalancer.inboundnatrules 文件的 " " 部分中的条目：

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
        若要完成添加或删除入站 NAT 规则，该规则必须在模板的结尾处作为**类型**属性存在或删除。 json 文件：

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
        有关入站 NAT 规则的信息，请参阅[什么是 Azure 负载均衡器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

    * **出站规则**。 可以通过编辑 OutboundRules 文件中的 " " 属性在配置中添加或删除出站规则：

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

         有关详细信息，请参阅[负载均衡器出站规则](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)。

12. 在联机编辑器中选择 "**保存**"。

13. 选择 "**基本** > " "**订阅**"，选择将在其中部署目标外部负载平衡器的订阅。

15. 选择 "**基本** > **资源组**" 以选择将在其中部署目标负载均衡器的资源组。 可以选择 "**新建**" 为目标外部负载均衡器创建新的资源组。 或者，你可以选择之前为公共 IP 创建的现有资源组。 请确保名称与现有源外部负载均衡器的源资源组相同。

16. 验证 "**基本** > **位置**" 是否已设置为要在其中部署外部负载平衡器的目标位置。

17. 在 "**设置**" 下，验证名称是否与先前在参数编辑器中输入的名称相匹配。 验证是否为配置中的任何公共 Ip 填充了资源 Id。

18. 选中 "**条款和条件**" 复选框。

19. 选择 "**购买**" 以部署目标公共 IP。

## <a name="discard"></a>弃用

如果要丢弃目标公共 IP 和外部负载均衡器，请删除包含它们的资源组。 为此，请在门户的 "仪表板" 中选择资源组，然后选择 "概述" 页顶部的 "**删除**"。

## <a name="clean-up"></a>清理

若要提交更改并完成公共 IP 和外部负载均衡器的移动，请删除源公共 IP 和外部负载均衡器或资源组。 为此，请在门户的 "仪表板" 中选择该资源组，然后在每个页面顶部选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 外部负载均衡器从一个区域移到另一个区域，并清理了源资源。 若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
