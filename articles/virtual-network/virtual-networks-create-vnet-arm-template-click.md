---
title: "创建虚拟网络 | Azure 资源管理器模板 | Microsoft 文档"
description: "了解如何使用 Azure 资源管理器模板创建虚拟网络。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81602766848a91331c8d811ea1c8ec3ffae44b96
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建虚拟网络

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有两个部署模型：Azure 资源管理器模型和经典模型。 Microsoft 建议通过 Resource Manager 部署模型创建资源。 若要详细了解这两个模型之间的差别，请阅读 [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md)（了解 Azure 部署模型）一文。
 
本文说明如何使用 Azure 资源管理器模板通过资源管理器部署模型创建 VNet。 此外，也可以使用其他工具通过 Resource Manager 创建 VNet，或者从以下列表中选择一个不同的选项，通过经典部署模型创建 VNet：

> [!div class="op_single_selector"]
- [门户](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [模板](virtual-networks-create-vnet-arm-template-click.md)
- [门户 经典）](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell（经典）](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI（经典）](virtual-networks-create-vnet-classic-cli.md)

将了解如何从 GitHub 下载并修改现有 ARM 模板，以及如何通过 GitHub、PowerShell 和 Azure CLI 部署该模板。

如果只是直接从 GitHub 部署 ARM 模板，而不进行任何更改，请跳到 [从 github 部署模板](#deploy-the-arm-template-by-using-click-to-deploy)。

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>下载 Azure 资源管理器模板并对其进行了解
可以从 GitHub 下载用于创建 VNet 和两个子网的现有模板，进行任何所需的更改，并重用该模板。 为此，请完成下列步骤：

1. 导航到 [示例模板页](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)。
2. 单击 **azuredeploy.json**，并单击 **RAW**。
3. 将该文件保存到计算机上的本地文件夹。
4. 如果熟悉模板，则跳到步骤 7。
5. 打开刚保存的文件，并查看 **parameters** 下第 5 行中的内容。 ARM 模板参数提供了在部署过程中可以填充的值的占位符。
   
   | 参数 | 说明 |
   | --- | --- |
   | **位置** |要在其中创建 VNet 的 Azure 区域 |
   | **vnetName** |新 VNet 的名称 |
   | **addressPrefix** |VNet 的地址空间，采用 CIDR 格式 |
   | **subnet1Name** |第一个 VNet 的名称 |
   | **subnet1Prefix** |第一个子网的 CIDR 块 |
   | **subnet2Name** |第二个 VNet 的名称 |
   | **subnet2Prefix** |第二个子网的 CIDR 块 |
   
   > [!IMPORTANT]
   > 在 GitHub 中维护的 Azure 资源管理器模板可能随着时间的推移发生变化。 请确保在使用该模板之前对其进行检查。
   > 
   > 
6. 查看 **resources** 下的内容，并注意以下项：
   
   * **type**。 模板创建的资源的类型。 在此实例中为 **Microsoft.Network/virtualNetworks**，表示 VNet。
   * **name**。 资源的名称。 请注意使用 **[parameters('vnetName')]**，这意味着在部署过程中由用户或参数文件作为输入提供该名称。
   * **properties**。 资源的属性列表。 此模板在 VNet 创建期间使用地址空间和子网属性。
7. 导航回 [示例模板页](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)。
8. 单击 **azuredeploy-paremeters.json**，并单击 **RAW**。
9. 将该文件保存到计算机上的本地文件夹。
10. 打开刚保存的文件并编辑参数的值。 使用以下值来部署方案中所述的 VNet：

    ```json
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. 保存文件。


## <a name="deploy-the-template-using-powershell"></a>使用 PowerShell 部署模板

通过使用 PowerShell 完成部署下载的模板的步骤：

1. 请按[如何安装和配置 Azure PowerShell](/powershell/azure/overview) 文章中的步骤安装和配置 Azure PowerShell。
2. 运行以下命令来创建新资源组：

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    该命令在*美国中部* Azure 区域中创建一个名为 *TestRG* 的资源组。 有关资源组的详细信息，请访问 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。

    预期输出：

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. 运行以下命令，使用前面下载并修改的模板和参数文件部署新 VNet：

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    预期输出：
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. 运行以下命令，查看新 VNet 的属性：

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    预期输出：

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>通过单击部署方式部署模板

可以重用上传到由 Microsoft 维护并对社区开放的 GitHub 存储库的预定义 Azure 资源管理器模板。 这些模板可直接从 GitHub 部署，或者将其下载并修改以满足需要。 若要部署用于创建包含两个子网的 VNet 的模板，请完成以下步骤：

1. 从浏览器导航到 [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)。
2. 向下滚动模板列表，并单击“101-vnet-two-subnets”。 选中 **README.md** 文件，如下所示。

    ![github 中的 READEME.md 文件](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. 单击 **“部署到 Azure”**。 如有必要，输入 Azure 登录凭据。 
4. 在“参数”边栏选项卡中，输入要用于创建新 VNet 的值，并单击“确定”。 下图显示了方案的值：
   
    ![ARM 模板参数](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. 单击“资源组”并选择要将 VNet 添加到的资源组，或者单击“新建”将 VNet 添加到新资源组。 下图显示了名为 **TestRG** 的新资源组的资源组设置：

    ![资源组](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. 如有必要，更改 VNet 的“订阅”和“位置”设置。
7. 如果不想看到该 VNet 作为**启动板**中的磁贴，请禁用“固定到启动板”。
8. 单击“**法律条款**”，阅读条款，并单击“**购买**”以表示同意。 
9. 单击“创建”以创建该 VNet  。
   
    ![在预览门户中提交部署磁贴](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. 部署完成后，在 Azure 门户中单击“**更多服务**”，在出现的筛选器框中键入“*虚拟网络*”，并单击“虚拟网络”查看“虚拟网络”边栏选项卡。 在边栏选项卡中，单击“*TestVNet*”。 在“*TestVNet*”边栏选项卡中，单击“**子网**”查看创建的子网，如下图中所示：
    
     ![在预览门户中创建 VNet](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>后续步骤

如何连接：

- 阅读文章[创建 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 或[创建 Linux VM](../virtual-machines/linux/quick-create-portal.md)，将虚拟机 (VM) 连接到虚拟网络。 不要根据这两篇文章的步骤创建 VNet 和子网，可以选择要将 VM 连接到的现有 VNet 和子网。
- 阅读[连接 VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) 一文，将一个虚拟网络连接到其他虚拟网络。
- 使用站点到站点虚拟专用网络 (VPN) 或 ExpressRoute 线路，将本地网络连接到虚拟网络。 阅读文章[使用站点到站点 VPN 将 VNet 连接到本地网络](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)和[将 VNet 链接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-arm.md)，了解相关操作方法。
