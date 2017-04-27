---
title: "在 Azure 虚拟机规模集上部署应用 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 模板在虚拟机规模集上部署简单的自动缩放应用程序。"
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: getting-started
ms.date: 4/4/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 8a903cd870f01f9ca6224efd1386b68c63e3aa98
ms.lasthandoff: 04/06/2017


---

# <a name="deploy-an-autoscaling-app-using-a-template"></a>使用模板部署自动缩放应用

[Azure Resource Manager 模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)是部署成组的相关资源的好办法。 本教程基于[部署简单规模集](virtual-machine-scale-sets-mvss-start.md)，介绍如何使用 Azure Resource Manager 模板在规模集上部署简单的自动缩放应用程序。  还可以使用 PowerShell、CLI 或门户设置自动缩放。 有关详细信息，请参阅[自动缩放概述](virtual-machine-scale-sets-autoscale-overview.md)。

## <a name="two-quickstart-templates"></a>两个快速入门模板
部署规模集时，可以使用 [VM 扩展](../virtual-machines/virtual-machines-windows-extensions-features.md)在平台映像中安装新软件。 VM 扩展是小型应用程序，可在Azure 虚拟机上提供部署后配置和自动化任务，例如部署应用。 [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) 中提供两个不同的示例模板，介绍了如何使用 VM 扩展将自动缩放应用程序部署到规模集上。

### <a name="python-http-server-on-linux"></a>Linux 上的 Python HTTP 服务器
[Linux 上的 Python HTTP 服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)示例模板部署一个在 Linux 规模集上运行的简单自动缩放应用程序。  使用自定义脚本 VM 扩展在规模集的每个 VM 上部署 Python Web 框架 [Bottle](http://bottlepy.org/docs/dev/) 和一个简单的 HTTP 服务器。 此规模集在所有 VM 的平均 CPU 使用率高于 60% 时增加，在平均 CPU 使用率低于 30% 时减少。

除规模集资源外，azuredeploy.json 示例模板还声明虚拟网络、公共 IP 地址、负载均衡器和自动缩放设置资源。  有关在模板中创建这些资源的详细信息，请参阅[具有自动缩放功能的 Linux 规模集](virtual-machine-scale-sets-linux-autoscale.md)。

在 azuredeploy.json 模板中，`Microsoft.Compute/virtualMachineScaleSets` 资源的 `extensionProfile` 属性指定自定义脚本扩展。 `fileUris` 指定脚本位置。 在这种情况下，存在两个文件：workserver.py，用于指定一个简单的 HTTP 服务器；installserver.sh，用于安装 Bottle 并启动 HTTP 服务器。 部署规模集后，`commandToExecute` 指定要运行的命令。

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Windows 上的 ASP.NET MVC 应用程序
[Windows 上的 ASP.NET MVC 应用程序](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)示例模板部署一个在 Windows 规模集的 IIS 中运行的简单 ASP.NET MVC 应用。  使用 [PowerShell 所需状态配置 (DSC)](virtual-machine-scale-sets-dsc.md) VM 扩展部署 IIS 和 MVC 应用。  CPU 使用率高于 50% 持续 5 分钟时，（VM 上的）规模集增加。 

除规模集资源外，azuredeploy.json 示例模板还声明虚拟网络、公共 IP 地址、负载均衡器和自动缩放设置资源。 此外，该模板还演示了应用程序升级。  有关在模板中创建这些资源的详细信息，请参阅[具有自动缩放功能的 Windows 规模集](virtual-machine-scale-sets-windows-autoscale.md)。

在 azuredeploy.json 模板中，`Microsoft.Compute/virtualMachineScaleSets` 资源的 `extensionProfile` 属性指定[所需状态配置 (DSC)](virtual-machine-scale-sets-dsc.md) 扩展，此扩展从 WebDeploy 包安装 IIS 和默认 Web 应用。  IISInstall.ps1 脚本在虚拟机上安装 IIS，该脚本位于 DSC 文件夹中。  可在 WebDeploy 文件夹中找到 MVC Web 应用。  安装脚本和 Web 应用的路径定义在 azuredeploy.parameters.json 文件的 `powershelldscZip` 和 `webDeployPackage` 中。 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>部署模板
部署 [Linux 上的 Python HTTP 服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)或 [Windows 上的 ASP.NET MVC 应用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)模板最简单的方式是使用 GitHub 的自述文件中的“部署到 Azure”按钮。  也可使用 PowerShell 或 Azure CLI 部署示例模板。

### <a name="powershell"></a>PowerShell
将 [Linux 上的 Python HTTP 服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)或 [Windows 上的 ASP.NET MVC 应用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)文件从 GitHub 存储库复制到本地计算机上的文件夹。  打开 azuredeploy.parameters.json 文件并更新 `vmssName`、`adminUsername` 和 `adminPassword` 参数的默认值。 将以下 PowerShell 脚本保存到示例文件夹中的 deploy.ps1 作为 azuredeploy.json 模板。 若要部署示例模板，请从 PowerShell 命令窗口运行 deploy.ps1 脚本。

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "azuredeploy.json",

 [string]
 $parametersFilePath = "azuredeploy.parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.resources");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
