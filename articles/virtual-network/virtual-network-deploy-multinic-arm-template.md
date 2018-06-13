---
title: 创建具有多个 NIC 的 VM - Azure 资源管理器模板 | Microsoft 文档
description: 使用 Azure 资源管理器模板创建具有多个 NIC 的 VM。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31435128"
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>使用模板创建具有多个 NIC 的 VM
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../resource-manager-deployment-model.md)。  本文介绍如何使用 Resource Manager 部署模型。Microsoft 建议对大多数新的部署使用该模型，而不是[经典部署模型](virtual-network-deploy-multinic-classic-ps.md)。
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

以下步骤使用名为 *IaaSStory* 的资源组用于 WEB 服务器，将名为 *IaaSStory-BackEnd* 的资源组用于 DB 服务器。

## <a name="prerequisites"></a>先决条件
创建数据库服务器之前，需要先使用此方案的所有必需资源创建 *IaaSStory* 资源组。 若要创建这些资源，请完成以下步骤：

1. 导航到[模板页](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 在模板页中“**父资源组**”的右侧，单击“**部署到 Azure**”。
3. 如果需要，更改参数值，并按照 Azure 预览门户中的步骤部署资源组。

> [!IMPORTANT]
> 请确保存储帐户名称是唯一的。 不能在 Azure 中有重复的存储帐户名称。
> 

## <a name="understand-the-deployment-template"></a>了解部署模板
在部署随本文档提供的模板之前，请确保了解其用途。 通过以下步骤可大致了解该模板：

1. 导航到[模板页](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 单击 **azuredeploy.json** 以打开模板文件。
3. 请注意下面列出的 *osType* 参数。 此参数用于选择要用于数据库服务器的 VM 映像，以及多个与操作系统相关的设置。

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. 向下滚动到变量列表，并检查下面列出的 **dbVMSetting** 变量的定义。 它接收 **dbVMSettings** 变量中包含的数组元素之一。 如果熟悉软件开发术语，则可以将 **dbVMSettings** 变量视为哈希表或字典。

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. 假设决定要在后端部署运行 SQL 的 Windows VM。 那么 **osType** 的值将为 *Windows*，**dbVMSetting** 变量将包含下面列出的元素，它表示 **dbVMSettings** 变量中的第一个值。

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. 请注意，**vmSize** 包含值 *Standard_DS3*。 只有某些 VM 大小允许使用多个 NIC。 可以通过阅读文章 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)和 [Linux VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)来确定哪些 VM 大小支持多个 NIC。

7. 向下滚动到“**资源**”，并留意第一个元素。 它描述存储帐户。 此存储帐户用于维护每个数据库 VM 使用的数据磁盘。 在此方案中，每个数据库 VM 有一个存储在常规存储中的 OS 磁盘，以及两个存储在 SSD（高级）存储中的数据磁盘。

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. 向下滚动到下一个资源，如下所示。 此资源表示用于访问每个数据库 VM 中的数据库的 NIC。 请注意，在此资源中使用 **copy** 函数。 此模板允许根据需要部署任意多个 VM，这取决于 **dbCount** 参数。 因此，需要为数据库访问创建相同数量的 NIC，每个 VM 一个。

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. 向下滚动到下一个资源，如下所示。 此资源表示每个数据库 VM 中用于管理的 NIC。 同样，需要为每个数据库 VM 创建一个此类 NIC。 请注意链接 NSG 的 **networkSecurityGroup** 元素，它仅允许访问连接到此 NIC 的 RDP/SSH。

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. 向下滚动到下一个资源，如下所示。 此资源表示要由所有数据库 VM 共享的可用性集。 这样可保证，在维护期间集中始终有一个 VM 在运行。

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. 向下滚动到下一个资源。 此资源表示数据库 VM，如下面所列的前几行所示。 同样请注意使用 **copy** 函数，从而确保根据 **dbCount** 参数创建多个 VM。 另请注意 **dependsOn** 集合。 它列出了需要在部署 VM 之前创建的两个 NIC，以及可用性集和存储帐户。

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. 在 VM 资源中向下滚动到 **networkProfile** 元素，如下所示。 请注意，有两个 NIC 作为每个 VM 的引用。 为一个 VM 创建多个 NIC 时，必须将其一个 NIC 的 **primary** 属性设为 *true*，并将其余 NIC 的该属性设为 *false*。

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>通过使用单击部署来部署 ARM 模板

> [!IMPORTANT]
> 请确保先执行[先决条件](#Pre-requisites)步骤，再按照下面的说明操作。
> 

公共存储库中提供的示例模板采用包含用于生成上述方案的默认值的参数文件。 要使用单击部署来部署此模板，请打开[此链接](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)，在“**后端资源组(参见文档)**”右侧单击“**部署至 Azure**”，如有必要替换默认参数值，并按照门户中的说明进行操作。

下图显示在部署之后新资源组的内容。

![后端资源组](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 部署模板
要使用 PowerShell 部署下载的模板，请通过完成[安装和配置 PowerShell](/powershell/azure/overview) 一文中的步骤来安装并配置 PowerShell，并完成以下步骤：

运行 **`New-AzureRmResourceGroup`** cmdlet 以使用模板创建资源组。

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

预期输出：

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署模板
若要使用 Azure CLI 部署模板，请执行以下步骤。

1. 如果从未使用过 Azure CLI，请参阅[安装和配置 Azure CLI](../cli-install-nodejs.md)，并按照说明进行操作，直到选择 Azure 帐户和订阅。
2. 运行 **`azure config mode`** 命令切换到 Resource Manager 模式（如下所示）。

    ```azurecli
    azure config mode arm
    ```

    预期输出如下：

        info:    New mode is arm

3. 打开[参数文件](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json)，选择其内容，然后将其保存到计算机上的文件中。 对于本示例，我们将参数文件保存到 *parameters.json*。
4. 运行 **`azure group deployment create`** cmdlet，使用在前面下载并修改的模板和参数文件部署新 VNet。 在输出后显示的列表说明了所用的参数。

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    预期输出：
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

