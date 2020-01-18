---
title: 使用 Azure PowerShell 在开发测试实验室中创建虚拟机
description: 了解如何使用 Azure 开发测试实验室，通过 Azure PowerShell 来创建和管理虚拟机。
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167107"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>使用 Azure PowerShell 创建包含开发测试实验室的虚拟机
本文介绍如何使用 Azure PowerShell 在 Azure 开发测试实验室中创建虚拟机。 可以使用 PowerShell 脚本在 Azure 开发测试实验室的实验室中自动创建虚拟机。 

## <a name="prerequisites"></a>必备组件
开始之前：

- 如果你不想使用现有实验室来测试本文中的脚本或命令，请[创建实验室](devtest-lab-create-lab.md)。 
- [安装 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0)或使用集成到 Azure 门户的 Azure Cloud Shell。 

## <a name="powershell-script"></a>PowerShell 脚本
本部分中的示例脚本使用[AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) cmdlet。  此 cmdlet 采用实验室的资源 ID、要执行的操作的名称（`createEnvironment`）以及执行该操作所需的参数。 参数位于包含所有虚拟机说明属性的哈希表中。 

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

使用上述脚本中虚拟机的属性，我们可以创建使用 Windows Server 2016 DataCenter 作为操作系统的虚拟机。 对于每种类型的虚拟机，这些属性将略有不同。 "[定义虚拟机](#define-virtual-machine)" 部分显示了如何确定要在此脚本中使用的属性。

下面的命令提供一个示例，用于运行保存在文件名： Create-LabVirtualMachine 中的脚本。 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>定义虚拟机
本部分演示如何获取特定于要创建的虚拟机类型的属性。 

### <a name="use-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建 VM 时，可以生成 Azure 资源管理器模板。 无需完成创建 VM 的过程。 只需要执行这些步骤，直到看到该模板。 如果尚未创建实验室 VM，则这是获取所需 JSON 说明的最佳方式。 

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 选择左侧导航菜单中的 "**所有服务**"。
3. 搜索并从服务列表中选择 "**开发测试实验室**"。 
4. 在 "**开发测试实验室**" 页的实验室列表中，选择实验室。
5. 在实验室的主页上，选择工具栏上的 " **+ 添加**"。 
6. 为 VM 选择**基本映像**。 
7. 选择 "**提交**" 按钮上方页面底部的 "**自动化选项**"。 
8. 你会看到用于创建虚拟机的**Azure 资源管理器模板**。 
9. **Resources**节中的 JSON 段包含之前所选图像类型的定义。 

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

在此示例中，你将了解如何获取 Azure 市场位置映像的定义。 可以采用相同的方式获取自定义图像、公式或环境的定义。 添加虚拟机所需的任何项目，并设置所需的任何高级设置。 在为必填字段和任何可选字段提供值后，在选择 "**自动化选项**" 按钮之前。

### <a name="use-azure-rest-api"></a>使用 Azure REST API
以下过程提供了使用 REST API 获取映像属性的步骤：这些步骤仅适用于实验室中的现有 VM。 

1. 导航到 "[虚拟机-列表](/rest/api/dtl/virtualmachines/list)" 页，选择 "**试用**" 按钮。 
2. 选择 **Azure 订阅**。
3. 输入**实验室的资源组**。
4. 输入**实验室的名称**。 
5. 选择“运行”。
6. 此时会显示基于创建 VM 的**映像的属性**。 

## <a name="set-expiration-date"></a>设置到期日期
在定型、演示和试验等方案中，您可能需要创建虚拟机并在固定的持续时间后自动将其删除，以免产生不必要的成本。 使用 PowerShell 创建 VM 时，可以为其设置过期日期，如示例[PowerShell 脚本](#powershell-script)部分所示。

下面是一个示例 PowerShell 脚本，用于为实验室中的所有现有 Vm 设置过期日期：

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
请参阅以下内容： [Azure 开发测试实验室 Azure PowerShell 文档](/powershell/module/az.devtestlabs/)
