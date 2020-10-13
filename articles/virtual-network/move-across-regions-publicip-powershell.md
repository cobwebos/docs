---
title: 使用 Azure PowerShell 将 Azure 公共 IP 配置移动到另一个 Azure 区域
description: 使用 Azure 资源管理器模板可通过 Azure PowerShell 将 Azure 公共 IP 配置从一个 Azure 区域移到另一个区域。
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 4f72c22ee26375e025af7b3a391fdd45187e7041
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84703734"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 将 Azure 公共 IP 配置移动到另一个区域

在许多场景中，你希望将现有 Azure 公共 IP 区域从一个区域移动到另一个区域。 例如，可能需要创建一个具有相同配置和 SKU 的公共 IP，以便进行测试。 还可能需要按照灾难恢复规划将公共 IP 移到另一个区域。

Azure 公共 IP 特定于区域，不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板来导出公共 IP 的现有配置。  然后，可将资源暂存在另一区域，方法是：将公共 IP 导出到模板，根据目标区域的情况修改参数，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 公共 IP 位于要从中移动的 Azure 区域中。

- Azure 公共 IP 无法在区域之间移动。  必须将新的公共 IP 关联到目标区域中的资源。

- 若要导出公共 IP 配置并部署模板，以便在另一区域创建公共 IP，需要“网络参与者”角色或更高级别的角色。
   
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组 (NSG) 和虚拟网络。

- 验证 Azure 订阅是否允许在使用的目标区域中创建公司 IP。 请联系支持部门，启用所需配额。

- 确保订阅提供足够的资源，以便为此过程添加公共 IP。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>准备并移动
以下步骤演示如何使用资源管理器模板准备公共 IP，以便进行配置移动，并使用 Azure PowerShell 将公共 IP 配置移到目标区域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>通过脚本导出模板并进行部署

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 使用 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0) 获取要移到目标区域的公共 IP 的资源 ID，将其置于一个变量中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源虚拟网络导出到执行 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 命令时所在的目录中的某个 .json 文件：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 已下载的文件将根据从其导出了资源的资源组来命名。  找到通过命令导出的名为 \<resource-group-name>.json 的文件，在所选编辑器中将其打开：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要编辑公共 IP 名称的参数，请将源公共 IP 名称的属性 defaultValue 更改为目标公共 IP 的名称，确保对名称使用引号：
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. 若要编辑要将公共 IP 移到其中的目标区域，请更改 resources 下的 location 属性：

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
  
7. 若要获取区域位置代码，可以通过运行以下命令来使用 Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 也可选择更改模板中的其他参数，这些参数是可选的，具体取决于你的要求：

    * Sku - 可以在配置中将公共 IP 的 sku 从“标准”更改为“基本”或从“基本”更改为“标准”，只需在 \<resource-group-name>.json 文件中更改 sku >  name 属性即可：

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

         有关基本和标准 sku 公共 ip 之间的差异的详细信息，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * 公共 IP 分配方法和空闲超时 - 可以在模板中更改这两个选项，只需将 publicIPAllocationMethod 属性从 Dynamic 更改为 Static，或从 Static 更改为 Dynamic     即可。 若要更改空闲超时，可以将 idleTimeoutInMinutes 属性更改为所需时间。  默认值为 4：

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
         ```

        有关分配方法和空闲超时值的详细信息，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


9. 保存 \<resource-group-name>.json 文件。

10. 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 在目标区域创建资源组，以便部署目标公共 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用 [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) 将编辑的 \<resource-group-name>.json 文件部署到在上一步创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域创建这些资源，请使用 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 和 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>弃用 

部署以后，如果希望重新开始或弃用目标中的公共 IP，请删除在目标中创建的资源组，系统就会删除已移动的公共 IP。  若要删除资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

若要提交更改并完成虚拟网络的移动，以及删除源虚拟网络或资源组，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 或 [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>后续步骤

在本教程中，我们将 Azure 公共 IP 从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
