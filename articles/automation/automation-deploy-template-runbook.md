---
title: 在 Azure 自动化 PowerShell Runbook 中部署 Azure 资源管理器模板
description: 本文介绍如何从 PowerShell runbook 部署 azure 存储中存储的 Azure 资源管理器模板。
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
keywords: powershell, runbook, json, azure 自动化
ms.openlocfilehash: 18f1d4ced2a80f9adb5da2c209987fc1997a3f22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304145"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>在 PowerShell Runbook 中部署 Azure 资源管理器模板

可以编写一个 [Azure 自动化 PowerShell Runbook](./learn/automation-tutorial-runbook-textual-powershell.md)，用于通过 [Azure 资源管理模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)部署 Azure 资源。 模板允许使用 Azure 自动化来自动部署 Azure 资源。 可以在一个安全的中心位置（例如 Azure 存储）维护资源管理器模板。

本文创建一个 PowerShell Runbook，该 Runbook 使用 [Azure 存储](../storage/common/storage-introduction.md)中存储的资源管理器模板部署新的 Azure 存储帐户。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，则可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或[注册免费帐户](https://azure.microsoft.com/free/)。
* [自动化帐户](./manage-runas-account.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* 要在其中存储资源管理器模板的[Azure 存储帐户](../storage/common/storage-account-create.md)。
* 安装在本地计算机上的 Azure PowerShell。 若要详细了解如何获得 Azure PowerShell，请参阅[安装 Azure Powershell 模块](/powershell/azure/install-az-ps)。

## <a name="create-the-resource-manager-template"></a>创建 资源管理器模板

在此示例中，我们使用一个部署新的 Azure 存储帐户的资源管理器模板。

在文本编辑器中复制以下文本：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

将该文件在本地保存为 TemplateTest.json。

## <a name="save-the-resource-manager-template-in-azure-storage"></a>在 Azure 存储中保存资源管理器模板

现在，我们使用 PowerShell 创建 Azure 存储文件共享并上传 TemplateTest.json 文件。 有关如何在 Azure 门户中创建文件共享和上传文件的说明，请参阅[在 Windows 上开始使用 Azure 文件存储](../storage/files/storage-dotnet-how-to-use-files.md)。

在本地计算机上启动 PowerShell，运行以下命令创建文件共享并将资源管理器模板上传到该文件共享。

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>创建 PowerShell Runbook 脚本

现在，我们创建一个 PowerShell 脚本，用于从 Azure 存储获取文件的 **TemplateTest.js** ，并部署模板来创建新的 azure 存储帐户。

在文本编辑器中粘贴以下文本：

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
```

将该文件在本地保存为 DeployTemplate.ps1。

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>在 Azure 自动化帐户中导入并发布 Runbook

现在，我们使用 PowerShell 将 Runbook 导入 Azure 自动化帐户，并发布该 Runbook。 有关如何在 Azure 门户中导入和发布 Runbook 的信息，请参阅[在 Azure 自动化中管理 Runbook](manage-runbooks.md)。

若要将 DeployTemplate.ps1 以 PowerShell Runbook 的形式导入自动化帐户，请运行以下 PowerShell 命令：

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>启动 Runbook

现在，通过调用 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 来启动该 Runbook。 有关如何在 Azure 门户中启动 Runbook 的信息，请参阅[在 Azure 自动化中启动 Runbook](./start-runbooks.md)。

在 PowerShell 控制台中运行以下命令：

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Runbook 运行后，可以通过检索作业对象的属性值来检查其状态 `$job.Status` 。

Runbook 会获取资源管理器模板，并使用它来部署新的 Azure 存储帐户。 可以通过运行以下命令来查看已创建的新存储帐户：

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>后续步骤

* 若要详细了解资源管理器模板，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)。
* 若要开始使用 Azure 存储，请参阅 [Azure 存储简介](../storage/common/storage-introduction.md)。
* 若要查找其他有用的 Azure 自动化 Runbook，请参阅[在 Azure 自动化中使用 Runbook 和模块](automation-runbook-gallery.md)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation#automation)。
