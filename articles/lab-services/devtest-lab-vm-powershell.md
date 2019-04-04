---
title: 在使用 Azure PowerShell 的开发测试实验室中创建虚拟机 |Microsoft Docs
description: 了解如何使用 Azure 开发测试实验室创建和使用 Azure PowerShell 管理虚拟机。
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: df6d7e59c6c569a56162a738924b4ffd05b47da6
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896272"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>使用开发测试实验室使用 Azure PowerShell 创建虚拟机
本文介绍如何使用 Azure PowerShell 在 Azure 开发测试实验室中创建虚拟机。 PowerShell 脚本可用于自动创建的 Azure 开发测试实验室中的实验室中的虚拟机。 

## <a name="prerequisites"></a>必备组件
开始之前：

- [创建实验室](devtest-lab-create-lab.md)如果不想要使用现有实验室来测试这篇文章中的脚本或命令。 
- [安装 Azure PowerShell](/powershell/azure/azurerm/other-install)或使用已集成到 Azure 门户的 Azure Cloud Shell。 

## <a name="powershell-script"></a>PowerShell 脚本
本部分中的示例脚本使用[Invoke-azurermresourceaction](/powershell/module/azurerm.resources/invoke-azurermresourceaction) cmdlet。  此 cmdlet 使用实验室的资源 ID，要执行的操作的名称 (`createEnvironment`)，和必需的参数执行该操作。 参数是包含所有虚拟机描述属性的哈希表中。 

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
        $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzureRmResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
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
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzureRmResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

上述脚本中的虚拟机的属性使我们能够创建具有作为操作系统的 Windows Server 2016 DataCenter 的虚拟机。 对于每个类型的虚拟机，这些属性会略有不同。 [定义虚拟机](#define-virtual-machine)部分演示如何确定要在此脚本中使用的属性。

下面的命令提供了运行中的文件名称保存该脚本的示例：创建-LabVirtualMachine.ps1。 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>定义虚拟机
本部分演示如何获取特定于你想要创建的虚拟机的类型的属性。 

### <a name="use-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建 VM 时，你可以生成的 Azure 资源管理器模板。 不需要完成创建 VM 的过程。 直到您看到该模板，仅按照的步骤。 这是最佳的方法以获取必要的 JSON 描述，如果还没有的实验室创建 VM。 

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 选择**所有服务**在左侧导航菜单中。
3. 搜索并选择**开发测试实验室**从服务列表。 
4. 上**开发测试实验室**页上，在实验室列表中选择你的实验室。
5. 在你的实验室主页上，选择 **+ 添加**工具栏上。 
6. 选择**基本映像**vm。 
7. 选择**自动化选项**在上述页面的底部**提交**按钮。 
8. 您看到**Azure 资源管理器模板**用于创建虚拟机。 
9. 中的 JSON 段**资源**部分包含先前选择的映像类型的定义。 

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

在此示例中，您将了解如何获取 Azure Market Place 图像的定义。 可以在相同的方式获取自定义映像、 公式或环境的定义。 添加所需的虚拟机，任何项目并设置任何所需的高级的设置。 为必填的字段和任何可选字段提供值之后, 再选择**自动化选项**按钮。

### <a name="use-azure-rest-api"></a>使用 Azure REST API
以下过程提供使用 REST API 获取映像的属性的步骤：这些步骤仅适用于在实验室中的现有 VM。 

1. 导航到[虚拟机-列出](/rest/api/dtl/virtualmachines/list)页上，选择**试试**按钮。 
2. 选择 **Azure 订阅**。
3. 输入**实验室的资源组**。
4. 输入**名称的实验室**。 
5. 选择“运行”。
6. 您看到**映像属性**基于创建的 VM。 



## <a name="next-steps"></a>后续步骤
请参阅以下内容：[有关 Azure 开发测试实验室的 azure PowerShell 文档](/powershell/module/az.devtestlabs/)