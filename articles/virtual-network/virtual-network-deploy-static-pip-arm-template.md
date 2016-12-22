---
title: "在模板中创建使用静态公共 IP 的 VM | Microsoft 文档"
description: "了解如何在模板中通过 Azure Resource Manager 创建使用静态公共 IP 地址的 VM。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: d551085a-c7ed-4ec6-b4c3-e9e1cebb774c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 536cb4cd7975283dd61c8c4f2fe1a707a735504e
ms.openlocfilehash: 7e05ab9c6ba1d23399d2ded63eb9b413c422f7bc


---
# <a name="create-a-vm-with-a-static-public-ip-using-a-template"></a>在模板中创建使用静态公共 IP 的 VM

> [!div class="op_single_selector"]
- [Azure 门户](virtual-network-deploy-static-pip-arm-portal.md)
- [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
- [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
- [模板](virtual-network-deploy-static-pip-arm-template.md)
- [PowerShell（经典）](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../resource-manager-deployment-model.md)。 本文介绍如何使用 Resource Manager 部署模型。Microsoft 建议对大多数新的部署使用该模型，而不是经典部署模型。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-address-resources-in-a-template-file"></a>模板文件中的公共 IP 地址资源
可以查看和下载[示例模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json)。

以下部分说明如何基于上述方案定义公共 IP 资源：

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('webVMSetting').pipName]",
  "location": "[variables('location')]",
  "properties": {
    "publicIPAllocationMethod": "Static"
  },
  "tags": {
    "displayName": "PublicIPAddress - Web"
  }
},
```

请注意 **publicIPAllocationMethod** 属性已设置为 *Static*。 此属性可以是 *Dynamic*（默认值），也可以是 *Static*。 将其设置为静态可确保分配的公共 IP 地址永不更改。

以下部分说明公共 IP 地址与网络接口的关联。

```json
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[variables('webVMSetting').nicName]",
    "location": "[variables('location')]",
    "tags": {
    "displayName": "NetworkInterface - Web"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
      "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
    ],
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
          "privateIPAllocationMethod": "Static",
          "privateIPAddress": "[variables('webVMSetting').ipAddress]",
          "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
          },
          "subnet": {
            "id": "[variables('frontEndSubnetRef')]"
          }
        }
      }
    ]
  }
},
```

请注意，**publicIPAddress** 属性指向名为 **variables('webVMSetting').pipName** 的资源的 **Id**。 该名称是上面所示的公共 IP 资源的名称。

最后，上述网络接口列在要创建的 VM 的 **networkProfile** 属性中。

```json
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }
```

## <a name="deploy-the-template-by-using-click-to-deploy"></a>通过单击部署方式部署模板

公共存储库中提供的示例模板采用包含用于生成上述方案的默认值的参数文件。 若要使用单击部署来部署此模板，请在[使用静态 PIP](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) 的 VM 模板的 Readme.md 文件中单击“部署到 Azure”。 如果需要，请替换默认参数值并输入空白参数的值。  按照门户中的说明使用静态公共 IP 地址创建虚拟机。

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 部署模板

若要使用 PowerShell 部署下载的模板，请执行以下步骤。

1. 如果从未用过 Azure PowerShell，请完成[如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 一文中的步骤。
2. 在 PowerShell 控制台中，根据需要运行 `New-AzureRmResourceGroup` cmdlet 创建新资源组。 如果已创建资源组，请转到步骤 3。

    ```powershell
    New-AzureRmResourceGroup -Name PIPTEST -Location westus
    ```

    预期输出：
   
        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. 在 PowerShell 控制台中，运行 `New-AzureRmResourceGroupDeployment` cmdlet 部署该模板。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
        -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
        -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json
    ```

    预期输出：
   
        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0
   
        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         
   
        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署模板
若要使用 Azure CLI 部署该模板，请完成以下步骤：

1. 如果从未用过 Azure CLI，请遵循[如何安装和配置 Azure PowerShell](../xplat-cli-install.md) 一文中的步骤安装并配置该程序。
2. 运行 `azure config mode` 命令切换到 Resource Manager 模式，如下所示。

    ```azurecli
    azure config mode arm
    ```

    上述命令的预期输出如下：

        info:    New mode is arm

3. 打开[参数文件](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json)，选择其内容，然后将其保存到计算机上的文件中。 在本示例中，参数已保存到名为 *parameters.json* 的文件中。 如果需要，请更改文件中的参数值，但建议至少将 adminPassword 参数的值更改为唯一的复杂密码。
4. 运行 `azure group deployment create` 命令，使用前面下载并修改的模板和参数文件部署新 VNet。 在以下命令中，请将 <path> 替换为文件保存到的目标路径。 

    ```azurecli
    azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json
    ```

    预期输出（列出使用的参数值）：

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/[Subscription ID]/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK




<!--HONumber=Nov16_HO3-->


