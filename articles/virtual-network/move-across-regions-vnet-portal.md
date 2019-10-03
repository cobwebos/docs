---
title: 使用 Azure 门户将 Azure 虚拟网络移到另一个 Azure 区域。
description: 使用 Azure 资源管理器模板将 Azure 虚拟网络从一个 Azure 区域移到另一个 Azure 区域，使用 Azure 门户。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: a09ce7b77dfcaa51e7c82f67a5d20000f3e22b61
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219993"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>使用 Azure 门户将 Azure 虚拟网络移到另一个区域

在各种情况下，你想要将现有的 Azure 虚拟网络（Vnet）从一个区域移到另一个区域。 例如，你可能想要使用相同的配置来创建虚拟网络，以便测试和现有虚拟网络的可用性。 你可能还希望将生产虚拟网络作为灾难恢复计划的一部分转移到另一个区域。

你可以使用 Azure 资源管理器模板来完成将虚拟网络移动到另一个区域。 为此，可将虚拟网络导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 虚拟网络位于要移动的 Azure 区域中。

- 若要导出虚拟网络并部署模板以在另一个区域中创建虚拟网络，你将需要网络参与者角色或更高版本。

- 虚拟网络对等互连不会重新创建，如果仍存在于模板中，它将失败。  在导出模板之前，必须删除所有虚拟网络对等方，然后在移动虚拟网络后重新建立对等节点。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组（Nsg）和公共 Ip。

- 验证 Azure 订阅是否允许在使用的目标区域中创建虚拟网络。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持为此过程添加虚拟网络。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>准备并移动
以下步骤演示了如何使用资源管理器模板为移动准备虚拟网络，以及使用门户将虚拟网络移动到目标区域。

### <a name="export-the-template-and-deploy-from-the-portal"></a>导出模板并从门户部署

1. 登录到[Azure 门户](https://portal.azure.com) > **资源组**。
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

8.  在编辑器中单击 "**保存**"。

9.  单击 "**模板** > " "**编辑模板**"，在联机编辑器中打开**模板**文件。

10. 若要编辑 VNET 将移动到的目标区域，请在 "联机" 编辑器中的 "**资源**" 下更改 "**位置**" 属性：

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

12. 你还可以根据需要更改模板中的其他参数，并根据需要进行选择：

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

## <a name="discard"></a>弃用

如果希望放弃目标虚拟网络，请删除包含目标虚拟网络的资源组。  为此，请在门户的 "仪表板" 中选择资源组，并选择 "概述" 页顶部的 "**删除**"。

## <a name="clean-up"></a>清理

若要提交更改并完成虚拟网络的移动，请删除源虚拟网络或资源组。 为此，请在门户的 "仪表板" 中选择虚拟网络或资源组，并在每个页面顶部选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 虚拟网络从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
