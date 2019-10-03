---
title: 使用 Azure 门户将 Azure 网络安全组（NSG）移动到另一个 Azure 区域
description: 使用 Azure 资源管理器模板将 azure 网络安全组从一个 Azure 区域移到另一个 Azure 区域，使用 Azure 门户。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 839e608aa4bba26712ae5b0c160da40db279bbc9
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219187"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>使用 Azure 门户将 Azure 网络安全组（NSG）移动到另一个区域

在各种情况下，你想要将现有的 Nsg 从一个区域移到另一个区域。 例如，你可能想要使用相同的配置和安全规则来创建 NSG，以便进行测试。 在灾难恢复规划过程中，你可能还需要将 NSG 移动到另一个区域。

Azure 安全组不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板导出 NSG 的现有配置和安全规则。  然后，你可以通过将 NSG 导出到模板来暂存另一个区域中的资源，修改参数以匹配目标区域，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请[参阅快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 网络安全组在要移动的 Azure 区域中。

- Azure 网络安全组不能在区域之间移动。  需要将新的 NSG 关联到目标区域中的资源。

- 若要导出 NSG 配置并部署模板以在另一个区域中创建 NSG，需要 "网络参与者" 角色或更高版本。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、公共 Ip 和虚拟网络。

- 验证 Azure 订阅是否允许在使用的目标区域中创建 Nsg。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持为此过程添加 Nsg。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>准备并移动
以下步骤演示了如何为使用资源管理器模板的配置和安全规则准备网络安全组，以及如何使用门户将 NSG 配置和安全规则移动到目标区域。


### <a name="export-the-template-and-deploy-from-the-portal"></a>导出模板并从门户部署

1. 登录到[Azure 门户](https://portal.azure.com) > **资源组**。
2. 找到包含源 NSG 的资源组，然后单击该资源组。
3. 选择 >**设置** > ""**导出模板**"。
4. 在 "**导出模板**" 边栏选项卡中选择 "**部署**"。
5. 单击 "**模板** > " "**编辑参数**"，在联机编辑器中打开 "参数文件" **。**
6. 若要编辑 NSG 名称的参数，请在 "**参数**" 下更改**值**属性：

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. 在编辑器中，将 "源 NSG" 值更改为您选择的目标 NSG 的名称。 确保将名称括在引号中。

8.  在编辑器中单击 "**保存**"。

9.  单击 "**模板** > " "**编辑模板**"，在联机编辑器中打开**模板**文件。

10. 若要编辑 NSG 配置和安全规则将移动到的目标区域，请在 "联机编辑器" 中的 "**资源**" 下更改 "**位置**" 属性：

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. 若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不包含空格、**美国** = 中部**centralus**的区域名称。

12. 你还可以根据需要更改模板中的其他参数，并根据需要进行选择：

    * **安全规则**-可以通过在 NSG 文件的**securityRules**部分中添加或删除规则，来编辑部署到目标中的规则 **。**

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      若要完成添加或删除目标 NSG 中的规则，还必须在**模板**结尾文件的末尾编辑自定义规则类型，如下所示：

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. 在联机编辑器中单击 "**保存**"。

14. 单击 "**基础** > **订阅**" 以选择将在其中部署目标 NSG 的订阅。

15. 单击 "**基本** > **资源组**" 以选择将在其中部署目标 NSG 的资源组。  可以单击 "**新建**" 为目标 NSG 创建新的资源组。  确保名称与现有 NSG 的源资源组不同。

16. 验证**基本** > **位置**是否设置为要在其中部署 NSG 的目标位置。

17. 在 "**设置**" 下验证名称是否与上述 "参数编辑器" 中输入的名称相匹配。

18. 选中 "**条款和条件**" 下面的框。

19. 单击 "**购买**" 按钮部署目标网络安全组。

## <a name="discard"></a>弃用

如果希望放弃目标 NSG，请删除包含目标 NSG 的资源组。  为此，请在门户的 "仪表板" 中选择资源组，并选择 "概述" 页顶部的 "**删除**"。

## <a name="clean-up"></a>清理

若要提交更改并完成 NSG 的移动，请删除源 NSG 或资源组。 为此，请在门户的 "仪表板" 中选择网络安全组或资源组，并在每个页面顶部选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 网络安全组从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
