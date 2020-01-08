---
title: 使用 Azure PowerShell 将 Azure 网络安全组（NSG）移动到另一个 Azure 区域
description: 使用 Azure 资源管理器模板将 azure 网络安全组从一个 Azure 区域移到另一个 Azure 区域，使用 Azure PowerShell。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75641462"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 将 Azure 网络安全组（NSG）移动到另一个区域

在各种情况下，你想要将现有的 Nsg 从一个区域移到另一个区域。 例如，你可能想要使用相同的配置和安全规则来创建 NSG，以便进行测试。 在灾难恢复规划过程中，你可能还需要将 NSG 移动到另一个区域。

Azure 安全组不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板导出 NSG 的现有配置和安全规则。  然后，你可以通过将 NSG 导出到模板来暂存另一个区域中的资源，修改参数以匹配目标区域，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>必备组件

- 请确保 Azure 网络安全组在要移动的 Azure 区域中。

- Azure 网络安全组不能在区域之间移动。  需要将新的 NSG 关联到目标区域中的资源。

- 若要导出 NSG 配置并部署模板以在另一个区域中创建 NSG，需要 "网络参与者" 角色或更高版本。
   
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、公共 Ip 和虚拟网络。

- 验证 Azure 订阅是否允许在使用的目标区域中创建 Nsg。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持为此过程添加 Nsg。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>准备并移动
以下步骤演示了如何为使用资源管理器模板的配置和安全规则准备网络安全组，以及如何使用 Azure PowerShell 将 NSG 配置和安全规则移动到目标区域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>导出模板并从脚本部署

1. 通过[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登录到 Azure 订阅，并按照屏幕上的说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 获取要移动到目标区域的 NSG 的资源 ID，并使用[AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)将其放在变量中：

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源 NSG 导出到 json 文件，并将其导出到执行命令[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目录：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. 下载的文件将命名为从中导出资源的资源组。  找到从名为 **\<资源组-name >** 的命令中导出的文件，并在所选的编辑器中将其打开：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要编辑 NSG 名称的参数，请将源 NSG**的 name 属性值**更改为目标 NSG 的名称，并确保名称为引号：
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. 若要编辑 NSG 配置和安全规则将移动到的目标区域，请更改 "**资源**" 下的 "**位置**" 属性：

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
    ```
  
7. 若要获取区域位置代码，可以通过运行以下命令使用 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 你还可以根据你的要求，更改 **\<资源组名称 > json**中的其他参数：

    * **安全规则**-你可以通过在 **\<资源组 > json**文件中的 " **securityRules** " 部分中添加或删除规则，来编辑要部署到目标 NSG 的规则：

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
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
                        ]
            }  
            
        ```

        若要完成添加或删除目标 NSG 中的规则，还必须按以下示例格式在 **\<资源组名称 > json**文件的末尾编辑自定义规则类型：

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

9. 将 **\<的资源组名称保存 > json**文件。

10. 在目标区域中创建一个资源组，以使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目标 NSG：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 使用[AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)将已编辑的已编辑 **\<资源组名称 > json**文件部署到上一步骤中创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域中创建资源，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>弃用 

部署后，如果你想要重新开始或放弃目标中的 NSG，则删除在目标中创建的资源组，已移动的 NSG 将被删除。  若要删除资源组，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

若要提交更改并完成 NSG 的移动，请删除源 NSG 或资源组，使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 网络安全组从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
