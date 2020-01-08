---
title: 使用 Azure PowerShell 将 Azure 公共 IP 移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板将 Azure 公共 IP 从一个 Azure 区域移到另一个 Azure 区域，使用 Azure PowerShell。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75641509"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 将 Azure 公共 IP 移到另一个区域

在各种情况下，你想要将现有的 Azure 公共 Ip 从一个区域移到另一个区域。 例如，你可能想要使用相同的配置和 sku 来创建公共 IP 来进行测试。 在灾难恢复规划过程中，你可能还需要将公共 IP 移到另一个区域。

Azure 公共 Ip 是特定于区域的，不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板导出公共 IP 的现有配置。  然后，你可以在另一个区域中暂存资源，方法是将公共 IP 导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>必备组件

- 请确保 Azure 公共 IP 位于要移动的 Azure 区域中。

- 无法在区域之间移动 Azure 公共 Ip。  需要将新的公共 ip 关联到目标区域中的资源。

- 若要导出公共 IP 配置并部署模板以在另一个区域中创建公共 IP，你将需要网络参与者角色或更高版本。
   
- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组（Nsg）和虚拟网络。

- 验证 Azure 订阅是否允许在目标区域中创建使用的公共 Ip。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持为此进程添加公共 Ip。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>准备并移动
以下步骤说明如何使用资源管理器模板为配置移动准备公共 IP，并使用 Azure PowerShell 将公共 IP 配置移动到目标区域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>导出模板并从脚本部署

1. 通过[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登录到 Azure 订阅，并按照屏幕上的说明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 获取要移动到目标区域的公共 IP 的资源 ID，并使用[AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)将其放在变量中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 将源虚拟网络导出到 json 文件，以执行命令[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下载的文件将命名为从中导出资源的资源组。  找到从名为 **\<资源组-name >** 的命令中导出的文件，并在所选的编辑器中将其打开：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要编辑公共 IP 名称的参数，请将 "源公共 IP 名称" 的属性 "**默认**值" 更改为目标公共 ip 的名称，确保名称为引号：
    
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

6. 若要编辑将移动公共 IP 的目标区域，请在 "资源" 下更改 "**位置**" 属性：

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
  
7. 若要获取区域位置代码，可以通过运行以下命令使用 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 你还可以根据需要更改模板中的其他参数，并根据需要进行选择：

    * **Sku** -可以通过更改 **\<资源组名称 > json**文件中的 " **sku** " > **名称**"属性，将配置中的公共 IP 的 sku 从" 标准 "更改为" 基本 "或" 基本 "更改为" 标准 "。

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

    * **公共 IP 分配方法**和**空闲超时**-可以通过将**publicIPAllocationMethod**属性从**动态**改为**静态**或**静态**更改为**动态**，来更改模板中的这两个选项。 可以通过将**idleTimeoutInMinutes**属性更改为所需的量来更改空闲超时。  默认值为**4**：

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


9. 将 **\<的资源组名称保存 > json**文件。

10. 在目标区域中创建一个资源组，以使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目标公共 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)将已编辑的已编辑 **\<资源组名称 > json**文件部署到上一步骤中创建的资源组：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要验证是否已在目标区域中创建资源，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>弃用 

部署后，如果你想要在目标中重新开始或丢弃公共 ip，则删除在目标中创建的资源组，移动的公共 IP 将被删除。  若要删除资源组，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

若要提交更改并完成虚拟网络的移动，请删除源虚拟网络或资源组，并使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 公共 IP 从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
