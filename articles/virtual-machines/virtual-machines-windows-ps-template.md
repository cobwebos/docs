---
title: "使用 Resource Manager 模板创建 VM | Microsoft Docs"
description: "将 Resource Manager 模板与 PowerShell 配合使用，以轻松创建新的 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: a094f6dccd93712c2effef60fdac0cea1a68bcb1


---
# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>使用资源管理器模板创建 Windows 虚拟机
本文介绍一个 Azure Resource Manager 模板，并说明如何使用 PowerShell 部署该模板。 此模板将在包含单个子网的新虚拟网络上部署运行 Windows Server 的单个虚拟机。

执行本文中的步骤大约需要 20 分钟时间。

> [!IMPORTANT]
> 如果希望将 VM 包含在某个可用性集中，可以在创建 VM 时将其添加到该集。 目前不支持在创建 VM 之后再将其添加到可用性集。
>
>

## <a name="step-1-create-the-template-file"></a>步骤 1：创建模板文件
可以使用[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)中的信息创建自己的模板。 也可以从 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)部署已创建的模板。

1. 打开最喜欢的文本编辑器并添加所需的架构元素和所需的 contentVersion 元素：

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  }
  ```

2. [参数](../azure-resource-manager/resource-group-authoring-templates.md#parameters)并不总是必需的，但在部署模板时，它们提供了输入值的一种方法。 在 ContentVersion 元素后面添加 parameters 元素及其子元素：

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUserName": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
  }
  ```

3. 可以在模板中使用[变量](../azure-resource-manager/resource-group-authoring-templates.md#variables)指定可能经常发生变化的值或需要通过组合参数值创建的值。 在 parameters 节的后面添加 variables 元素：

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUsername": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
    "variables": {
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
      "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
    },
  }
  ```

4. 接下来将在模板中定义[资源](../azure-resource-manager/resource-group-authoring-templates.md#resources)，例如虚拟机、虚拟网络和存储帐户。 在 variables 节的后面添加 resources 节：

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUsername": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
    "variables": {
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
      "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "mystorage1",
        "apiVersion": "2015-06-15",
        "location": "[resourceGroup().location]",
        "properties": { "accountType": "Standard_LRS" }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "myip1",
        "location": "[resourceGroup().location]",
        "properties": {
          "publicIPAllocationMethod": "Dynamic",
          "dnsSettings": { "domainNameLabel": "mydns1" }
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "myvn1",
        "location": "[resourceGroup().location]",
        "properties": {
          "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
          "subnets": [ {
            "name": "mysn1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          } ]
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "mync1",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "Microsoft.Network/publicIPAddresses/myip1",
          "Microsoft.Network/virtualNetworks/myvn1"
        ],
        "properties": {
          "ipConfigurations": [ {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
              },
              "subnet": { "id": "[variables('subnetRef')]" }
            }
          } ]
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "myvm1",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "Microsoft.Network/networkInterfaces/mync1",
          "Microsoft.Storage/storageAccounts/mystorage1"
        ],
        "properties": {
          "hardwareProfile": { "vmSize": "Standard_A1" },
          "osProfile": {
            "computerName": "myvm1",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "storageProfile": {
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
              "version" : "latest"
            },
            "osDisk": {
              "name": "myosdisk1",
              "vhd": {
                "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
              },
              "caching": "ReadWrite",
              "createOption": "FromImage"
            }
          },
          "networkProfile": {
            "networkInterfaces" : [ {
              "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
            } ]
          }
        }
      } ]
    }
  ```

  > [!NOTE]
  > 本文创建运行 Windows Server 操作系统版本的虚拟机。 若要详细了解如何选择其他映像，请参阅 [Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（使用 Windows PowerShell 和 Azure CLI 来导航和选择 Azure 虚拟机映像）。

5. 将模板文件保存为 *VirtualMachineTemplate.json*。

## <a name="step-2-create-the-parameters-file"></a>步骤 2：创建参数文件
若要为模板中定义的资源参数指定值，请创建一个参数文件，该文件包含部署模板时所使用的值。

1. 在文本编辑器中，将此 JSON 内容复制到名为 *Parameters.json* 的新文件中：

  ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
  ```

   > [!NOTE]
   > 查看更多关于 [用户名和密码要求](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)。
   >
   >
2. 保存参数文件。

## <a name="step-3-install-azure-powershell"></a>步骤 3：安装 Azure PowerShell
有关安装最新版 Azure PowerShell、选择订阅和登录到帐户的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="step-4-create-a-resource-group"></a>步骤 4：创建资源组
必须在[资源组](../azure-resource-manager/resource-group-overview.md)中部署所有资源。

1. 获取可以创建资源的可用位置列表。

  ```powershell
  Get-AzureRmLocation | sort DisplayName | Select DisplayName
  ```
2. 使用列表中的位置（例如 **Central US**）替换 **$locName** 的值。 创建变量。

  ```powershell
  $locName = "location name"
  ```

3. 使用新资源组的名称替换 **$rgName** 的值。 创建变量和资源组。

  ```powershell
  $rgName = "resource group name"
  New-AzureRmResourceGroup -Name $rgName -Location $locName
  ```

  用户应看到与此示例类似的内容：

  ```powershell
  ResourceGroupName : myrg1
  Location          : centralus
  ProvisioningState : Succeeded
  Tags              :
  ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
  ```

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>步骤 5：使用模板和参数创建资源
将 **$templateFile** 的值替换为模板文件的路径和名称。 将 **$parameterFile** 的值替换为参数文件的路径和名称。 创建变量，然后部署模板。

```powershell
$templateFile = "template file"
$parameterFile = "parameter file"
New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

你将看到类似于下面的内容：

```powershell
DeploymentName    : VirtualMachineTemplate
ResourceGroupName : myrg1
ProvisioningState : Succeeded
Timestamp         : 4/14/2016 8:11:37 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     mytestacct1
                    adminPassword    SecureString

Outputs           :
```

> [!NOTE]
> 也可以从 Azure 存储帐户部署模板和参数。 有关详细信息，请参阅[对 Azure 存储空间使用 Azure PowerShell](../storage/storage-powershell-guide-full.md)。
>
>

## <a name="next-steps"></a>后续步骤
* 如果部署出现问题，请查看[使用 Azure 门户对资源组部署进行故障排除](../resource-manager-troubleshoot-deployments-portal.md)
* 查看[使用 Azure Resource Manager 和 PowerShell 管理虚拟机](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，了解如何管理创建的虚拟机。




<!--HONumber=Dec16_HO2-->


