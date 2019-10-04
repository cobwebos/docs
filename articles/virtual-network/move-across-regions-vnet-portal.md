---
title: 使用 Azure 门户将 Azure 虚拟网络移到另一个 Azure 区域。
description: 使用资源管理器模板和 Azure 门户将 Azure 虚拟网络从一个 Azure 区域移到另一个区域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: d6f417e53e7d7a1a242a0c0dc56c2356f78f5344
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828949"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>使用 Azure 门户将 Azure 虚拟网络移到另一个区域

将现有的 Azure 虚拟网络从一个区域移到另一个区域的方案有多种。 例如，你可能希望使用与现有虚拟网络相同的测试和可用性配置来创建虚拟网络。 或者，您可能希望将生产虚拟网络作为灾难恢复计划的一部分转移到另一个区域。

你可以使用 Azure 资源管理器模板来完成将虚拟网络移动到另一个区域。 为此，可将虚拟网络导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域。 有关资源管理器模板的详细信息，请参阅 [Quickstart：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>先决条件

- 确保你的虚拟网络位于要移动的 Azure 区域中。

- 若要导出虚拟网络并部署模板以在另一个区域中创建虚拟网络，需要具有网络参与者角色或更高版本。

- 虚拟网络对等互连不会重新创建，如果仍然存在于模板中，它们将会失败。 导出模板之前，必须删除任何虚拟网络对等节点。 然后，你可以在虚拟网络移动后重新建立它们。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组（Nsg）和公共 Ip。

- 验证 Azure 订阅是否允许在目标区域中创建虚拟网络。 若要启用所需的配额，请联系支持人员。

- 请确保订阅中有足够的资源，以支持为此过程添加虚拟网络。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-for-the-move"></a>准备移动
在本部分中，将使用资源管理器模板为移动准备虚拟网络。 然后，使用 Azure 门户将虚拟网络移动到目标区域。

若要导出虚拟网络并使用 Azure 门户部署目标虚拟网络，请执行以下操作：

1. 登录到[Azure 门户](https://portal.azure.com)，然后选择 "**资源组**"。
1. 找到包含源虚拟网络的资源组，然后选择它。
1. 选择 "**设置** > " "**导出模板**"。
1. 在 "**导出模板**" 窗格中，选择 "**部署**"。
1. 若要在在线编辑器中打开*parameters*文件，请选择 "**模板** > **编辑参数**"。
1. 若要编辑虚拟网络名称的参数，请在 "**参数**" 下更改 "**值**" 属性：

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

1. 在编辑器中，将编辑器中的 "源虚拟网络名称" 值更改为目标虚拟网络所需的名称。 请确保将该名称括在引号中。

1. 在编辑器中选择 "**保存**"。

1. 若要在在线编辑器中打开*模板 json*文件，请选择 "**模板** > **编辑模板**"。

1. 在 "联机" 编辑器中，若要编辑要将虚拟网络移动到的目标区域，请更改 "**资源**" 下的 "**位置**" 属性：

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

1. 若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是区域名称，没有空格（例如，**美国中部** = **centralus**）。

1. 可有可无还可以根据需要更改模板中的其他参数：

    * **地址空间**：在保存文件之前，可以通过修改**资源** > **addressSpace**部分并更改**addressPrefixes**属性，来更改虚拟网络的地址空间：

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

    * **子网**：可以通过更改模板的**子网**部分来更改或添加到子网名称和子网地址空间。 可以通过更改 "**名称**" 属性来更改子网的名称。 您可以通过更改**addressPrefix**属性来更改子网地址空间：

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

        若要更改*模板 json*文件中的地址前缀，请在以下两个位置中进行编辑：在上一节中的代码中，在以下代码的**type**节中进行编辑。 更改以下代码中的**addressPrefix**属性，以匹配上一部分代码中的**addressPrefix**属性。

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

1. 在联机编辑器中，选择 "**保存**"。

1. 若要选择将在其中部署目标虚拟网络的订阅，请选择 "**基础** > **订阅**"。

1. 若要选择将在其中部署目标虚拟网络的资源组，请选择 "**基础** > **资源组**"。 

    如果需要为目标虚拟网络创建新的资源组，请选择 "**新建**"。 请确保名称与现有虚拟网络中的源资源组名称不同。

1. 验证**基本** > **位置**是否设置为要在其中部署虚拟网络的目标位置。

1. 在 "**设置**" 下，验证名称是否与您之前在参数编辑器中输入的名称相匹配。

1. 选中 "**条款和条件**" 复选框。

1. 若要部署目标虚拟网络，请选择 "**购买**"。

## <a name="delete-the-target-virtual-network"></a>删除目标虚拟网络

若要放弃目标虚拟网络，请删除包含目标虚拟网络的资源组。 为此，请执行以下操作：
1. 在 Azure 门户 "仪表板上，选择资源组。
1. 在 "**概述**" 窗格的顶部，选择 "**删除**"。

## <a name="clean-up"></a>清理

若要提交更改并完成虚拟网络移动，请删除源虚拟网络或资源组。 为此，请执行以下操作：
1. 在 Azure 门户仪表板上，选择虚拟网络或资源组。
1. 在每个窗格的顶部，选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本教程中，通过使用 Azure 门户将 Azure 虚拟网络从一个区域移到另一个区域，然后清理不需要的源资源。 若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure 虚拟机移动到另一个区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
