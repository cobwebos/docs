---
title: 使用 Azure PowerShell 在 DevTest 实验室中创建虚拟机
description: 了解如何使用 Azure 开发人员测试实验室使用 Azure PowerShell 创建和管理虚拟机。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167107"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>使用 Azure PowerShell 使用开发人员测试实验室创建虚拟机
本文介绍如何使用 Azure PowerShell 在 Azure 开发人员测试实验室中创建虚拟机。 您可以使用 PowerShell 脚本在 Azure 开发人员测试实验室的实验室中自动创建虚拟机。 

## <a name="prerequisites"></a>先决条件
开始之前：

- 如果不想使用现有实验室来测试本文中的脚本或命令，[请创建一个实验室](devtest-lab-create-lab.md)。 
- [安装 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0)或使用集成到 Azure 门户中的 Azure 云外壳。 

## <a name="powershell-script"></a>PowerShell 脚本
本节中的示例脚本使用[Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) cmdlet。  此 cmdlet 获取实验室的资源 ID、要执行的操作的名称 （`createEnvironment`），以及执行该操作所需的参数。 参数位于包含所有虚拟机描述属性的哈希表中。 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

上述脚本中虚拟机的属性允许我们创建一个虚拟机，该虚拟机以 Windows Server 2016 数据中心为操作系统。 对于每种类型的虚拟机，这些属性将略有不同。 "[定义虚拟机](#define-virtual-machine)"部分演示如何确定在此脚本中使用哪些属性。

以下命令提供了运行保存在文件名中的脚本的示例：Create-LabVirtualMachine.ps1。 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>定义虚拟机
本节介绍如何获取特定于要创建的虚拟机类型的属性。 

### <a name="use-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建 VM 时，可以生成 Azure 资源管理器模板。 您无需完成创建 VM 的过程。 您只按照这些步骤操作，直到看到模板。 如果尚未创建实验室 VM，则这是获取必要 JSON 说明的最佳方式。 

1. 导航到[Azure 门户](https://portal.azure.com)。
2. 选择左侧导航菜单上**的所有服务**。
3. 从服务列表中搜索并选择**DevTest 实验室**。 
4. 在**DevTest 实验室**页面上，在实验室列表中选择您的实验室。
5. 在实验室的主页上，选择 **"添加"** 在工具栏上。 
6. 为 VM 选择**基本映像**。 
7. 选择 **"提交"** 按钮上方的页面底部的**自动化选项**。 
8. 您将看到用于创建虚拟机的**Azure 资源管理器模板**。 
9. **资源**部分中的 JSON 段具有您之前选择的图像类型的定义。 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

在此示例中，您将了解如何获取 Azure 市场位置图像的定义。 您可以以同样的方式获取自定义图像、公式或环境的定义。 添加虚拟机所需的任何工件，并设置所需的任何高级设置。 在选择 **"自动化"选项**按钮之前，为所需字段和任何可选字段提供值。

### <a name="use-azure-rest-api"></a>使用 Azure REST API
以下过程提供了使用 REST API 获取映像属性的步骤：这些步骤仅适用于实验室中的现有 VM。 

1. 导航到[虚拟机 - 列表](/rest/api/dtl/virtualmachines/list)页，选择 **"试用"** 按钮。 
2. 选择**Azure 订阅**。
3. 输入**实验室的资源组**。
4. 输入**实验室的名称**。 
5. 选择 **"运行**"。
6. 您将看到基于创建 VM 的图像**的属性**。 

## <a name="set-expiration-date"></a>设置到期日期
在培训、演示和试用等方案中，您可能希望创建虚拟机，并在固定持续时间后自动删除虚拟机，这样您就不会产生不必要的成本。 您可以使用 PowerShell[脚本示例部分](#powershell-script)所示，在使用 PowerShell 创建 VM 时设置到期日期。

下面是一个示例 PowerShell 脚本，该脚本设置实验室中所有现有 VM 的到期日期：

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>后续步骤
请参阅以下内容[：Azure 开发人员测试实验室的 Azure PowerShell 文档](/powershell/module/az.devtestlabs/)
