---
title: 使用 Azure 门户将 Azure 公共 IP 移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板将 Azure 公共 IP 从一个 Azure 区域移到另一个 Azure 区域，使用 Azure 门户。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 2610afe9df06d28f2b75bd0023f7ec5a3fe9e56c
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219222"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>使用 Azure 门户将 Azure 公共 IP 移到另一个区域

在各种情况下，你想要将现有的 Azure 公共 Ip 从一个区域移到另一个区域。 例如，你可能想要使用相同的配置和 sku 来创建公共 IP 来进行测试。 在灾难恢复规划过程中，你可能还需要将公共 IP 移到另一个区域。

Azure 公共 Ip 是特定于区域的，不能从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板导出公共 IP 的现有配置。  然后，你可以在另一个区域中暂存资源，方法是将公共 IP 导出到模板，修改参数以匹配目标区域，然后将模板部署到新区域。  有关资源管理器和模板的详细信息，请[参阅快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>先决条件

- 请确保 Azure 公共 IP 位于要移动的 Azure 区域中。

- 无法在区域之间移动 Azure 公共 Ip。  需要将新的公共 ip 关联到目标区域中的资源。

- 若要导出公共 IP 配置并部署模板以在另一个区域中创建公共 IP，你将需要网络参与者角色或更高版本。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组（Nsg）和虚拟网络。

- 验证 Azure 订阅是否允许在目标区域中创建使用的公共 Ip。 请联系支持部门，启用所需配额。

- 请确保订阅中有足够的资源，以支持为此进程添加公共 Ip。  请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>准备并移动
以下步骤说明如何使用资源管理器模板为配置移动准备公共 IP，并使用 Azure 门户将公共 IP 配置移动到目标区域。

### <a name="export-the-template-and-deploy-from-a-script"></a>导出模板并从脚本部署

1. 登录到[Azure 门户](https://portal.azure.com) > **资源组**。
2. 找到包含源公共 IP 的资源组，然后单击该资源组。
3. 选择 >**设置** > ""**导出模板**"。
4. 在 "**导出模板**" 边栏选项卡中选择 "**部署**"。
5. 单击 "**模板** > " "**编辑参数**"，在联机编辑器中打开 "参数文件" **。**
8. 若要编辑公共 ip 名称的参数，请将 "**参数** > "**值**下的属性从 "源公共 ip 名称" 更改为目标公共 ip 的名称，确保名称位于引号中：

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
8.  在编辑器中单击 "**保存**"。

9.  单击 "**模板** > " "**编辑模板**"，在联机编辑器中打开**模板**文件。

10. 若要编辑将移动公共 IP 的目标区域，请在 "**资源**" 下更改 "**位置**" 属性：

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

11. 若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不包含空格、**美国** = 中部**centralus**的区域名称。

12. 你还可以根据需要更改模板中的其他参数，并根据需要进行选择：

    * **Sku** -可以通过更改**模板 json**文件中的**Sku** > **名称**属性，将配置中的公共 IP 的 sku 从标准更改为基本或基本到标准。

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

        有关基本和标准 sku 公共 ip 之间的差异的详细信息，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)：

    * **公共 IP 分配方法**和**空闲超时**-可以通过将**publicIPAllocationMethod**属性从**动态**改为**静态**或**静态**更改为动态，来更改模板中的这两个选项. 可以通过将**idleTimeoutInMinutes**属性更改为所需的量来更改空闲超时。  默认值为**4**：

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

        有关分配方法和空闲超时值的详细信息，请参阅[创建、更改或删除公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


13. 在联机编辑器中单击 "**保存**"。

14. 单击 "**基本** > **订阅**" 以选择将在其中部署目标公共 IP 的订阅。

15. 单击 "**基本** > **资源组**" 以选择将在其中部署目标公共 IP 的资源组。  可以单击 "**新建**" 为目标公共 IP 创建新的资源组。  确保名称与现有源公共 IP 的源资源组不同。

16. 验证**基本** > **位置**是否设置为要在其中部署公共 IP 的目标位置。

17. 在 "**设置**" 下验证名称是否与上述 "参数编辑器" 中输入的名称相匹配。

18. 选中 "**条款和条件**" 下面的框。

19. 单击 "**购买**" 按钮部署目标公共 IP。

## <a name="discard"></a>弃用

如果要丢弃目标公共 IP，请删除包含目标公共 IP 的资源组。  为此，请在门户的 "仪表板" 中选择资源组，并选择 "概述" 页顶部的 "**删除**"。

## <a name="clean-up"></a>清理

若要提交更改并完成公共 IP 的移动，请删除源公共 IP 或资源组。 为此，请在门户的 "仪表板" 中选择公共 IP 或资源组，并在每个页面顶部选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本教程中，已将 Azure 公共 IP 从一个区域移到另一个区域，并清理了源资源。  若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
