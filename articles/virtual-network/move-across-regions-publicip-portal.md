---
title: 将 Azure 公共 IP 配置移动到另一个 Azure 区域 Azure 门户
description: 使用模板将 Azure 公共 IP 配置从一个 Azure 区域移到另一个 Azure 区域，使用 Azure 门户。
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 23fe515ddfdecb9ef168dd662e3fa2d91ece688f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711470"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>使用 Azure 门户将 Azure 公共 IP 配置移动到另一个区域

在许多场景中，你希望将现有 Azure 公共 IP 区域从一个区域移动到另一个区域。 例如，可能需要创建一个具有相同配置和 SKU 的公共 IP，以便进行测试。 还可能需要按照灾难恢复规划将公共 IP 移到另一个区域。

Azure 公共 IP 特定于区域，不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板来导出公共 IP 的现有配置。  然后，可以将资源暂存在另一区域，方法是：将公共 IP 导出到模板，根据目标区域的情况修改参数，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 公共 IP 位于要从中移动的 Azure 区域中。

- Azure 公共 IP 无法在区域之间移动。  必须将新的公共 IP 关联到目标区域中的资源。

- 若要导出公共 IP 配置并部署模板，以便在另一区域创建公共 IP，需要“网络参与者”角色或更高级别的角色。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组 (NSG) 和虚拟网络。

- 验证 Azure 订阅是否允许在已使用的目标区域中创建公共 IP。 请联系支持部门，启用所需配额。

- 确保订阅提供足够的资源，以便为此过程添加公共 IP。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>准备并移动
以下步骤说明如何使用资源管理器模板准备公共 IP，以进行配置移动，并使用 Azure 门户将公共 IP 配置移到目标区域。

### <a name="export-the-template-and-deploy-from-a-script"></a>通过脚本导出模板并进行部署

1. 登录到 [Azure 门户](https://portal.azure.com)  >  选择“资源组”。 
2. 找到包含源公共 IP 的资源组并单击它。
3. 选择“设置” > “导出模板”。  
4. 在“导出模板”边栏选项卡中选择“部署”。  
5. 单击“模板” > “编辑参数”，在在线编辑器中打开 parameters.json 文件。   
8. 若要编辑公共 IP 名称的参数，请将源公共 IP 名称中 **parameters** > **value** 下的属性更改为目标公共 IP 的名称（请务必将名称括在引号中）：

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
8.  在编辑器中单击“保存”。 

9.  单击“模板” > “编辑模板”，在在线编辑器中打开 template.json 文件。   

10. 若要编辑要将公共 IP 移到其中的目标区域，请更改 **resources** 下的 **location** 属性：

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

11. 若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不包含空格、**美国中部**  =  **centralus**的区域名称。

12. 也可选择更改模板中的其他参数，这些参数是可选的，具体取决于你的要求：

    * **SKU** - 可以在配置中将公共 IP 的 SKU 从 standard 更改为 basic 或从 basic 更改为 standard，只需在 **template.json** 文件中更改 **sku** > **name** 属性即可：

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

        若要详细了解基本的和标准的 sku 公共 IP 的区别，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)：

    * **公共 IP 分配方法**和**空闲超时** - 可以在模板中更改这两个选项，只需将 **publicIPAllocationMethod** 属性从 **Dynamic** 更改为 **Static** 或从 **Static** 更改为 **Dynamic** 即可。 若要更改空闲超时，可以将 **idleTimeoutInMinutes** 属性更改为所需时间。  默认为 **4**：

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

        若要详细了解分配方法和空闲超时值，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


13. 在在线编辑器中单击“保存”。 

14. 单击“基本信息” > “订阅”，以选择要将目标公共 IP 部署到的订阅。  

15. 单击“基本信息” > “资源组”，以选择要将目标公共 IP 部署到的资源组。    可以单击“新建”来为目标公共 IP 创建新的资源组。   确保所选名称与现有源公共 IP 的源资源组不同。

16. 确认“基本信息” > “位置”是否设置为要将公共 IP 部署到的目标位置。  

17. 在“设置”下，确认名称是否与先前在 parameters 编辑器中输入的名称相匹配。 

18. 选中“条款和条件”下的框。 

19. 单击“购买”按钮部署目标公共 IP。 

## <a name="discard"></a>弃用

若要丢弃目标公共 IP，请删除包含目标公共 IP 的资源组。  为此，请从门户上的仪表板中选择该资源组，然后选择概述页顶部的“删除”。 

## <a name="clean-up"></a>清理

若要提交更改并完成公共 IP 的移动，请删除源公共 IP 或资源组。 为此，请从门户上的仪表板中选择该公共 IP 或资源组，然后选择每个页面顶部的“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们将 Azure 公共 IP 从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
