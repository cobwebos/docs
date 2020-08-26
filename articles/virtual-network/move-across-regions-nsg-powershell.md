---
title: 使用 Azure PowerShell 将 Azure 网络安全组 (NSG) 移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板，通过 Azure PowerShell 将 Azure 网络安全组从一个 Azure 区域移到另一个 Azure 区域。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 04abc051cec8a6fb38ce6aa8f5347ae06cb8bd1d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84688443"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 将 Azure 网络安全组 (NSG) 移到另一个区域

在多种情况下，可能需要将现有的 NSG 从一个区域移到另一个区域。 例如，可能需要创建一个具有相同配置和安全规则的 NSG，以便进行测试。 还可能需要按照灾难恢复规划将 NSG 移到另一个区域。

Azure 安全组不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板来导出 NSG 的现有配置和安全规则。  然后，可以将资源暂存在另一区域，方法是：将 NSG 导出到模板，根据目标区域的情况修改参数，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>必备条件

- 请确保 Azure 网络安全组位于要从其移动的 Azure 区域中。

- Azure 网络安全组不能在区域之间移动。  必须将新的 NSG 关联到目标区域中的资源。

- 若要导出 NSG 配置并部署模板，以便在另一区域创建 NSG，需要“网络参与者”角色或更高级别的角色。
   
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、公共 IP 和虚拟网络。

- 验证 Azure 订阅是否允许在已使用的目标区域中创建 NSG。 请联系支持部门，启用所需配额。

- 确保订阅提供足够的资源，以便为此过程添加 NSG。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>准备并移动
以下步骤介绍了如何使用资源管理器模板准备网络安全组，以便进行配置和安全规则的移动，通过 Azure PowerShell 将 NSG 配置和安全规则移到目标区域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>通过脚本导出模板并进行部署

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 使用 [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0) 获取要移到目标区域的 NSG 的资源 ID，将其置于一个变量中：

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源 NSG 导出到执行 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 命令时所在的目录中的某个 .json 文件：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. 已下载的文件将根据从其导出了资源的资源组来命名。  找到通过命令导出的名为 \<resource-group-name>.json 的文件，在所选编辑器中将其打开：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要编辑 NSG 名称的参数，请将源 NSG 名称的属性 **defaultValue** 更改为目标 NSG 的名称，确保对名称使用引号：
    
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


6. 若要编辑要将 NSG 配置和安全规则移到其中的目标区域，请更改 **resources** 下的 **location** 属性：

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
  
7. 若要获取区域位置代码，可以通过运行以下命令来使用 Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 如果你选择，则还可以更改** \<resource-group-name> json**中的其他参数，并根据你的要求进行选择：

    * **安全规则**-你可以通过在**securityRules**部分中添加或删除规则，来编辑要部署到目标** \<resource-group-name> NSG 的规则**：

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

        若要完成添加或删除目标 NSG 中的规则，还必须按以下示例格式在** \<resource-group-name> json**文件末尾编辑自定义规则类型：

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

9. 保存 \<resource-group-name>.json 文件。

10. 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 在目标区域创建资源组，以便部署目标 NSG：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 使用 [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) 将编辑的 \<resource-group-name>.json 文件部署到在上一步创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域创建这些资源，请使用 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 和 [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>弃用 

部署以后，如果希望重新开始或弃用目标中的 NSG，请删除在目标中创建的资源组，系统就会删除已移动的 NSG。  若要删除资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

若要提交所做的更改并完成 NSG 的移动，以及删除源 NSG 或资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 或 [Remove-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>后续步骤

在本教程中，我们将 Azure 网络安全组从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
