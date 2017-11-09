---
title: "创建网络安全组 - Azure Resource Manager 模板 | Microsoft 文档"
description: "了解如何使用  Azure Resource Manager 模板创建和部署网络安全组。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: f3e7385d-717c-44ff-be20-f9aa450aa99b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88f7e5b2144daee7bf1c8e7312ba98e6fa967899
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-network-security-groups-using-an-azure-resource-manager-template"></a>使用  Azure Resource Manager 模板创建网络安全组

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍 Resource Manager 部署模型。 还可[在经典部署模型中创建 NSG](virtual-networks-create-nsg-classic-ps.md)。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## <a name="nsg-resources-in-a-template-file"></a>模板文件中的 NSG 资源
可以查看和下载[示例模板](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/NSGs.json)。

下面的部分介绍了因方案而异的前端 NSG 定义。

```json
"apiVersion": "2015-06-15",
"type": "Microsoft.Network/networkSecurityGroups",
"name": "[parameters('frontEndNSGName')]",
"location": "[resourceGroup().location]",
"tags": {
  "displayName": "NSG - Front End"
},
"properties": {
  "securityRules": [
    {
      "name": "rdp-rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 100,
        "direction": "Inbound"
      }
    },
    {
      "name": "web-rule",
      "properties": {
        "description": "Allow WEB",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 101,
        "direction": "Inbound"
      }
    }
  ]
}
```
要将 NSG 关联到前端子网，需要更改模板中的子网定义，并对 NSG 使用引用 ID。

```json
"subnets": [
  {
    "name": "[parameters('frontEndSubnetName')]",
    "properties": {
      "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
      "networkSecurityGroup": {
      "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
      }
    }
  }, 
```

请注意，需要在模板中对后端 NSG 和后端子网执行相同的操作。

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>通过使用单击部署来部署 ARM 模板
公共存储库中提供的示例模板采用包含用于生成上述方案的默认值的参数文件。 要通过单击部署的方式来部署此模板，请访问[此链接](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)，单击“部署至 Azure”，如有必要，请替换默认参数值，并按照门户中的说明进行操作。

## <a name="deploy-the-arm-template-by-using-powershell"></a>使用 PowerShell 部署 ARM 模板
若要使用 PowerShell 部署下载的 ARM 模板，请执行以下步骤。

1. 如果从未使用过 Azure PowerShell，请按[如何安装和配置 Azure PowerShell](/powershell/azure/overview) 中的说明操作，安装和配置 Azure PowerShell。
2. 运行 **`New-AzureRmResourceGroup`** cmdlet 以使用模板创建资源组。

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location uswest `
    -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' `
    -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'
    ```

    预期输出：

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  
   
        Resources         :
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            sqlAvSet            Microsoft.Compute/availabilitySets       westus  
                            webAvSet            Microsoft.Compute/availabilitySets       westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            Web1                Microsoft.Compute/virtualMachines        westus  
                            Web2                Microsoft.Compute/virtualMachines        westus  
                            TestNICSQL1         Microsoft.Network/networkInterfaces      westus  
                            TestNICSQL2         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb1         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb2         Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            TestPIPSQL1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPSQL2         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb2         Microsoft.Network/publicIPAddresses      westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
   
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署 ARM 模板
若要使用 Azure CLI 部署 ARM 模板，请执行下列步骤。

1. 如果从未使用过 Azure CLI，请参阅[安装和配置 Azure CLI](../cli-install-nodejs.md)，并按照说明进行操作，直到选择 Azure 帐户和订阅。
2. 运行 **`azure config mode`** 命令切换到 Resource Manager 模式（如下所示）。

    ```azurecli
    azure config mode arm
    ```

    下面是上述命令的预期输出：

        info:    New mode is arm

3. 运行 **`azure group deployment create`** cmdlet，使用在前面下载并修改的模板和参数文件部署新 VNet。 在输出后显示的列表说明了所用的参数。

    ```azurecli
    azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' -e 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'
    ```

    预期输出：
   
        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Creating resource group TestRG
        info:    Created resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK
   
   * **-n（或 --name）**。 要创建的资源组的名称。
   * **-l（或 --location）**。 要创建资源组所在的 Azure 区域。
   * **-f（或 --template-file）**。 ARM 模板文件的路径。
   * **-e（或 --parameters-file）**。 ARM 参数文件的路径。

