---
title: 使用 Azure 门户将 Azure 网络安全组 (NSG) 移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板，通过 Azure 门户将 Azure 网络安全组从一个 Azure 区域移到另一个 Azure 区域。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: a22dc6dc0c4fc199d3f262b18aeeae5090a06dce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84689310"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>使用 Azure 门户将 Azure 网络安全组 (NSG) 移到另一个区域

在多种情况下，可能需要将现有的 NSG 从一个区域移到另一个区域。 例如，可能需要创建一个具有相同配置和安全规则的 NSG，以便进行测试。 还可能需要按照灾难恢复规划将 NSG 移到另一个区域。

Azure 安全组不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板来导出 NSG 的现有配置和安全规则。  然后，可以将资源暂存在另一区域，方法是：将 NSG 导出到模板，根据目标区域的情况修改参数，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 网络安全组位于要从其移动的 Azure 区域中。

- Azure 网络安全组不能在区域之间移动。  必须将新的 NSG 关联到目标区域中的资源。

- 若要导出 NSG 配置并部署模板，以便在另一区域创建 NSG，需要“网络参与者”角色或更高级别的角色。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、公共 IP 和虚拟网络。

- 验证 Azure 订阅是否允许在已使用的目标区域中创建 NSG。 请联系支持部门，启用所需配额。

- 确保订阅提供足够的资源，以便为此过程添加 NSG。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>准备并移动
以下步骤说明如何使用资源管理器模板准备网络安全组，以进行配置和安全规则的移动，并使用门户将 NSG 配置和安全规则移到目标区域。


### <a name="export-the-template-and-deploy-from-the-portal"></a>通过门户导出模板并进行部署

1. 登录到 [Azure 门户](https://portal.azure.com)  >  选择“资源组”。 
2. 找到包含源 NSG 的资源组并单击它。
3. 选择“设置” > “导出模板”。  
4. 在“导出模板”边栏选项卡中选择“部署”。  
5. 单击“模板” > “编辑参数”，在在线编辑器中打开 parameters.json 文件。   
6. 若要编辑 NSG 名称的参数，请更改 **parameters** 下的 **value** 属性：

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

7. 将编辑器中的源 NSG 值更改为目标 NSG 的所选名称。 请务必将名称括在引号中。

8.  在编辑器中单击“保存”。 

9.  单击“模板” > “编辑模板”，在在线编辑器中打开 template.json 文件。   

10. 若要编辑要将 NSG 配置和安全规则移到其中的目标区域，请在在线编辑器中更改 **resources** 下的 **location** 属性：

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

11. 若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不包含空格、**美国中部**  =  **centralus**的区域名称。

12. 也可选择更改模板中的其他参数，这些参数是可选的，具体取决于你的要求：

    * **安全规则** - 可以通过编辑的方式设置哪些规则能够部署到目标 NSG 中，只需在 **template.json** 文件的 **securityRules** 节中添加或删除规则即可：

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

      若要完成在目标 NSG 中添加或删除规则的操作，还需编辑 **template.json** 文件末尾的自定义规则类型，具体格式见下面的示例：

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

13. 在在线编辑器中单击“保存”。 

14. 单击“基本信息” > “订阅”，以选择要将目标 NSG 部署到的订阅。  

15. 单击“基本信息” > “资源组”，以选择要将目标 NSG 部署到的资源组。    可以单击“新建”来为目标 NSG 创建新的资源组。   确保该名称不同于现有 NSG 的源资源组名称。

16. 确认“基本信息” > “位置”是否设置为要将 NSG 部署到的目标位置。  

17. 在“设置”下，确认名称是否与先前在 parameters 编辑器中输入的名称相匹配。 

18. 选中“条款和条件”下的框。 

19. 单击“购买”按钮部署目标网络安全组。 

## <a name="discard"></a>弃用

若要丢弃目标 NSG，请删除包含目标 NSG 的资源组。  为此，请从门户上的仪表板中选择该资源组，然后选择概述页顶部的“删除”。 

## <a name="clean-up"></a>清理

若要提交更改并完成 NSG 的移动，请删除源 NSG 或资源组。 为此，请从门户上的仪表板中选择该网络安全组或资源组，然后选择每个页面顶部的“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们将 Azure 网络安全组从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
