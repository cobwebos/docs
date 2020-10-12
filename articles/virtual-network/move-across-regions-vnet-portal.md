---
title: 使用 Azure 门户将 Azure 虚拟网络移到另一个 Azure 区域。
description: 使用资源管理器模板和 Azure 门户将 Azure 虚拟网络从一个 Azure 区域移到另一个区域。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: ed3da649ba65484a79b42ba5bb45431839e123d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711436"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>使用 Azure 门户将 Azure 虚拟网络移到另一个区域

将现有 Azure 虚拟网络从一个区域移到另一个区域的方案有多种。 例如，可以创建一个与现有虚拟网络使用相同测试和可用性配置的虚拟网络。 或者，可以将生产虚拟网络移到另一个区域，作为灾难恢复计划的一部分。

可以使用 Azure 资源管理器模板来完成将虚拟网络移到另一个区域的过程。 为此，可将虚拟网络导出到某个模板，根据目标区域修改参数，然后将该模板部署到新区域。 有关资源管理器模板的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>先决条件

- 确保你的虚拟网络位于要从中进行移动的 Azure 区域中。

- 若要导出虚拟网络并部署模板，以便在另一区域创建虚拟网络，需要“网络参与者”角色或更高级别的角色。

- 不会重新创建虚拟网络对等互连，如果它们仍存在于模板中，它们将会失败。 在导出模板之前，必须删除所有虚拟网络对等互连。 然后，可以在移动虚拟网络后重新建立对等互连。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组 (NSG) 和公共 IP。

- 验证 Azure 订阅是否允许在目标区域中创建虚拟网络。 若要启用所需配额，请联系支持部门。

- 确保订阅提供足够的资源，以支持在此过程中添加虚拟网络。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-for-the-move"></a>准备移动
在本部分，你将使用资源管理器模板来准备好要移动的虚拟网络。 然后，使用 Azure 门户将虚拟网络移到目标区域。

若要使用 Azure 门户导出虚拟网络并部署目标虚拟网络，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。 
1. 找到包含源虚拟网络的资源组并选择它。
1. 选择“设置” > “导出模板”。  
1. 在“导出模板”窗格中选择“部署”。  
1. 若要在在线编辑器中打开 *parameters.json* 文件，请选择“模板” > “编辑参数”。  
1. 若要编辑虚拟网络名称的参数，请更改 **parameters** 下的 **value** 属性：

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

1. 在编辑器中，将源虚拟网络名称值更改为所需的目标虚拟网络名称。 请务必将名称括在引号中。

1. 在编辑器中选择“保存”。 

1. 若要在在线编辑器中打开 *template.json* 文件，请选择“模板” > “编辑模板”。  

1. 在在线编辑器中，若要编辑要将虚拟网络移到的目标区域，请更改 **resources** 下的 **location** 属性：

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

1. 若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是区域名称，不包含空格 (例如， **Central US**  =  **centralus**) 。

1. （可选）还可以根据要求更改模板中的其他参数：

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

    * **子网**：可以通过更改该模板的 **subnets** 节来更改子网名称和子网地址空间或在其中添加内容。 可以通过更改 **name** 属性来更改子网名称。 可以通过更改 **addressPrefix** 属性来更改子网地址空间：

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

        若要在更改 *template.json* 文件中的地址前缀，请在两处进行编辑：前一节中的代码，以及以下代码的 **type** 节。 更改以下代码中的 **addressPrefix** 属性，使之与前一节的代码中的 **addressPrefix** 属性相匹配。

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

1. 在在线编辑器中选择“保存”。 

1. 若要选择要将目标虚拟网络部署到的订阅，请选择“基本信息” > “订阅”。  

1. 若要选择要将目标虚拟网络部署到的资源组，请选择“基本信息” > “资源组”。   

    如果需要为目标虚拟网络创建新的资源组，请选择“新建”。  确保该名称不同于现有虚拟网络中的源资源组名称。

1. 确认“基本信息” > “位置”是否设置为要将虚拟网络部署到的目标位置。  

1. 在“设置”下，确认名称是否与先前在 parameters 编辑器中输入的名称相匹配。 

1. 选中“条款和条件”复选框。 

1. 若要部署目标虚拟网络，请选择“购买”。 

## <a name="delete-the-target-virtual-network"></a>删除目标虚拟网络

若要丢弃目标虚拟网络，请删除包含目标虚拟网络的资源组。 为此，请执行以下操作：
1. 在 Azure 门户仪表板上，选择该资源组。
1. 在“概述”窗格的顶部，选择“删除”。  

## <a name="clean-up"></a>清理

若要提交更改并完成虚拟网络的移动，请删除源虚拟网络或资源组。 为此，请执行以下操作：
1. 在 Azure 门户仪表板上，选择该虚拟网络或资源组。
1. 在每个窗格的顶部，选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Azure 门户将 Azure 虚拟网络从一个区域移到了另一个区域，然后清理了不再需要的源资源。 若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure 虚拟机移到另一个区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
